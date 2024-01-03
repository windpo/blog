---
title: 'JAVASE 内部类'
date: "2022-4-10"
description: ''
tags: ["学习笔记", "JAVASE基础"] 
---

# 内部类

使用内部类的用处：

1、内部类可以对同一个包中的其他类隐藏

2、内部类方法可以访问定义该类的作用域中的数据，包括原本的私有数据

## 一、内部类

```java
/**
 * 内部类的使用演示
 *     1、内部类用来为外面的类提供一些代码块的实现（比如继承一些接口实现一些特定功能），它含有外部类的引用，能直接使用外部类的数据
 *     2、现在经常用lambda表达式在这些方面代替内部类
 * @author 风之诗
 * @version 1.0
 */
```

```java
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.time.Instant;

/**
 * 内部类的使用演示
 *     1、内部类用来为外面的类提供一些代码块的实现（继承一些接口），它含有外部类的引用，能直接使用外部类的数据
 *     2、现在经常用lambda表达式在这些方面代替内部类
 * @author 风之诗
 * @version 1.0
 */
public class InnerClassTest {
    public static void main(String[] args) {
        TalkingClock clock = new TalkingClock(1000, true);
        clock.start();

        JOptionPane.showMessageDialog(null,"Quit program?");
        System.exit(0);
    }
}
class TalkingClock{
    private int interval;
    private boolean beep;

    /**
     * Constructs of TalkingClock
     * @param interval  每隔一段时间执行的信息
     * @param beep  是否响铃
     */
    public TalkingClock(int interval, boolean beep) {
        this.interval = interval;
        this.beep = beep;
    }

    /**
     * 执行计时器
     */
    public void start(){
        TimePrinter timePrinter = new TimePrinter();
        //其中interval为外部类的私有变量，用来设置打印的间隔时间
        new Timer(interval,timePrinter).start();
    }
    public class TimePrinter implements ActionListener{
        /**
         * 打印目前时间以及判断是否响铃
         *      其中beep为外部类的私有变量
         * @param e 记录事件具体信息
         */
        @Override
        public void actionPerformed(ActionEvent e) {
            System.out.println("At rhe tone,the time is"
                    + Instant.ofEpochMilli(e.getWhen()));
            if (beep){
                Toolkit.getDefaultToolkit().beep();
            }
        }
    }
}
```

## 二、局部内部类

```java
局部内部类：在一个方法中局部地定义这个内部类，局部类的作用域仅在这个方法之中
    优点：1、简化代码
    	 2、不仅可以访问外部类的字段，还可以访问局部变量字段（该字段必须是事实最终变量：在该方法运行时不会改变的变量）
```

例如，在以上例子中，我们在方法start中创建了一个内部类的实例对象

```
class TalkingClock2{
    public void start(int interval,boolean beep){
        class TimePrinter implements ActionListener{
            @Override
            public void actionPerformed(ActionEvent e) {
                System.out.println("At the time,the time is"
                + Instant.ofEpochMilli(e.getWhen()));
                if(beep){
                    Toolkit.getDefaultToolkit().beep();
                }
            }
        }
        TimePrinter timePrinter = new TimePrinter();
        new Timer(interval,timePrinter).start();
    }
}
```

我们可以将上段代码进行修改，将内部类定义在方法中

```
public void start(){
     class TimePrinter implements ActionListener{
        /**
         * 打印目前时间以及判断是否响铃
         *      其中beep为外部类的私有变量
         * @param e 记录事件具体信息
         */
        @Override
        public void actionPerformed(ActionEvent e) {
            System.out.println("At rhe tone,the time is"
                    + Instant.ofEpochMilli(e.getWhen()));
            if (beep){
                Toolkit.getDefaultToolkit().beep();
            }
        }
    }
    TimePrinter timePrinter = new TimePrinter();
    //其中interval为外部类的私有变量，用来设置打印的间隔时间
    new Timer(interval,timePrinter).start();
}
```

## 三、匿名内部类

​	如果指向创建该类的对象，无需对该类指定名字，那么我们用匿名内部类来简化操作

​		优点：当我们在实现一个函数式接口时，我们可以用匿名内部类来简化代码，简化后的局部内部类代码如下

```
/**
 * 方法三：匿名内部类实现
 */
class TalkingClock2{
    public void start(int inverval,boolean beep){
        ActionListener actionListener = new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                System.out.println("At the tone,the time is"
                +Instant.ofEpochMilli(e.getWhen()));
                if(beep){
                    Toolkit.getDefaultToolkit().beep();
                }
            }
        };
        new Timer(inverval,actionListener).start();
    }
}
```

## 四、静态内部类

​	静态内部类不含外部类的指针，所以静态内部类无法访问外部类的属性和方法

​		优点：静态内部类能隐藏该类，解决命名冲突

​		实例：

```java
/**
 * @author 风之诗
 * @version 1.0
 * 演示静态内部类的使用，实现一个方法返回比较数组的最大数和最小数，通过静态内部类来实现代表一组最大值和最小值
 */
public class StaticInnerClassTest {
    public static void main(String[] args) {
        double[] values = new double[20];
        for (int i = 0; i < values.length; i++) {
            values[i]=100*Math.random();

        }
        ArrayAlg.Pair minmax = ArrayAlg.minmax(values);
        System.out.println("min="+minmax.getFirst());
        System.out.println("max="+minmax.getSecond());
    }
}
class ArrayAlg{
    /**
     * 定义一个静态内部类Pair，其中包含有一个最小值一个最大值
     */
    public static class Pair{
        private double first;
        private double second;

        public Pair(double first, double second) {
            this.first = first;
            this.second = second;
        }

        public double getFirst() {
            return first;
        }

        public double getSecond() {
            return second;
        }
    }

    /**
     * 通过设置一个min和max来比较一个数组中的最大值和最小值
     * @param values 传入比较的数组
     * @return 返回一个Pair内部类，其中包含有两个值，分别为最大值和最小值
     */
    public static Pair minmax(double[] values){
        double min=Double.POSITIVE_INFINITY;
        double max=Double.NEGATIVE_INFINITY;
        for (double v :
                values) {
            if (min>v){
                min=v;
            }
            if(v>max){
                max=v;
            }
        }
        return new Pair(min,max);
    }
}
```

## 五、比较匿名内部类和lambda表达式来实现函数式接口

​	说明lambda表达式的简洁，所以如果要实现事件监听器或者其它类似的接口，最好使用lambda表达式来代替内部类

​	FourTypesOfInner.java

```java
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.time.Instant;

/**
 * 用4种方法实现Timer定时器
 *  1、内部类：见InnerClassTest.java
 *  2、局部内部类：在方法中定义一个内部类来实现ActionListener
 *  3、匿名内部类：简化局部内部类，直接实现AciontListener
 *  4、lambda表达式：利用lambda表达式实现ActionListenner接口（函数式接口）
 * @author 风之诗
 * @version 1.0
 */
public class FourTypesOfInner {
    public static void main(String[] args) {
        TalkingClock2 talkingClock2 = new TalkingClock2();
        talkingClock2.start(1000,true);
        JOptionPane.showMessageDialog(null,"Quit program?");
        System.exit(0);
    }
}

/**
 * 方法二：局部内部类实现
 */
//class TalkingClock2{
//    public void start(int interval,boolean beep){
//        class TimePrinter implements ActionListener{
//            @Override
//            public void actionPerformed(ActionEvent e) {
//                System.out.println("At the time,the time is"
//                + Instant.ofEpochMilli(e.getWhen()));
//                if(beep){
//                    Toolkit.getDefaultToolkit().beep();
//                }
//            }
//        }
//        TimePrinter timePrinter = new TimePrinter();
//        new Timer(interval,timePrinter).start();
//    }
//}
/**
 * 方法三：匿名内部类实现
 */
//class TalkingClock2{
//    public void start(int inverval,boolean beep){
//        ActionListener actionListener = new ActionListener() {
//            @Override
//            public void actionPerformed(ActionEvent e) {
//                System.out.println("At the tone,the time is"
//                +Instant.ofEpochMilli(e.getWhen()));
//                if(beep){
//                    Toolkit.getDefaultToolkit().beep();
//                }
//            }
//        };
//        new Timer(inverval,actionListener).start();
//    }
//}
/**
 * 方法四：lambda表达式实现（最简单)
 */
class TalkingClock2{
    public void start(int inverval,boolean beep){
        new Timer(inverval,e -> {
            System.out.println("At the tone,the time is"+Instant.ofEpochMilli(e.getWhen()));
            if(beep){
                Toolkit.getDefaultToolkit().beep();
            }
        }).start();
    }
}
```