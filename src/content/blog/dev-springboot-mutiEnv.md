---
title: 'Springboot多环境配置'
date: "2022-10-15"
description: '在开发时，不同人员的本地配置文件不同，项目开发、测试、正式部署的配置文件也不同，利用Springboot多环境配置可以在多个配置文件间切换'
tags: ["开发经验", "Springboot", "多环境配置"] 
---

## SpringBoot与maven多环境配置：面向实战的快速上手

## 一、多环境的配置介绍

通常应用系统可能在**开发环境(dev)、测试环境(test)、生产环境(prod)**中运行，那么如何做到多个运行环境配置灵活、快速切换呢? SpringBoot提供了极简的解决方案，只需要简单的配置，应用系统就能灵活切换运行环境配置。

### 关于多环境配置文件

创建多环境配置文件时，需要遵循Spring Boot允许的命名约定来命名，格式为application-{profile}.properties（如：application-prod.properties），其中{profile}为对应的环境标识。在项目resources目录下分别创建application-dev.properties、application-test.properties和application-prod.properties三个配置文件，对应开发环境、测试环境和生产环境，如下图所示：



![img](https://s6.51cto.com/oss/202202/16/548580413e7c3bf527d0720c1135c05b44abac.webp)



根据应用系统中常见的三个运行环境拆分成了多个不同的配置文件，分别独立配置上面各运行环境的配置项。具体如下所示：

- application.properties为项目主配置文件，包含项目所需的所有公共配置。
- application-dev.properties为开发环境配置文件，包含项目所需的单独配置。
- application-test.properties为测试环境配置文件。
- application-prod.properties为生产环境配置文件。



## 二、实际操作

### 准备配置文件

- 准备四个配置文件，分别是application.yml、application-dev.yml、application-test.yml、application-prod.yml。



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/24/1710d31b4c1f7673~tplv-t2oaga2asx-zoom-in-crop-mark:4536:0:0:0.awebp)



- application.yml作为主配置文件，里面定义了需要激活的环境，如下：

```yml
spring:
  profiles:
    active: @profile.active@
```

- application-*.yml这三个配置文件就是各自环境的配置，比如application-dev.yml（开发环境）文件的配置如下：

```yaml
server:
  port: 8080
  servlet:
    context-path: /api-front
spring:
  redis:
    database: 2
    host: 120.26.101.203
    password: live4dream
    jedis:
      pool:
        max-active: 8
        min-idle: 0
        maxActive: 10
```

### POM文件配置

- 要和Maven整合当然少不了pom文件的配置，maven中也提供了profile的支持，每个profile能够定义自己的一些依赖和配置和激活条件，profile配置如下：

```xml
<!--定义三种开发环境-->
    <profiles>
        <profile>
            <!--不同环境的唯一id-->
            <id>dev</id>
            <activation>
                <!--默认激活开发环境-->
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <!--profile.active对应application.yml中的@profile.active@-->
                <profile.active>dev</profile.active>
            </properties>
        </profile>

        <!--测试环境-->
        <profile>
            <id>test</id>
            <properties>
                <profile.active>test</profile.active>
            </properties>
        </profile>

        <!--生产环境-->
        <profile>
            <id>prod</id>
            <properties>
                <profile.active>prod</profile.active>
            </properties>
        </profile>
    </profiles>
```

- （可选）maven中的profile的激活条件还可以根据jdk、操作系统、文件存在或者缺失来激活。如下：

```xml
<!--activation用来指定激活方式，可以根据jdk环境，环境变量，文件的存在或缺失-->
            <activation>
                <!--配置默认激活-->
                <activeByDefault>true</activeByDefault>
                
                <!--通过jdk版本-->
                <!--当jdk环境版本为1.8时，此profile被激活-->
                <jdk>1.8</jdk>
                <!--当jdk环境版本1.8或以上时，此profile被激活-->
                <jdk>[1.8,)</jdk>

                <!--根据当前操作系统-->
                <os>
                    <name>Windows XP</name>
                    <family>Windows</family>
                    <arch>x86</arch>
                    <version>5.1.2600</version>
                </os>
            </activation>
        </profile>
```

### 资源过滤

- 在进行生产环境打包的时候并不需要开发和测试环境的一些文件，这里我们可以进行资源的过滤，保留需要的配置和文件，同样是在pom文件中定义，如下：

```xml
	<build>
        <resources>
            <!--排除配置文件-->
            <resource>
                <directory>src/main/resources</directory>
                <!--先排除所有的配置文件-->
                <excludes>
                    <!--使用通配符，当然可以定义多个exclude标签进行排除-->
                    <exclude>application*.yml</exclude>
                </excludes>
            </resource>

            <!--根据激活条件引入打包所需的配置和文件-->
            <resource>
                <directory>src/main/resources</directory>
                <!--引入所需环境的配置文件-->
                <filtering>true</filtering>
                <includes>
                    <include>application.yml</include>
                    <!--根据maven选择环境导入配置文件-->
                    <include>application-${profile.active}.yml</include>
                </includes>
            </resource>
        </resources>
    </build>
```

上述配置主要分为两个方面，第一是先排除所有配置文件，第二是根据`profile.active`动态的引入配置文件。

### IDEA切换

- 上面的配置全部完成的话，就可以在DIEA中正常使用了，如下图：



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/24/1710d31b4c5b9c67~tplv-t2oaga2asx-zoom-in-crop-mark:4536:0:0:0.awebp)



- 点击lifecycle中的package即可正常打包，相当于一下命令

```css
mvn clean install package -P dev
```

## 总结

- SpringBoot和Maven的多环境配置需要以下步骤：
  - 准备多环境的配置文件
  - POM文件中配置profile
  - POM文件中配置资源过滤
  - IDEA中自由切换