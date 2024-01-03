---
title: 'JAVASE 多态、异常、集合框架'
date: "2022-3-17"
description: ''
tags: ["学习笔记", "JAVASE基础"] 
---


# java中的多态、异常、以及集合框架



## 多态



#### 先让我们来简单说一下多态



多态是面向对象中很重要的特性。它易于理解，但有时候也会带来一些困惑

**多态，就是同一个行为具有多个不同的表现形式或形态。**

![](https://www.runoob.com/wp-content/uploads/2013/12/java-polymorphism-111.png)



#### 举两个例子：

1. 一个学生(Student)对象，即是学生(Student)，也是人(People)

   ```java
   public class People{
      	......
   }
   ——————————————————————————
   public class Student extends People{
       ......
   }
   ——————————————————————————
   Student s1 = new Student();
   People p1 = new Student();
   ```

2. 一个学生对象，即可以吃饭，也可以吃面

   ```java
   public class People{
       public void eat(){
           System.out.println("吃饭");
       }
      	......
   }
   ——————————————————————————
   public class Student extends People{
       @Override
       public void eat(){
           System.out.println("吃面");
       }
       ......
   }
   ——————————————————————————
   People p2 = new People();
   People p3 = new Student();
   p2.eat();
   p3.eat();
   //输出结果为：
   //吃饭
   //吃面
   ```

如果能够理解这两个例子，那么就已经基本理解了多态

在第一个例子中，由于 Student 是继承自 People 的，从我们根据现实中的思维来想，那么 Student 的对象理应也能赋给 People 的变量。这就是所谓的**向上转型**。而如果反过来，将 People 类型的对象赋值给 Student 类型的变量，就是**向下转型**。

在第二个例子中，两个对象虽然都被赋给了 People 类型的变量，但 p3 变量中的对象实际是 Student ，因此调用 eat() 方法，实际上调用的是 Student 类的 eat()

这也就是所说的**动态绑定**。

#### 结合两个例子来看，我们可以想象一下这样的场景：

```java
public class People{
   	......
}
——————————————————————————
public class Student extends People{
    ......
    public void attendClass(){
        System.out.println("上课咯，happy");
    }
}
——————————————————————————
People p4 = new Student();
```

Student 类中有一个 People 类中没有的方法 attendClass()，但是出于某种不可抗因素，我们只能用 People 类型的变量 p4 来接收一个 Student 对象。此时，如果我们想通过 p4.attendClass() 来调用这个方法，显然是做不到的。因为 p4 虽然实际类型是 Student ，但是它形式上的类型却是 People ，其中没有 attendClass() 方法，因此我们需要使用**向下转型**

```java
((Student)p4).attendClass()
//向下转型需要强转，而向上转型则是自动的
```

## 接口

接口是什么？

在java中，接口也可以理解为是类之间约定的规范。接口不是类，而是对类的一组需求的描述，这些类都要遵从统一格式的定义。

**我们可以简单的把接口理解做是一系列方法的声明，实现它的类一定要实现这个接口包含的方法。**

让我们来看看下面的一个例子：

```java
public interface GameInterface {
    void startPlay();
    void endPlay();
}
```

```java
public class Csgo implements GameInterface { //通过implements实现接口，需要重写接口的方法
    @Override
    public void startPlay() {
        System.out.println("原神启动！");
    }

    @Override
    public void endPlay() {
        System.out.println("肝完下班！");
    }
}
```

从上面的例子我们可以看出接口的一些特点：

- 接口没有实例域（成员变量 或者 属性）
- 接口可以有多个方法，只能给出定义（方法名，参数，返回类型），不能在接口中实现，只能由实现接口的类来实现
- 接口中的方法是没有public，private这些修饰符的，因为接口中的所有方法都为**public**。
- 接口中不能定义变量，但可以定义常量，默认的修饰符是**public static final**
- 接口不能够实例化，也就是说是不能创建对象的。



看了上面的例子后，或许你还是不太能理解接口的作用，为什么需要单独多出一个接口来声明方法啊，我直接去写方法它不香吗？



**接口的存在使得我们项目的开发变得结构清晰且规范，并且降低了代码的耦合性。**

当我们需要实现一个功能的时候，我们**先定义出功能的接口**，再写一个实现类去**实现该功能**，假如情况有变，我们需要写另外一个实现类满足该功能，我们只需要让新写好的类实现原来的接口，之后用原来的接口引用实现它的类就可以了。这样也使得项目更好维护。

从上面的例子也可以看出，接口其实是多态的一种实现方式。



**面向对象**的概念是Java的重中之重，没有理解清楚的小伙伴请一定要花时间去理解。接下来让我们开始今天的主要部分

## 一、java中的异常

### 1、异常机制

写代码的时候，如果出现BUG，一般都会出现异常（Exception），异常主要在**编译期间**和**运行期间**出现，如果是在编译时期出现好歹有编译器提醒我们修正，但如果是运行期间出现的异常，编译器就没法帮忙了，在开发过程中，如果我们不对出现的或可能出现的异常进行任何处理，整个项目就有可能会彻底瘫痪，所以我们需要手动写代码来处理异常（**也就是说，如果出现异常，我们要通过一定的方式来处理这个异常**）。



 在平时我们写java的时候，大家肯定早就接触了各种各样的异常。下面的三个例子分别展示了我们平时常见的异常以及java中异常的处理机制：

```java
public class Test {
    public static void main(String[] args) {
        int a=4,b=0;
        int c=a/b;
        System.out.println(c);
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        int[] ints = new int[5];
        int index=10;
        System.out.println(ints[index]);
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        int[] ints = new int[5];
        int index=10;
        try {
            System.out.println(ints[index]);
        } catch (ArrayIndexOutOfBoundsException e) {
            e.printStackTrace();
        }
        System.out.println("我继续执行啦！");
    }
}
```

### 2、异常的体系结构

在了解了java的异常机制后，我们先来了解一下java中异常的整体结构。

在java中，可以说是万物皆对象，包括我们的异常，实质上也是类，下面是类之间的关系图

![](https://img2018.cnblogs.com/blog/1294391/201809/1294391-20180919152605174-2400592.png)

- **Throwable**

  ：是java中所有异常和错误的父类,其两个子类为 Error(错误) 和 Exception(异常)

  - **Error**： 是程序中无法处理的错误，表示运行应用程序中出现了严重的错误。此类错误一般表示代码运行时JVM（java虚拟机）出现问题。通常有Virtual MachineError（虚拟机运行错误）、NoClassDefFoundError（类定义错误）等。比如说当jvm耗完可用内存时，将出现OutOfMemoryError。此类错误发生时，JVM将终止线程。**非代码性错误**。因此，当此类错误发生时，我们的程序不应该去处理此类错误。**（简单的来说Error是我们编写代码的时候不用去考虑处理的错误）**

  - **Exception**

    ： 是程序本身可以捕获并且可以处理的异常。其中可分为**运行时异常**（RuntimeException）和**非运行时异常**（也叫做受检异常）。我们平时最多遇到的也就是**运行时异常**。

    - **运行时异常(不受检异常)**： RuntimeException类极其子类表示JVM在运行期间可能出现的错误。编译器**不会检查**此类异常，并且**不要求处理**异常，比如用空值对象的引用（NullPointerException）、数组下标越界（ArrayIndexOutBoundException）。此类异常属于不可查异常，一般是由程序逻辑错误引起的，在程序中可以选择捕获处理，也可以不处理。**（编译时不会要求你去处理它，但运行时遇到该类异常会报错）**

    - **非运行时异常(受检异常)**： Exception中除RuntimeException极其子类之外的异常。**编译器会检查此类异常**，如果程序中出现此类异常，比如说IOException，**必须对该异常进行处理，要么使用try-catch捕获，要么使用throws语句抛出**，否则编译不通过。**（在编译的时候必须对此类异常进行处理，否则编译不通过）**

      举个栗子：

      ```java
      public class Test {
          public static void main(String[] args) {
              try {
                  //创建一个文件输出流
                  FileInputStream fis=new FileInputStream("D://Demo.txt");
                  int size=0;
                  String content=null;
                  byte[] buffer=new byte[1024];
                  //循环来读取该文件中的数据
                  while((size=fis.read(buffer))!=-1) {
                      content = new String(buffer, 0, size);
                      System.out.println(content);
                  }
                  fis.close();
              } catch (IOException e) {
                  e.printStackTrace();
              }
          }
      }
      ```

值得一提的是：其实还可以自定义异常，继承父类的异常类并重写构造方法就可以了。（大家可以课后自行去了解：http://c.biancheng.net/view/1051.html）

### 3、异常的处理

##### 3.1 通过try catch语句块捕获异常



#### try-catch-finally

其实在上面的例子当中，我们已经大量使用过了这种异常处理方式了。

- 我们将**可能出现异常的代码**放到try{}中, 运行时, 如果**代码发生了异常**的话,就会**生成一个对应的异常类的对象**.这个**产生的异常对象会与catch的异常类型相匹配**,匹配成功后就**被 catch 捕获**,然后**运行catch{}中的代码**, 一般catch中的代码为处理异常的代码, 比如**返回异常的信息和返回异常的详细信息**等. 一旦异常处理完成,就会跳出当前的try-catch结构。无论有没有发生异常**finally中的代码都会最后被执行**

- 在try结构中生命的变量，在出了try结构以后，就不能在被调用。如果想避免这种情况，就需要在try之前声明变量并初始化，在try中赋值。

```java
public class Test {
    public static void main(String[] args) {
        try {
            int[] arr = new int[5];
            int index=2;
            System.out.println(arr[index]);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            System.out.println("代码执行结束!");
        }
    }
}
```



体会：在开发中，运行时异常比较常见，此时一般不用try-catch去处理，而是用下面一种方式来处理，因为处理和不处理都是一个报错，最好办法是去修改代码。

##### 3.2 通过throw关键字**抛出异常**（抛出异常是为了让其它的代码程序来捕获这个异常） 



```java
    throw new Exception("这是一个异常");
```

### throws + 异常类型



- throws 一般用于方法中**可能存在异常**时, 需要将异常情况**向方法之上的层级抛出**（也就是调用这个函数的地方）, **由抛出方法的上一级来解决这个问题**, 如果方法的**上一级无法解决的会就会再将异常向上抛出**, 最终会抛给main方法(俗称甩锅) ，这样一来main方法中调用了这个方法的时候,就需要解决这个可能出现的异常.
  当然main方法也可以**不解决异常**, 将异常往上**抛出给java虚拟机**, 如果java虚拟机也无法解决的话,那么java虚拟机就over了

- `throws + 异常类型`也可以写在方法的声明处.指明此方法执行时,可能会抛出的异常类型. 

  ```java
  public static void main(String[] args) throws IOException {
      
  }
  ```

  

  try-catch-fianlly:真正的将异常给处理掉了。throws的方式只是将异常抛给了方法的调用者，并没有真正将异常处理掉。

  当然，将异常向上抛出也算是一种处理异常的方法



```java
public class Test {
    public static void main(String[] args) {
        int a=4,b=0;
        try {
            caculate(a,b);
        } catch (Exception e) {
            System.out.println("进入catch处理");
            e.printStackTrace();
        }

    }
    public static void caculate(int a,int b){
        if(b==0){
            throw new ArithmeticException("该分母为0");
        }
    }
}
```



### 4、如何查看报错信息

我们主要关注两个信息：

1、报错给的信息提示

2、大部分情况下，**最上方的报错信息就是我们代码出错的位置**。但是有时候最上方的日志**并不是我们自己的代码**，而是我们调用了一些第三方jar包的代码（别人写的代码）。我们还是根据上面报错，下面跟随来定位问题，那么真正报错的位置还是在上面。那么我们只需要从上往下依次找我们自己的代码即可。第一个找到的我们的代码位置就是我们代码中引发报错的位置。



关于报错堆栈的原理，大家感兴趣的可以去自行了解https://blog.csdn.net/Vermont_/article/details/105466068

## 二、集合框架

### 1、集合框架概述

  在我们进行项目开发**实现各种方法**的时候，选择不同的**数据结构**会给我们的代码带来很大的差异，需要**快速搜索成千上万个不重复的数据**吗？需要个数不受限制的数组吗？需要**建立一对一的键值对关联**吗？

  这部分内容我们将介绍**如何利用java中的类库来帮助我们实现数据结构**。当然，这里我们并没有时间去讲数据结构的知识，毕竟可能在大二的时候，你们很可能要单独上一学期的数据结构，所以这里只讲**每种我们所用到的类库的特点以及如何去使用它们**。具体的实现原理大家可以去自己了解



集合可以看作是**一种容器**，用来存储对象信息。 那我们以前也学过用来存储信息的容器---**数组**。

数组与集合的区别如下：

　　1）**数组长度不可变化**而且**无法保存具有映射关系的数据**；集合类可以用于保存数量不确定的数据，或者保存具有映射关系的数据 等。

　　2）数组元素既可以是基本类型的值，也可以是对象；**集合只能保存对象**。



![Java集合框架](https://img-blog.csdnimg.cn/20200321185804869.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29tYW4wMDE=,size_16,color_FFFFFF,t_70)

上面是**集合框架的主要结构**，Java集合类**主要由两个根接口Collection和Map派生出来的**，**Collection**派生出了三个子接口：**List、Set、Queue**（Java5新增的队列），因此Java集合大致也可分成**List、Set、Queue、Map四种接口体系**，它的**主要思想与接口和多态有着密不可分的联系**。



java集合类库将**接口与实现进行分开**，当我们考虑采用一种数据结构时，以List接口举例，比如我们想用一个类似数组的**顺序存储的结构[1,5,7,2,8]**时，我们既可以采用**ArrayList（动态数组）的实现方式**，同时也可以采用LinkedList（链表）的实现方式**（这里可以先不用知道链表是啥）**。那么一旦我们构造了一个集合，**我们就不需要知道它用了哪种实现方法，直接使用接口类型List来存放集合的引用。**

```java
List<Integer> arr = new ArrayList<>();
```

**如果我们想随时换一种实现，那只需要对构造对象的地方修改就行了**

```java
List<Integer> arr = new LinkedList<>();
```

### 2、Collection集合以及其实现类

#### 1.Set集合 （确定性，无序性，唯一性）

（也就是咱们高中学的那种集合）

它的特点是：

1、Set集合不允许存储相同的元素，所以如果把两个相同元素添加到同一个Set集合，则添加操作失败，新元素不会被加入，add()方法返回false（确定、唯一）

2、Set集合与数组不同，它不能通过下标来访问其中的某个元素（无序）

它的底层实现图大概是这样的：

![](https://tse2-mm.cn.bing.net/th/id/OIP-C.vZN_Gek_p3Mhx20kIlWxpAAAAA?w=223&h=180&c=7&r=0&o=5&pid=1.7)

常见的实现类是HashSet

```java
public class DemoSet {
    public static void main(String[] args) {
        HashSet<String> stringSet = new HashSet<>();
        //向集合内添加元素
        stringSet.add("温迪");
        stringSet.add("纳西妲");
        stringSet.add("温迪");//Set集合内容不能重复，所以只能有一个温迪
        stringSet.add("钟离");
        //输出一下，看看集合里面有些什么元素
        System.out.println(stringSet);
        
        //遍历输出集合里面的元素
        for (String s : stringSet) {
            System.out.println(s);
        }
        
        //删除集合内的一个元素
        stringSet.remove("纳西妲");
        System.out.println(stringSet);
    }
}
```

#### 2.List列表  （不同于Set集合，List是有序且可重复的）

可以把它理解做类似数组的特点（有序，可重复）

![](https://tse1-mm.cn.bing.net/th/id/OIP-C.8fI5uut1x3MDM-IGkCwZpQHaBB?w=404&h=55&c=7&r=0&o=5&pid=1.7)

最常见的两种实现类就是**ArrayList**和**LinkedList**

```java
public class DemoList {
    public static void main(String[] args) {
        //List的特性 有序 可重复
        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(7);
        list.add(7); //list允许重复 所以会有两个3
        //先输出一下list的内容
        System.out.println(list);
        //遍历输出list里面的元素
        for (Integer integer : list) {
            System.out.println(integer);
        }
        //根据下标访问list里面的数
        System.out.println("list中的第一个数为："+list.get(0));
        //根据下标删除list中的数
        list.remove(2);
        System.out.println(list);
    }
}
```

#### 3.Queue队列 （队列是先进先出的一条通道）

队列接口可以在队列的尾部添加元素，然后在队列的头部删除元素。

可以这样理解：有一条通道，里面有很多人在排队，在队列里面的人只能从通道的后面进入，从通道的前面出去。（至于怎么应用就得大家学到后面的时候才会了）

![queue实现图 的图像结果](https://tse2-mm.cn.bing.net/th/id/OIP-C.XShHMnLCFJ8vn4J3Bd7_HAHaDm?w=325&h=170&c=7&r=0&o=5&pid=1.7)

常见的实现是**ArrayDeque**

```java
public class Test1 {
    public static void main(String[] args) {
        ArrayDeque<Integer> arrayQueue = new ArrayDeque<Integer>(5);
        //向队列中添加元素
        arrayQueue.add(1);
        arrayQueue.add(2);
        arrayQueue.add(3);
        //输出队列内容
        System.out.println(arrayQueue);
        //遍历队列
        for (Integer num : arrayQueue) {
            System.out.println(num);
        }
        //从队列中弹出元素
        System.out.println("第1个出队元素"+arrayQueue.pop());
        System.out.println("第2个出队元素"+arrayQueue.pop());
        System.out.println("第3个出队元素"+arrayQueue.pop());

    }
}
```

### 3、Map集合以及其实现类

#### Map

Map可以简单理解为映射的集合

这种映射就是key-value（键值对），Map由一个或多个key组成（**这些key不能重复**），而每个key都对应着一个value（**value是可以重复的**，是不是有点像高中学的映射的概念？）。

![image](https://yumoimgbed.oss-cn-shenzhen.aliyuncs.com/img/8S}DJH2%NNBX`VFXRS%%9[F.png)

Map的实现方法有很多，常见的是**HashMap**（哈希表），HashTable，LinkedHashMap。其中最常用的就是HashMap

（为什么叫HashMap，因为这个map是以hash算法为原理，具体可以百度HashMap原理）

```java
public class DemoHashMap {
    public static void main(String[] args) {
//        map中传入的泛型不能是基本数据类型 (int,byte,double,float,char,boolean,long,short)
//        Map<int,String> wrongMap=new HashMap<>(); 这样子写是不行的，得用对应的封装类型来代替
        Map<Integer, String> map = new HashMap<>();
        map.put(1, "一号选手");
        map.put(2, "二号选手");
        map.put(3, "三号选手");

        //如果key值一样 会把之前的key-value给覆盖掉
        map.put(2, "二号替补选手");

        //hashmap的遍历方法（其实不止这一种，其他的请自行百度）
        for (Map.Entry<Integer, String> entry : map.entrySet()) {
            System.out.println(entry.getValue());
        }
    }
}
```


## 三、IO流

## IO流

IO就是Input，Output。

IO常用的应用场景是对文件的读入和写入，以及网络编程等等。

IO流从功能上来分就是**输入流**和**输出流**（输入流只能读取数据，不能写入数据；输出流只能写入数据，不能读取数据）。

IO流从处理单位来分是**字节流**和**字符流**（字节流以字节为单位，大小为8位；字符流以字符为操作单位，大小为16位）。

其中字节流的抽象基类用InputStream，OutputStream

字符流的抽象基类Reader，Writer

具体的IO类都需要继承实现其对应的抽象基类

![img](http://5b0988e595225.cdn.sohucs.com/images/20190114/1713d309ab744292a77455cdeb59a334.png)

![img](http://5b0988e595225.cdn.sohucs.com/images/20190114/0b4bfa0a7c904b4f9a64bb9ac5cefca8.png)

下面演示一个IO流的简单例子

```java
public class DemoIOException {
    public static void main(String[] args) throws IOException {
        //path是文件读取的路径
        String path = "/Users/jinyuzhou/IdeaProjects/sast_lesson/txt/list.txt";
        readFile(path);
    }

    //IO相关的Exception一定要抛出（属于是非RunTimeException） 否则编译器会报错
    public static void readFile(String path) throws IOException {
        FileReader fileReader = new FileReader(path);
        //BufferedReader类从字符输入流中读取文本并缓冲字符
        //BufferedReader可以一行一行读，而FileReader不行
        BufferedReader bufferedReader = new BufferedReader(fileReader);
        String line = null;
        while ((line = bufferedReader.readLine()) != null) {
            System.out.println(line);
        }
    }
}
```

