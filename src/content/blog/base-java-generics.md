---
title: 'JAVASE 泛型设计'
date: "2022-4-20"
description: ''
tags: ["学习笔记", "JAVASE基础"] 
---

# 泛型设计

## 一、泛型类

泛型类的定义：在类名之后紧跟<T>（java中常用E表示集合的元素的类型，K，V表示表的键值的类型，T、U、S表示“任意类型”）

泛型类演示：

Pair.java

```java
package javaLearn_chapter8.pair1;

/**
 * @author 风之诗
 * @version 1.0
 * 创建一个泛型类
 */
public class Pair <T>{
    private T first;
    private T second;

    public Pair(){
        first=null;
        second=null;
    }

    public Pair(T first, T second) {
        this.first = first;
        this.second = second;
    }

    public T getFirst() {
        return first;
    }

    public void setFirst(T first) {
        this.first = first;
    }

    public T getSecond() {
        return second;
    }

    public void setSecond(T second) {
        this.second = second;
    }
}
```

PairTest.java

```java
package javaLearn_chapter8.pair1;

/**
 * @author 风之诗
 * @version 1.0
 * 泛型类使用演示
 */
public class PairTest1 {
    public static void main(String[] args) {
        String[] words={"Mary","had","a","little","lamb"};
        final Pair<String> mm = ArrayAlg.minmax(words);
        System.out.println("min= "+mm.getFirst());
        System.out.println("max= "+mm.getSecond());
    }
}

/**
 * 使用Pair<String>作为函数的返回值，返回一个包含有最大字符串和最小字符串的对象
 */
class ArrayAlg
{
    public static Pair<String> minmax(String[] a){
        /**
         * 得到一堆字符串中的最大值和最小值
         */
        if(a==null||a.length==0){
            return null;
        }
        String min=a[0];
        String max=a[0];
        for (int i = 1; i < a.length; i++) {
            if(a[i].compareTo(max)>0){
                max=a[i];
            }
            if(a[i].compareTo(min)<0){
                min=a[i];
            }
        }
        return new Pair<>(min,max);
    }
}
```

## 二、泛型方法

​		泛型方法的的类型变量放在修饰符之后（如：public static <T> ）在调用时写上类型，也可以省略，由系统推出

## 三、类型变量的限定

​		1、概述：

​		在泛型中，有时我们需要对类类型变量加以约束（比如我们需要限定仅需要满足所有实现了comparable的对象），此时我们就需要设置一个限定来实现这一点。

​		2、细节：

```java
类型变量限定：
*          0、限制的变量类型既可以是类，也可以是接口
*          1、如何限制类型变量 <T extends Comparable>指任意实现了（子类）Comparable的类型
*          2、限制多个类型变量 <T extends A&B>指任意实现了（子类） A和B的类型
*          3、最多有一个限定可以是类，如果有一个类作为限定，它必须为限定列表中的第一个限定
```

​		3、变量限定的实例演示

​		（1）、Pair.java(见泛型类的代码演示)

​		（2）、PairTest2.java

```java
import javaLearn_chapter8.pair1.Pair;

import java.time.LocalDate;

/**
 * @author 风之诗
 * @version 1.0
 * 演示类型变量限定的使用
 *      类型变量限定：
 *          0、限制的变量类型既可以是类，也可以是接口
 *          1、如何限制类型变量 <T extends Comparable>指任意实现了（子类）Comparable的类型
 *          2、限制多个类型变量 <T extends A&B>指任意实现了（子类） A和B的类型
 *          3、最多有一个限定可以是类，如果有一个类作为限定，它必须为限定列表中的第一个限定
 */
public class PairTest2 {
    public static void main(String[] args) {
        LocalDate[] birthdays={
                LocalDate.of(1906,12,9),
                LocalDate.of(1815,12,10),
                LocalDate.of(1903,12,3),
                LocalDate.of(1910,6,22),
        };
        Pair<LocalDate> mm=ArrayAlg.minmax(birthdays);
        System.out.println("min= " + mm.getFirst());
        System.out.println("max= " + mm.getSecond());
    }
}
class ArrayAlg{
    /**
     * 比较得到任意对象T数组中的最大值和最小值
     */
    public static <T extends Comparable>Pair<T>minmax(T[] a){
        if(a==null||a.length==0){
            return null;
        }
        T min=a[0];
        T max=a[0];
        for (int i = 1; i < a.length; i++) {
            if(min.compareTo(a[i])>0){
                min=a[i];
            }
            if(max.compareTo(a[i])<0){
                max=a[i];
            }
        }
        return new Pair<T>(min,max);
    }
}
```

## 四、泛型的限制与局限性

### 	1、不能用基本类型实例化类型参数

​		因为泛型的本质为类型擦除，泛型类型最终都会变成指定的类型，擦除之后，Pair<T>中会含有Object字段，而Object类型不能存储double值。

### 	2、运行时类型查询仅适用于原始类型

​		在虚拟机中的对象总有一个特定的非泛型类型。因此所有类型查询仅能产生原始类型。

​			如：Pair<String>在instenceof()中仅为Pair类型，而不是Pair<String>类型

​			同理，getClass方法总是得到返回的原始类型		Pair<String>.getClass()==Pair<Employee>.getClass()

### 	3、不能创建参数化类型的数组

​		不能实例化参数化类型的数组：var table=new Pair<String>[10] 错误

​		原因：在类型擦除之后，table的类型是Pair[],可以把它转化为一个Object[]，数组会记住它的元素类型，如果试图存储其他类型的元素，会抛出一个ArrayStore异常。

​		推荐做法：**如果想收集参数化类型的对象，简单地使用ArrayList：ArrayList<Pair<String>>更安全有效**

### 	4、可变参数传入报错

​		由3可知，我们不能使用参数化类型的数组，但可变参数的本质就是一个数组，那当我们传入参数时，系统会增加一个警告

此时，我们需要在方法之前添加注解@SafeVarargs来抑制该方法的警告

### 	5、不能实例化类型变量

​		public Pair ( ) { first = new T();  second = new T(); }错误

​		解决方法：让调用者提供一个构造器表达式（以后再来理解）

### 	6、不能构造泛型数组

​		（1）、当在方法中创建一个数组时

​			public static <T extends Comparable> T[ ] minmax(T... a){	

​					T[] mm=new T[2];//ERROR

}

​	类型擦除会让这个方法总是构造Comparable[2]的数组

​		（2）、如果该数组作为类的一个属性时，也会出现问题。

​		所以综上建议不要用泛型数组，可以用ArrayList来代替

### 	7、不能使用带有类型变量的静态字段和方法

​			以下代码错误，T不能作为静态字段和方法的类型，但是Pair<T>可以，因为Pair<T>为一个特定的Pair类型

```java
class Singleton<T>{
    private static T singleInstance;
    private static T getSingleInstance(){
        return singleInstance;
    }
}
```

### 	8、不能抛出或捕获泛型类的实例

​			但可以在异常规范中使用类型变量，如：<T extends Throwable>

### 	9、可以取消编译器对检查型异常的检查

​			可以用泛型加@SuppressWarnings注解来消除编译器的限制（每个检查型异常必须被抛出或处理）

### 	10、不能同时实现继承同一类的不同类型变量的方法

​			倘若两个接口类型是同一接口的不同参数化，一个类或类型变量就不能同时作为这两个接口类型的子类

## 五、泛型类型的继承规则（把泛型类看作一个单独的新类型）

​	**1、泛型类中的泛型变量拥有继承关系，与泛型类没有任何关系  例如：Pair<Manager>与Pair<Employee>直接没有任何继承关系**

​	**2、一个泛型类可以实现其它的泛型类或者继承其它的泛型类**  如：ArrayList<T>类实现了List<T>接口，它们俩具有接口实现关系

### 六、泛型通配符

#### 	1、<?>:表示任意类型，不能写入数据，读出的数据类型为Object

#### 	2、<? extends Employee>：表示Employee以及其子类，表示泛型的上限，只能读出数据

#### 	3、<? super Manager>：表示Manager以及其父类，表示泛型的下限，只能写进去数据

#### 	4、泛型通配符<?>和泛型<T>的理解

​		泛型： 是一个形参，可以理解为一个占位符，被使用时，会在程序运行的时候替换成具体的类型，比如替换成String，Integer之类的。

​		故：泛型一般用于在设计类的泛型、泛型方法的时使用

​		通配符： 是一个实参，这是Java定义的一种特殊类型，比Object更特殊，就像一个无所不能的对象更胜于object。比如List和List是没有父子关系的两个类型，但是List<?> 更似于是所以java中所有对象的的父类。

​		故：泛型通配符一般用于想在一个方法中捕获类似"含有一个泛型参数的类Pair<? extends Employee>或者表（List<? super Manager>）"的形式参数

#### 	5、泛型和通配符的区别

1.泛型可以进行写操作，通配符则不行

public static <T extends Number> void addTExtend(List<T> list, T e){
    list.add(e);
}

public static void addTExtend(List<?> list, T e){
    list.add(e);//会报错，由于通配符可泛指任何类型对象，所以在写操作后，List集合内不知道是什么类型而不允许操作
}

public static void addTExtend(List<? super T> list, T e){
    list.add(e);//不会报错，由于限定了T的子类及T自身，限定通配符可以进行写操作
}

2.泛型和通配符获取泛指值

public static <T extends Number> void addTExtend(List<T> list, T e){
    T t = list.get(0);
}

public static void addTExtend(List<?> list, T e){
    Object o = list.get(0);//get出来以后只能用Object接收
}

public static void addTExtend(List<? extends T> list, T e){
    Object/T o = list.get(0);//get出来以后可以通过Object和T来接收
}

public static void addTExtend(List<? super T> list, T e){
    Object o = list.get(0);//get出来以后可以通过Object来接收
}

3.泛型可以多继承，通配符不允许

