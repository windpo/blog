---
title: 'JAVASE 正则表达式'
date: "2022-5-10"
description: ''
tags: ["学习笔记", "JAVASE基础"] 
---

# 正则表达式

## *一、正则表达式概念及快速入门*

#### 1、概念

​	正则表达式(regular expression)描述了一种字符串匹配的模式（pattern），可以用来检查一个串是否含有某种子串、将匹配的子串替换或者从某个串中取出符合条件的子串等。

​	如：平时我们利用String类来处理字符串信息的时候，需要对一个一个字符进行处理，如果想验证一大串信息是否满足要求时，想要达到各种各样的要求就非常的复杂，但正则表达式的使用就会使我们处理字符串非常的方便，正则表达式将会是我们处理各种字符串的利器。

#### 2、正则表达式快速入门

```java



import java.util.regex.Matcher;
import java.util.regex.Pattern;

/**
 * 体验正则表达式威力，体现正则表达式处理文本带来的威力
 * @author 风之诗
 */
public class Regexp_ {
        public static void main(String[] args) {
                /**
                 * 三段内容，分别是java的基本介绍，html网页代码，一些地址
                 */
                String content1="1995年，互联网的蓬勃发展给了Oak机会。业界为了使死板、单调的静态网页能够“灵活”起来，急需一种" +
                        "软件技术来开发一种程序，这种程序可以通过网络传播并且能够跨平台运行。于是，世界各大IT企业为此纷纷投入了大量的人力、物力和财力。这个时候，Sun公司想起了那个被搁置起来很久的Oak，并且重新审视了那个用软件编写的试验平台，由于它是按照嵌入式系统硬件平台体系结构进行编写的，所以非常小，特别适用于网络上的传输系统，而Oak也是一种精简的语言，程序非常小，适合在网络上传输。Sun公司首先推出了可以嵌入网页并且可以随同网页在网络上传输的Applet（Applet是一种将小程序嵌入到网页中进行执行的技术）" +
                        "，并将Oak更名为Java（在申请注册商标时，发现Oak已经被人使用了，再想了一系列名字之后，最" +
                        "终，使用了提议者在喝一杯Java咖啡时无意提到的Java词语）。5月23日，Sun公司在Sun world会" +
                        "上正式发布Java和HotJava浏览器。IBM、Apple、DEC、Adobe、HP、Oracle、Netscape和微软等" +
                        "各大公司都纷纷停止了自己的相关开发项目，竞相购买了Java使用许可证，并为自己的产品开发了相应的Ja" +
                        "va平台。";
                String content2="私有地址（Private address）属于非注册地址，专门为组织机构内部使用。\n" +
                        "以下列出留用的内部私有地址\n" +
                        "A类 10.0.0.0--10.255.255.255\n" +
                        "B类 172.16.0.0--172.31.255.255\n" +
                        "C类 192.168.0.0--192.168.255.255";
                       
                //传统方法：使用遍历，代码量大，效率不高
                //正则表达式技术
                //1、提取文章中的所有英文单词
                //2、提取文章中的所有数字
                //3、提取文章中的所有数字和英文单词
                //4、匹配url


                //1.先创建一个pattern对象：模式的对象，正则表达式的对象
                Pattern pattern1 = Pattern.compile("[a-zA-Z]+");
                //Pattern pattern2 = Pattern.compile("[0-9]+");
                //Pattern pattern3 = Pattern.compile("([0-9])+|([a-zA-Z]+)");
                //Pattern pattern4 = Pattern.compile("\\d+\\.\\d+\\.\\d+\\.\\d+");
                //2.创建一个匹配器对象：就是按照pattern对象的模式，到content的文本中进行匹配和解释的对象
                Matcher matcher = pattern.matcher(content1);
                //matcher.find()方法，即在文本中寻找字符串，找到返回true，未找到返回false
                //匹配字符串
                int no=0;
                while(matcher.find()){
                        //匹配内容，文本，放到m.gruop(0)中
                        System.out.println("找到: "+(++no)+" "+ matcher.group(0));
                }
        }

}
```

## *二、正则表达式底层源码分析*

#### 1、不分组以及分组

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

/**
 * 源码分析以及正则表达式的分组的分析
 */
public class RegTheory {
    public static void main(String[] args) {
        String content="1995年，互联网的蓬勃发展给了Oak机会。业界为了使死板、单调的静态网页能够“灵活”起来，急需一种" +
                "软件技术来开发一种程序，这种程序可以通过网络传播并且能够跨平台运行。于是，世界各大IT企业为此纷纷投入了大量的人力、物力和财力。这个时候，Sun公司想起了那个被搁置起来很久的Oak，并且重新审视了那个用软件编写的试验平台，由于它是按照嵌入式系统硬件平台体系结构进行编写的，所以非常小，特别适用于网络上的传输系统，而Oak也是一种精简的语言，程序非常小，适合在网络上传输。Sun公司首先推出了可以嵌入网页并且可以随同网页在网络上传输的Applet（Applet是一种将小程序嵌入到网页中进行执行的技术）" +
                "，并将Oak更名为Java（在申请注册商标时，发现Oak已经被人使用了，再想了一系列名字之后，最" +
                "终，使用了提议者在喝一杯Java咖啡时无意提到的Java词语）。5月23日，Sun公司在Sun world会" +
                "上正式发布Java和HotJava浏览器。IBM、Apple、DEC、Adobe、HP、Oracle、Netscape和微软等" +
                "各大公司都纷纷停止了自己的相关开发项目，竞相购买了Java使用许可证，并为自己的产品开发了相应的Ja" +
                "va平台。";
        //  \\d表示任意一个数字
        String RegStr="(\\d\\d)(\\d\\d)";//正则表达式模式字符串
        //1、创建一个pattern对象来传入正则表达式
        Pattern pattern = Pattern.compile(RegStr);
        //2、创建matcher匹配器 传入所需匹配字符串的内容 按照pattern定义模式的方法去匹配
        Matcher matcher = pattern.matcher(content);
        //3、开始匹配
        while(matcher.find()){
            //有括号时的匹配规则：
            //group（0）代表取出全部
            //group（1）代表取出第一组
            //group（2）代表取出第二组
            //注意：1、匹配的组数不能越界，有几组就取出几组。
            //     2、如果没分组，就只有group（0）
            System.out.println("找到： "+matcher.group(0));
            System.out.println("第一组()匹配到的值： "+matcher.group(1));
            System.out.println("第二组()匹配到的值： "+matcher.group(2));
        }
    }
}
```

解析：

```java
/**
 *
 * matcher.find()完成的任务 (考虑分组)：定位所匹配到的位置，然后交给.group转化为字符串
 *find（）过程：
 * 分组：正则表达式中用（）来分组，如(\d\d)(\d\d),第一个（）表示第一组，第二个（）表示第二组
 * 1、根据指定的规则，定位满足规则的子字符串（如1998）
 * 2、找到后，将子字符串的索引位置记录到matcher对象的属性 int[] groups；
 *      2.1 groups[0]=0，将字符串结束的索引+1并记录在groups[1]=4（因为后面group方法寻找的下标不包含最右边）
 *      2.2 记录1组()匹配到的字符串的索引groups[2]=0 gruops[3]=2
 *      2.3 记录2组()匹配到字符串的索引gruops[4]=2 gruops[5]=4
 * 3、统计记录oldLast 的值 为结束的索引+1 为4，下次使用find时，就从4 开始匹配
 *
 * matcher.group(0) 分析
 * 源码：
 * public String group(int group) {
 *         if (first < 0)
 *             throw new IllegalStateException("No match found");
 *         if (group < 0 || group > groupCount())
 *             throw new IndexOutOfBoundsException("No group " + group);
 *         if ((groups[group*2] == -1) || (groups[group*2+1] == -1))
 *             return null;
 *         return getSubSequence(groups[group * 2], groups[group * 2 + 1]).toString();
 *     }
 *  1、根据groups[0]=0和groups[1]=4的位置，从content中截取子字符串返回
 *     就是从[0,4)包含的子字符串（不包含4）
 *  2、同理：当gruops（1）就是根据groups[2]=0和groups[3]=2的位置，从第一组括号里面提取下标转化为字符串，取出第一个分组
 */
```

## *三、正则表达式语法*

#### 1、字符匹配符:匹配自己想要选择的字符串的一种格式

```java
/**
 * //d:匹配0-9数字
 * //D：匹配非0-9数字
 * //w：匹配任意英文字符，数字以及下划线
 * //W：匹配非任意英文字符，数字以及下划线
 * //s：匹配任何空白字符（空格、制表符）
 * //S：匹配任何非空白字符
 * . ：匹配除任何\n之外的所有字符
 * -:连字符 表示一个字符范围  实例：A-Z 任意单个大写字母
 * []:可接受的字符列表 匹配括号里面的字符 实例：[abcd]--->接受abcd其中任意一个字符 [a-zA-Z]---->接受a-z,A-Z其中任何一个字符
 * [^]：不可接受字符列表 匹配非括号里面的字符 实例：	[^abcd]--->接受除了abcd外的其它字符
 
 !!转义!!
 *在其他语言中，\\ 表示：我想要在正则表达式中插入一个普通的（字面上的）反斜杠，请不要给它任何特殊的意义。
*在 Java 中，\\ 表示：我要插入一个正则表达式的反斜线，所以其后的字符具有特殊的意义。
*所以，在其他的语言中（如 Perl），一个反斜杠 \ 就足以具有转义的作用，而在 Java 中正则表达式中则需要有两个反斜杠才能被解析为其他*语言中的转义作用。也可以简单的理解在 Java 的正则表达式中，两个 \\ 代表其他语言中的一个 \，这也就是为什么表示一位数字的正则表达 *式是 \\d，而表示一个普通的反斜杠是 \\。
*System.out.print("\\");    // 输出为 \
*System.out.print("\\\\");  // 输出为 \\


 */
```

#### 2、选择匹配符：相当于或

|: 如果匹配某个字符是选择性的，则需要用|来匹配。 实例：ab|cd  匹配ab或者cd

#### 3、限定符：用于指定某一段字符连续出现多少次

```java
/**
 *
 *  *:表示匹配指定字符的0到n次  (abc)*-->abc、abcabc
 *  +：表示匹配指定字符的1到n次  m(abc)+-->mabc、mabcabc
 *  ?:表示匹配指定字符的0或1次   mabc?--->mab、mabc
 *  {n}:匹配长度为n的指定字符串  [abcd]{3}---->abc、acd、adc、bcd。。。。
 *  {n,}:匹配长度最少为n的指定字符串  [abcd]{4,}---->abcd、aabcd、bcdaaaa
 *  {a,b}:匹配长度最少为a最多为b的指定字符串 [abcd]{1,2}---->ab、a、cd
 */
```

贪婪匹配与非贪婪匹配机制：

（1）、贪婪匹配：当我们有一个字符串“abc123456”的时候，我们使用"\\\\d+"的时候，系统每次会自动匹配尽可能多的字符串123456，即贪婪匹配

（2）、解决贪婪匹配：我们用?作用与限定符“+”、“*”上，实现非贪婪匹配，如"\\\\d+?"，系统每次仅仅会匹配最少的字符串1 

#### 4、分组：将字符串分为一个个组别,匹配子字符串

```java
/**
 * 演示分组的使用
 *  1、非命名分组：将一段匹配串分为n个组，然后用group可以分开取出每一个组别
 *  2、命名分组：在组别的开头加上?<名称>,取组时，可以直接用group（组名）来取出相应的组
 */
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegExp05 {
    public static void main(String[] args) {
        String content="aoawijd 1995 2351321 1152 132a 1123";
        String regString1="(\\d\\d)(\\d\\d)";//普通分组
        String regString2="(?<num1>\\d\\d)(?<num2>\\d\\d)";//命名分组
        Pattern pattern = Pattern.compile(regString2);
        Matcher matcher = pattern.matcher(content);
        while(matcher.find()){
            System.out.println("找到： "+matcher.group(0));
            System.out.println("找到第一组： "+matcher.group("num1"));
            //System.out.println("找到第一组： "+matcher.group(1));
            System.out.println("找到第二组： "+matcher.group("num2"));
            //System.out.println("找到第二组： "+matcher.group("2"));
        }
    }
}
```

#### 5、特别分组（非捕获匹配）:不会将分的组保存下来，可以看作是一种特殊的表达式

```java
/**
 *简化表达：如industry|industies--->indus(?:try|ties)
 * content(?:pattern):匹配含有后面多者之一的整个串    例如：venti(?:moyu|qifei) 匹配ventimoyu和ventiqifei两种字符串
 * content(?=pattern):匹配含有后面多者之一的前面的串   例如：venti(?=moyu|qifei) 匹配ventimoyu和ventiqifei两种字符串中的venti
 * content(?!pattern):匹配不含有后面多者之一的前面的串  例如：venti(?!moyu|qifei) 匹配非ventimoyu和ventiqifei两种字符串中的venti
 */
```

#### 6、定位符:指定开头与结束字符串的格式

```java
/**
 *   （1）、^:指定起始字符，整个字符串必须以^后面的内容开头  ^[0-9]+[a-z]* --->以至少一个数字开头，后面接任意个小写字母的字符串 123、6aa
 *   （2）、$：指定结束字符，整个字符串必须以$前面的内容结束  ^[0-9]\\-[a-z]+$ ---->以一个数字开头，中间用“-”连接，并以至少一个小写字母结尾 1-a
 *	 （3）、其它的定位符,在平时使用的时候较少，如果想进一步了解，请自行搜索（\\b,\\B）
 */

import java.util.regex.Matcher;
import java.util.regex.Pattern;

/**
 * 定位符用法演示
 */
public class RegExp01 {
    public static void main(String[] args) {
            String content="韩顺平a教育";
            String regString1="^[\u0391-\uffe5]+a[\u0391-\uffe5]+$";
            String regString2="[\u0391-\uffe5]+$";
        Pattern pattern = Pattern.compile(regString1);
            Matcher matcher = pattern.matcher(content);
            while(matcher.find()){
                System.out.println("找到： "+matcher.group(0));
            }
        }

}
```

#### 7、反向引用：分组后括号里的字符串被捕获后，可以在这个括号后面被再次使用

常用于匹配：

（1）、多个连续的数字  1111、2222、666666

（2）、个位与千位相同，十位与百位相同的数字  1221、2662

（3）、12321等等有特殊要求的数字

```java
/**
 * 反向引用
 *  内部的反向引用：\\  在正则表达式中使用
 *  外部的反向引用：$   在Pattern或者Matcher中的方法中使用
 */
 
 import java.util.regex.Matcher;
import java.util.regex.Pattern;

 public class RegExp10 {
    public static void main(String[] args) {
        String content3 = "12321-999888777";
        String content4 = "我...我要.....学学学学...编程java!";
        /**
         *  1、\\1可以调用第一个分组，引用第一个分组的规则  (\\d)\\1 -->两个相同的数 (\\d)\\1{4}  --->五个相同的数
         *  2、(\\d)(\\d)\\2\\1 --->  1551 2442格式
         *  3、检索12321-999888777 前面一个五位数，中间一个-，最后连续三个相同的9位数
         *  4、结巴程序:
         *         (1).去掉所有的点:   \.+
         *         (2).去掉所有重复的字:  1使用(.)\\1+匹配任意多个相同的字符  2使用反向引用$1来替换匹配到的内容
         */
        String regString3 = "^(\\d)(\\d)\\d\\2\\1-(\\d)\\3{2}(\\d)\\4{2}(\\d)\\5{2}$";

        /**(1).去掉所有的点*/
        String regString41 = "\\.+";

//        Pattern pattern = Pattern.compile(regString41);
//        Matcher matcher = pattern.matcher(content4);
//        content4 = matcher.replaceAll("");
        //使用一条语句替换
        content4 = Pattern.compile(regString41).matcher(content4).replaceAll("");

        System.out.println("content4= " + content4);

        /**(2).去掉所有重复的字*/
        String regString42="(.)\\1+";

//        Pattern pattern1 = Pattern.compile(regString42);
//        matcher=pattern1.matcher(content4);
//        content4 = matcher.replaceAll("$1");
        //使用一条语句直接替换
        content4 = Pattern.compile(regString42).matcher(content4).replaceAll("$1");
        System.out.println("content4= "+content4);
    }
}
```

## *四、正则表达式相关类以及常用方法*

#### 1、Pattern类

- Pattern 类：

  pattern 对象是一个正则表达式的编译表示。Pattern 类没有公共构造方法。要创建一个 Pattern 对象，你必须首先调用其公共静态编译方法，它返回一个 Pattern 对象。该方法接受一个正则表达式作为它的第一个参数。

  **创建过程**

  ```
  Pattern pattern = Pattern.compile(regString);
  ```

**Pattern常用方法：**

​	Pattern.compile（）:传入一个正则表达式参数，创建一个Pattern类对象

​	Pattern.matches（）：整体匹配，尝试将整个区域与模式匹配。

#### 2、Matcher类

- Matcher 类：

  Matcher 对象是对输入字符串进行解释和匹配操作的引擎。与Pattern 类一样，Matcher 也没有公共构造方法。你需要调用 Pattern 对象的 matcher 方法来获得一个 Matcher 对象。

  **创建过程：**

  ```
  Matcher matcher = pattern.matcher(content);
  ```

**Matcher常用方法：**

```java
/**
 *   matcher.start():返回找到的字符串的首索引
 *   matcher.end():返回找到的字符串的末尾索引+1
 *   matcher.matches():整体匹配,常用于去检验某个字符串是否满足某种规则
 *   matcher.replaceAll():将字符串中满足一定格式的全部替换
 content4 = Pattern.compile(regString41).matcher(content4).replaceAll(""); 
 */
```

## *五、String类中支持正则表达式的的常用方法*

```java
在String类中使用正则表达式
功能1、替换功能 repalceAll(String regex,String replacement):将正则表达式所对应格式的字符串进行替代
功能2、判断功能 matches(String regex):判断字符是否满足正则表达式格式，返回判断结果。（注：matches自带定位符^$）
功能3、分割功能 split(String regex):将字符串按照某个间隔符进行分割，分割成字符串数组


/**
 * 在String类中使用正则表达式
 */
public class RegExp11 {
    public static void main(String[] args) {
        /**
         * 功能1、替换功能 repalceAll(String regex,String replacement):将正则表达式所对应格式的字符串进行替代
         * 将下文中JDK1.3和JDK1.4替换为JDK
         */
        String content1="2000年5月，JDK1.3、JDK1.4和J2SE1.3相继发布，几周后其获得了Apple公司Mac OS X的工业标准的支持。2001年9月24日，J2EE1.3发布。" +
                "2002年2月26日，J2SE1.4发布。自此Java的计算能力有了大幅提升，与J2SE1.3相比，其多了近62%的类和接口。在这些新特性当中，" +
                "还提供了广泛的XML支持、安全套接字（Socket）支持（通过SSL与TLS协议）、全新的I/OAPI、正则表达式、日志与断言。2004年9月30日，J2SE1.5发布" +
                "，成为Java语言发展史上的又一里程碑。为了表示该版本的重要性，" +
                "J2SE 1.5更名为Java SE 5.0（内部版本号1.5.0），代号为“Tiger”，Tiger包含了从1996年发布1.0版本以来的最重大的更新，" +
                "其中包括泛型支持、基本类型的自动装箱、改进的循环、枚举类型、格式化I/O及可变参数。";
        content1 = content1.replaceAll("JDK1\\.(?:3|4)", "JDK");
        System.out.println(content1);
        /**
         * 功能2、判断功能 matches(String regex):判断字符是否满足正则表达式格式，返回判断结果
         * 验证一个手机号，要求必须以138|139开头的 11位数字
         */
        String content2="13988889999";
        if (content2.matches("^13(?:8|9)\\d{8}$")) {
            System.out.println("满足要求");
        }
        else {
            System.out.println("不满足要求");
        }
        /**
         * 功能3、分割功能 split(String regex):将字符串进行分割，分割成字符串数组
         * 要求按照# 或者- 或者~ 或者数字 来分割字符串
         */
        System.out.println("==========");
        String content3="hello#abc-jack12smith~北京";
        String[] split = content3.split("#|-|~|\\d+");
        for (String s :
                split) {
            System.out.println(s);
        }
    }
}

```

## *六、正则表达式应用*

```java
/**
 * 正则表达式的综合应用
 *  \\d：匹配数字0-9
 *  \\w：匹配字母以及数字和下划线
 *  \\s：匹配非空字符（包括空格、换行符）
 *  \\un：匹配n，其中n为四位数字的unicode字符
 *  汉字匹配：[\u0391-\uffe5]+$
 *  ^：指定起始字符
 *  $：指定结束字符
 */
```

```java
验证是否为汉字  
验证为邮政编码
验证为qq号码
验证手机号码
验证复杂的url
/**
         * 1、验证电子邮件是否合法：
         *      （1）、只能有一个@
         *      （2）、@前面是用户名，可以是a-z A-Z 0-9 _-字符
         *      （3）、@后面是域名，并且域名只能是英文字母，比如sohu.com 或者tsinghua.org.cn
         */
    	String content1="29-_2asd149@tsinghua.org.cn";
        String regStr1="^[\\w-]+@([a-zA-Z]+\\.)+[a-zA-Z]+$";
        //注：matches方法为整体匹配，不用添加^$定位符

         /**
         * 2、验证是不是整数或者小数
         *  注：此题要考虑正数和负数
         *  如：123 -345 34.89 -87.9 -0.01 0.45等
         *  特殊情况如：  0开头：    0.89
         *             非零数字开头   0023.9--->23.9
         *             正号开头：    +1.36
         */
    	String content2="+0.23";
        String regStr2="^[+-]?([1-9]\\d*|0)(\\.\\d+)?$";
         /**
         * 3、对一个url进行解析
         * 如：http://www.sohu.com:8080/abc/index.html
         *  （1）、要求得到协议是什么  http
         *  （2）、域名  www.sohu.com
         *  （3）、端口是什么 8080
         *  （4）、文件名是什么 index.html
         */
    //法一：分割字符串得到
        String content3="http://www.sohu.com:8080/abc/index.html";
        String regStr3="(://)|:|/(.+/)?";
        String[] split = content3.split(regStr3);
        for (String str :
                split) {
            System.out.println(str);
        }
        //法二：分组得到
        System.out.println("========");
        String regStr31="^([a-zA-Z]+)://([a-zA-Z.]+):(\\d+)[\\w-/]*/([a-zA-Z.]+)$";
        Matcher matcher = Pattern.compile(regStr31).matcher(content3);
```

```java


import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegExp08_example {
    public static void main(String[] args) {
        String content="www.bilibili.com/video/BV11R4y1L7Gk?spm_id_from=333.851.b_7265636f6d6d656e64.3";
        //验证是否为汉字
        String regString1="^[\u0391-\uffe5]+$";

        //验证为邮政编码
        //要求：是1-9开头的一个六位数，如：12890
        String regString2="^[1-9]\\d{5}$";

        //验证为qq号码
        //要求：是一个1-9开头的一个（5-10）位数
        String regString3="^[1-9]\\d{4,9}$";

        //验证手机号码
        //要求：必须以13，14，15，18 开头的11位数字
        String regString4="^1[3458]\\d{9}$";//注意：[]匹配一个字符,不能写[13|14|15|18]，括号中仅能表示一个，如果要多加得用限定符

        //验证复杂的url
        //先确定url的开头部分 https：// |http：//
        //通过([\\w-]+\\.)+[\\w-]+  来匹配www.baidu.com
        //通过(\\/[\w-#?=&/%.]*)?$  来匹配最后的字符，注意：[]中的内容全部均不用转意
        String regString5="^((https?)://)?([\\w-]+\\.)+[\\w-]+(\\/[\\w-#?=&/%.]*)?$";

        Pattern pattern = Pattern.compile(regString5);
        Matcher matcher = pattern.matcher(content);
        if(matcher.find()){
            System.out.println("匹配成功");
        }
        else {
            System.out.println("匹配失败");
        }

        //这里如果使用pattern的matches 整体匹配 更加简洁，自动从头开始检验,省略了^$，而find方法不是
        System.out.println(Pattern.matches(regString5,content));
    }

}
```