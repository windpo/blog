---
title: 'Kubernetes学习(一)'
date: "2024-1-3"
description: 'k8s基本介绍&&基本配置'
tags: ["学习笔记", "k8s"] 
---

## 一、基础知识

### 1、基本概念

参照：https://kuboard.cn/learning/k8s-bg/what-is-k8s.html，博客跳过此部分。

注意：Kubernetes不部署源码、不编译或构建应用程序。持续集成、分发、部署（CI/CD）的工作流极大程度上取决于组织的偏好以及技术要求。Kubernetes可以作为部署平台参与到 CI/CD 流程，但是**不涉及镜像构建和分发**的过程，可选的相关技术栈有 Jenkins / Gitlab Runner / docker registry / harbor 等。



根据架构图快速了解k8s：

![Kubernetes 组件](https://kubernetes.io/images/docs/kubernetes-cluster-architecture.svg)

**左侧controlPlane:**

主要包含一些用于控制容器的内容：操作容器的api接口、配置文件、控制器管理等

**右侧Node集群：**

- **Node节点**：一台物理主机/虚拟机
- **Pod**：一个Pod一般为一个项目（Kubernetes中最小的调度单元，它可以包含一个或多个容器。容器间可以共享数据和相互通信）
- **Pod内的容器**：一个项目中的不同功能（一个应用程序的不同组件，它们协同工作来完成特定的任务）
- **负载均衡**：将流量引导到一组具有相同标签的Pod，多个Pod实例可以处理流量，分摊压力。（通过Service实现）
- Kubelet、kube-proxy：管理Pods、完成通信相关内容


### 2、k8s组件

![Kubernetes 的组件](https://kubernetes.io/images/docs/components-of-kubernetes.svg)

### Master组件（Control Plane）

Master组件是集群的**控制平台**（control plane）：

- master 组件负责**集群中的全局决策**（例如，调度）
- master 组件**探测并响应集群事件**（例如，当 Deployment 的实际 Pod 副本数未达到 `replicas` 字段的规定时，启动一个新的 Pod）

Master组件可以运行于集群中的任何机器上。但是，为了简洁性，通常在**同一台机器上运行所有的 master 组件，且不在此机器上运行用户的容器**。参考 [安装Kubernetes高可用](https://kuboard.cn/install/install-kubernetes.html)。

#### *kube-apiserver*

该组件**提供 Kubernetes  API, 以命令行形式操作k8s**。kubectl /  kubernetes dashboard / kuboard 等Kubernetes管理工具就是通过 kubernetes API 实现对  Kubernetes 集群的管理。

#### *etcd*

**存储Kubernetes集群的所有配置信息**，支持一致性和高可用的名值对存储组件。请确保 [备份](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#backing-up-an-etcd-cluster) 了 etcd 的数据。关于 etcd 的更多信息，可参考 [etcd 官方文档](https://etcd.io/docs/)[ ](https://etcd.io/docs/)

#### *kube-scheduler*

该组件**监控所有新创建尚未分配到节点上的**[Pod](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/)，并且**自动选择为 Pod 选择一个合适的节点去运行**。调度决策考虑的因素包括单个 Pod 及 Pods 集合的资源需求、软硬件及策略约束、 亲和性及反亲和性规范、数据位置、工作负载间的干扰及最后时限。

#### *kube-controller-manager*

该组件运行了所有的[控制器](https://kubernetes.io/zh-cn/docs/concepts/architecture/controller/)（**通过API监控集群状态并将当前状态转化为期望状态**）

逻辑上来说，每一个控制器是一个独立的进程，k8s为降低复杂度，将这些控制器合并运行在一个进程里。

- 有许多不同类型的控制器。以下是一些例子：
  - **节点控制器（Node Controller）**：负责在节点出现故障时进行通知和响应
  - **任务控制器（Job Controller）**：监测代表一次性任务的 Job 对象，然后创建 Pods 来运行这些任务直至完成
  - **端点分片控制器（EndpointSlice controller）**：填充端点分片（EndpointSlice）对象（以提供 Service 和 Pod 之间的链接）。
  - **服务账号控制器（ServiceAccount controller）**：为新的命名空间创建默认的服务账号（ServiceAccount）。

#### *cloud-controller-manager*

一个 Kubernetes [控制平面](https://kubernetes.io/zh-cn/docs/reference/glossary/?all=true#term-control-plane)组件， **嵌入了特定于云平台的控制逻辑**。 [云控制器管理器（Cloud Controller Manager）](https://kubernetes.io/zh-cn/docs/concepts/architecture/cloud-controller/) 允许你将你的集群连接到云提供商的 API 之上， 并将与该云平台交互的组件同与你的集群交互的组件分离开来。

`cloud-controller-manager` 仅运行特定于云平台的控制器。 如果你在自己的环境中运行 Kubernetes，或者在本地计算机中运行学习环境， 所部署的集群不需要有云控制器管理器。

下面的控制器都包含对云平台驱动的依赖：

- 节点控制器（Node Controller）：用于在节点终止响应后检查云提供商以确定节点是否已被删除
- 路由控制器（Route Controller）：用于在底层云基础架构中设置路由
- 服务控制器（Service Controller）：用于创建、更新和删除云提供商负载均衡器



### Node组件

节点组件会在每个节点上运行，负责**维护运行的 Pod 并提供 Kubernetes 运行环境**。

#### *kublet*

kubelet 在集群中每个[节点（node）](https://kubernetes.io/zh-cn/docs/concepts/architecture/nodes/)上运行。 它保证[容器（containers）](https://kubernetes.io/zh-cn/docs/concepts/overview/what-is-kubernetes/#why-containers)都运行在 [Pod](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/) 中。

[kubelet](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/) 接收一组通过各类机制提供给它的 PodSpec，确保这些 PodSpec 中描述的容器处于运行状态且健康。 kubelet 不会管理不是由 Kubernetes 创建的容器。

#### *kube-proxy*

[kube-proxy](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/kube-proxy/) 是集群中每个[节点（node）](https://kubernetes.io/zh-cn/docs/concepts/architecture/nodes/)上所运行的网络代理， 实现 Kubernetes [服务（Service）](https://kubernetes.io/zh-cn/docs/concepts/services-networking/service/) 概念的一部分。

kube-proxy **维护节点上的一些网络规则**， 这些网络规则会允许从集群内部或外部的网络会话与 Pod 进行网络通信。

#### *Container Runtime*

它负责**管理 Kubernetes 环境中容器的执行和生命周期**，这个基础组件使 Kubernetes 能够有效运行容器。 

Kubernetes 支持的容器运行环境例如： [containerd](https://containerd.io/docs/)、 [CRI-O](https://cri-o.io/#what-is-cri-o) 以及基于 [Kubernetes CRI (容器运行环境接口)](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-node/container-runtime-interface.md) 的其他任何实现。



### Addons（插件）

插件使用 Kubernetes 资源（[DaemonSet](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/daemonset/)、 [Deployment](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/deployment/) 等）**实现集群功能**。 因为**这些插件提供集群级别的功能**，插件中命名空间域的资源属于 `kube-system` 命名空间。下面描述众多插件中的几种。有关可用插件的完整列表，请参见 [插件（Addons）](https://kubernetes.io/zh-cn/docs/concepts/cluster-administration/addons/)。

#### *DNS*

集群 DNS 是一个 DNS 服务器，和环境中的其他 DNS 服务器一起工作，它为 Kubernetes 服务提供 DNS 记录。

Kubernetes 启动的容器自动将此 DNS 服务器包含在其 DNS 搜索列表中。

#### *Web 界面（仪表盘）*

[Dashboard](https://kubernetes.io/zh-cn/docs/tasks/access-application-cluster/web-ui-dashboard/) 是 Kubernetes 集群的通用的、基于 Web 的用户界面。 它使用户可以管理集群中运行的应用程序以及集群本身， 并进行故障排除。

#### *容器资源监控*

[容器资源监控](https://kubernetes.io/zh-cn/docs/tasks/debug/debug-cluster/resource-usage-monitoring/) 将关于容器的一些常见的时间序列度量值保存到一个集中的数据库中， 并提供浏览这些数据的界面。

#### *集群层面日志*

[集群层面日志](https://kubernetes.io/zh-cn/docs/concepts/cluster-administration/logging/)机制负责将容器的日志数据保存到一个集中的日志存储中， 这种集中日志存储提供搜索和浏览接口。

#### *网络插件*

[网络插件](https://kubernetes.io/zh-cn/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins) 是实现容器网络接口（CNI）规范的软件组件。它们负责为 Pod 分配 IP 地址，并使这些 Pod 能在集群内部相互通信。



## 二、快速入门

纯概念太复杂？如果上面的概念有些没看懂，那也没关系，让我们先把配置配好，然后下篇博客中将以一个实际的例子来理解这些概念，有遇到不会的概念去[官网文档](https://kubernetes.io/zh-cn/docs/concepts/)参考一下。

### 1、基本工具的安装

对于在生产环境中使用k8s：可以参考官方文档：[在生产环境中安装k8s](https://kubernetes.io/zh-cn/docs/setup/production-environment/)

对于学习使用，需要准备的工具如下：

#### **minikube**

 `minikube`能让你在本地运行 Kubernetes。 minikube在你的个人计算机上运行一个一体化（all-in-one） 或多节点的**本地 Kubernetes 集群**，以便你来**尝试 Kubernetes** 。快速安装请参考[minikube快速安装文档](https://minikube.sigs.k8s.io/docs/start/)（**支持Windows、macOS、Linux**） ，更多信息可以查看其Github仓库[minikube](https://github.com/kubernetes/minikube)

**安装的前提条件：**

- 至少两个CPU
- 至少2G内存
- 至少20GB的磁盘空间
- 容器或虚拟机管理器（[Docker](https://minikube.sigs.k8s.io/docs/drivers/docker/)、[VirtualBox](https://minikube.sigs.k8s.io/docs/drivers/virtualbox/)、 [VMware Fusion](https://minikube.sigs.k8s.io/docs/drivers/vmware/)，该博客采用Docker容器管理器）

**使用minikube：**

启动minikube

```shell
minikube start
```

启动minikube面板

``` shell
minikube dashbord
```

从minikube start的内容中可以看到对应k8s的版本（以此选择kubectl的版本）

![screenshot](https://raw.githubusercontent.com/kubernetes/minikube/master/site/static/images/screenshot.png)



#### **kubectl**

`kubectl`是Kubernetes的**命令行运行工具**，让你可以对 Kubernetes 集群运行命令。 你可以使用 kubectl 来部署应用、监测和管理集群资源以及查看日志。

kubectl支持多个平台，你可以根据你的需要来安装（该博客采用Linux系统）

Linux: [在 Linux 上安装 kubectl](https://kubernetes.io/zh-cn/docs/tasks/tools/install-kubectl-linux)

Windows: [在 Windows 上安装 kubectl](https://kubernetes.io/zh-cn/docs/tasks/tools/install-kubectl-windows)

macOS: [在 macOS 上安装 kubectl](https://kubernetes.io/zh-cn/docs/tasks/tools/install-kubectl-macos)

**注意！**：**kubectl 版本和集群版本之间的差异必须在一个小版本号内**。 例如：**v1.29 版本**的客户端能与 **v1.28、 v1.29 和 v1.30 版本**的控制面通信。 用最新兼容版的 kubectl 有助于避免不可预见的问题。



验证kubectl安装成功

```shell
kubectl version --client
```

kubectl发现并访问kubernetes集群，需要一个**kubeconfig**文件，该文件在 [kube-up.sh](https://github.com/kubernetes/kubernetes/blob/master/cluster/kube-up.sh) 创建集群时，或成功部署一个 **Minikube 集群**时，均会**自动生成**。通常kubectl 的配置信息存放于文件 `~/.kube/config` 中。

验证kubectl配置成功（遇到错误查看kubectl上面的安装文档]）

```shell
kubectl cluster-info
```

查看kubectl和k8s分别的版本

```shell
kubectl verison
```







## 参考文档：

[K8s官方文档](https://kubernetes.io)

[kuboard教程](https://kuboard.cn/)
