---
layout:       post
title:        "[JVM] HotSpot虚拟机之编译优化"
subtitle:     "浅析HotSpot之即时编译器、编译优化等"
date:         2018-11-01 22:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-22.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - god road
---

# 前言
之所以说Java是跨平台语言，是因为JAVA有"一次编译，到处执行"的特点。

C语言/C++语言的编译过程：把源代码编译生成机器语言，机器可以直接执行。

Java语言的编译过程：通过JVM把字节码翻译成机器语言，而不同平台安装不同版本的JVM即可编译成具有对应平台特性的机器语言。

# JAVA编译步骤
![](/img/2018/2018-11/compile-1.png)

根据完成任务不同，可以将编译器的组成部分划分为前端（Front End）与后端（Back End）。

## 前端编译

前端编译主要指与源语言有关但与目标机无关的部分，包括词法分析、语法分析、语义分析与中间代码生成。 
我们所熟知的`javac`的编译就是前端编译（）。除了这种以外，我们使用的很多IDE，如eclipse，idea等，都内置了前端编译器。主要功能就是把`.java`代码转换成`.class`代码。

**1、词法分析**
读取源代码，一个字节一个字节的读取，找出其中我们定义好的关键字（如java中的if else for等关键字，识别哪些if是合法的关键字，哪些不是），这就是词法分析器进行词法分析的过程，其结果是从源代码中找出规范化的Token流。
**2、语法分析**
通过语法分析器对词法分析后Token流进行语法分析，这一步检查这些关键字组合再一次是否符合java语言规范（如在if后面是不是紧跟着一个布尔判断表达式），词法分析的结果是形成一个符合java语言规范的抽象语法树。
**3、语义分析**
通过语义分析器进行语义分析。语义分析主要是将一些难懂的、复杂的语法转化成更加简单的语法，结果形成最简单的语法（如将foreach转换成for循环、注解等），最后形成一个注解过后的抽象语法树，这个语法树更为接近目标语言的语法规则。
**4、中间代码生成**
通过字节码生产器将经过注解的抽象语法树转化成符合jvm规范的字节码。 
该中间代码有两个重要的性质：
- 易于生成；
- 能够轻松地翻译为目标机器上的语言。
在Java中，`javac` 执行的结果就是得到一个字节码，而这个字节码其实就是一种中间代码。只有通过后端编译成对应平台的机器语言才能真正在机器上运行。

## 后端编译
后端编译主要指与目标机有关的部分，包括代码优化和目标代码生成等。

这部分编译主要是将 `.class `文件翻译成机器指令的编译过程。

其实后端编译也可以理解为后端解译，让`.class`文件的字节码解译成机器可以理解的语言。这里涉及到了JVM的解释器。

# JIT
后端编译过程中，JVM通过解释字节码将其翻译成对应的机器指令，逐条读入，逐条解释翻译。很显然，经过解释执行，其执行速度必然会比可执行的二进制字节码程序慢很多。这就是传统的JVM的解释器（Interpreter）的功能。为了解决这种效率问题，引入了 JIT 技术。

JAVA程序还是通过解释器进行解释执行，当JVM发现某个方法或代码块运行特别频繁的时候，就会认为这是`“热点代码”`（Hot Spot Code)。然后JIT会把部分“热点代码”`翻译`成本地机器相关的机器码，并进行`优化`，然后再把翻译后的机器码缓存起来，以备下次使用。

除了上面说到的`传统的JVM解释器(Interpreter)`，HotSpot虚拟机中还内置了两个JIT编译器：

- Client Complier（编译速度）
- Server Complier（编译质量）

分别用在客户端和服务端，目前主流的HotSpot虚拟机中默认是采用解释器与其中一个编译器直接配合的方式工作。

---

当 JVM 执行代码时，它并不立即开始编译代码。而是会做判断并作出选择。

这段代码本身是否在将来知会被执行一次？

- 是：没必要用JIT编译，直接用JVM解释器将.class字节码解释成机器语言并执行，反而更快

- 否：每次都采用JVM解释器解释执行太麻烦，也太浪费资源；JIT会将热点代码编译并优化后缓存起来，提升效率

OpenJDK HotSpot VM有两个不同的编译器，每个都有它自己的编译临界值：

- 客户端或C1编译器，它的编译临界值比较低，只是1500，这有助于减少启动时间。
- 服务端或C2编译器，它的编译临界值比较高，达到了10000，这有助于针对性能关键的方法生成高度优化的代码，这些方法由应用的关键执行路径来判定是否属于性能关键方法。

在机器上，执行 java -version 命令就可以看到自己安装的JDK中JIT是哪种模式:
 
![](/img/2018/2018-11/compile-1.png)

    无论是Client Complier还是Server Complier，解释器与编译器的搭配使用方式都是混合模式，即上图中的mixed mode
    
**HotSpot虚拟机为什么要使用解释器与编译器并存的架构?**

> 并存原因

尽管并不是所有的Java虚拟机都采用解释器与编译器并存的架构，但许多主流的商用虚拟机（如HotSpot），都同时包含解释器和编译器。解释器与编译器两者各有优势。

当程序需要迅速启动和执行的时候，解释器可以首先发挥作用，省去编译的时间，立即执行。
 
在程序运行后，随着时间的推移，编译器逐渐发挥作用，把越来越多的代码编译成本地代码之后，可以获取更高的执行效率。 

当程序运行环境中内存资源限制较大（如部分嵌入式系统中），可以使用解释器执行节约内存，反之可以使用编译执行来提升效率。此外，如果编译后出现“罕见陷阱”，可以通过逆优化退回到解释执行。

![](/img/2018/2018-11/compile-3.png)

## 编译的时间开销
> 
> 解释器的执行，抽象的看是这样的： 
> 
> 输入的代码 -> [ 解释器 解释执行 ] -> 执行结果 
> 
> 而要JIT编译然后再执行的话，抽象的看则是： 
> 
> 输入的代码 -> [ 编译器 编译 ] -> 编译后的代码 -> [ 执行 ] -> 执行结果

**说JIT比解释快，其实说的是“执行编译后的代码”比“解释器解释执行”要快，并不是说“编译”这个动作比“解释”这个动作快。**

JIT编译再怎么快，至少也比解释执行一次略慢一些，而要得到最后的执行结果还得再经过一个“执行编译后的代码”的过程。 

所以，**对“只执行一次”的代码而言，解释执行其实总是比JIT编译执行要快。**

 怎么算是“只执行一次的代码”呢？粗略说，下面两个条件同时满足时就是严格的“只执行一次”
 
 - 只被调用一次，例如类的构造器（class initializer，()）
 - 没有循环
 
 对只执行一次的代码做JIT编译再执行，可以说是得不偿失。 
 
 对只执行少量次数的代码，JIT编译带来的执行速度的提升也未必能抵消掉最初编译带来的开销。
 
 ## 编译的空间开销
 
 对一般的Java方法而言，编译后代码的大小相对于字节码的大小，膨胀比达到10x是很正常的。同上面说的时间开销一样，这里的空间开销也是，只有对执行频繁的代码才值得编译，如果把所有代码都编译则会显著增加代码所占空间，导致“代码爆炸”。
 
 这也就解释了为什么有些JVM会选择不总是做JIT编译，而是选择用解释器+JIT编译器的混合执行引擎。
 
 # 热点检测
 
要想触发JIT，首先需要识别出热点代码。目前主要的热点代码识别方式是热点探测（Hot Spot Detection），有以下两种：
- 基于采样的方式探测（Sample Based Hot Spot Detection)

    周期性检测各个线程的栈顶，发现某个方法经常出现在栈顶，就认为是热点方法。好处就是简单，缺点就是无法精确确认一个方法的热度。容易受线程阻塞或别的原因干扰热点探测。

- 基于计数器的热点探测（Counter Based Hot Spot Detection)
    
    采用这种方法的虚拟机会为每个方法，甚至是代码块建立计数器，统计方法的执行次数，某个方法超过阀值就认为是热点方法，触发JIT编译。
    
---

在HotSpot虚拟机中使用的是第二种——基于计数器的热点探测方法，因此它为每个方法准备了两个计数器：方法调用计数器和回边计数器。

1. 方法计数器。顾名思义，就是记录一个方法被调用次数的计数器。

    执行过程如下：判断是否已存在编译版本，如已存在，则执行编译版本；否则，方法计数器+1，判断两个计数器之和（注意：是方法计数器和回边计数器的和）是否超过方法计数器阈值，超过则向编译器提交编译请求，然后和不超过阈值情况下的处理方式一样，仍旧解释执行该方法。 

    PS：该计数器并不是绝对次数，而是相对的执行次数，即在一段时间内的执行次数，当超过一定的时间限度，若还是没有达到阈值，那么它的计数器会减少一半，此过程被称为热度衰减。 

![](/img/2018/2018-11/compile-4.png)

2. 回边计数器。用于统计方法中循环体代码的执行次数，字节码中遇到控制流向后跳转的指令称为“回边”。建立该计数器的目的就是为触发OSR（On StackReplacement）编译，即栈上替换。
   
    和方法计数器执行过程不同的是：当两个计数器之和超过阈值的时候，它向编译器提交OSR编译，并且调整回边计数器值，然后仍旧以解释方式执行下去。 
   
    PS：该计数器是绝对次数，没有热度衰减。 

![](/img/2018/2018-11/compile-5.png)

# JIT编译过程

![](/img/2018/2018-11/compile-6.png)

## 编译优化

JIT除了具有缓存的功能外，还会对代码做各种优化，例如：逃逸分析、锁消除、锁膨胀、方法内联、数据边界检查、空值检查消除、类型检测消除、公共子表达式消除等。

## 逃逸分析

逃逸分析(Escape Analysis)是一种可以有效减少Java 程序中同步负载和内存堆分配压力的跨函数全局数据流分析算法。通过逃逸分析，Java Hotspot编译器能够分析出一个新的对象的引用的使用范围从而决定是否要将这个对象分配到堆上。

逃逸分析的基本行为就是分析对象动态作用域：当一个对象在方法中被定义后，它可能被外部方法所引用，这种行为被称为方法逃逸。甚至还可能被外部线程访问，这种行为被称为线程逃逸。

{% highlight java %}
public static StringBuffer craeteStringBuffer(String s1, String s2) {
    StringBuffer sb = new StringBuffer();
    sb.append(s1);
    sb.append(s2);
    return sb;
}
{% endhighlight %}

StringBuffer sb是一个方法内部变量，上述代码中直接将sb返回，这样这个StringBuffer有可能被其他方法所改变，这样它的作用域就不只是在方法内部，虽然它是一个局部变量，称其逃逸到了方法外部

上述代码如果想要StringBuffer sb不逃出方法，可以这样写：

{% highlight java %}
public static String createStringBuffer(String s1, String s2) {
    StringBuffer sb = new StringBuffer();
    sb.append(s1);
    sb.append(s2);
    return sb.toString();
}
{% endhighlight %}

使用逃逸分析，编译器可以对代码做如下优化： 

1. 同步省略
    如果一个对象被发现只能从一个线程被访问到，那么对于这个对象的操作可以不考虑同步。
2. 将堆分配转化为栈分配
    如果一个对象在子程序中被分配，要使指向该对象的指针永远不会逃逸，对象可能是栈分配的候选，而不是堆分配。
3. 分离对象或标量替换
    有的对象可能不需要作为一个连续的内存结构存在也可以被访问到，那么对象的部分（或全部）可以不存储在内存，而是存储在CPU寄存器中。

在Java代码运行时，通过JVM参数可指定是否开启逃逸分析， `-XX:+DoEscapeAnalysis` ： 表示开启逃逸分析 `-XX:-DoEscapeAnalysis` ： 表示关闭逃逸分析 
从jdk 1.7开始已经默认开始逃逸分析，如需关闭，需要指定 -XX:-DoEscapeAnalysis

# 对象的栈上内存分配

在一般情况下，对象和数组元素的内存分配是在堆内存上进行的。但是随着JIT编译器的日渐成熟，很多优化使这种分配策略并不绝对。

JIT编译器就可以在编译期间根据逃逸分析的结果，来决定是否可以将对象的内存分配从堆转化为栈。

{% highlight java %}
public static void main(String[] args) {
    long a1 = System.currentTimeMillis();
    for (int i = 0; i < 1000000; i++) {
        alloc();
    }
    // 查看执行时间
    long a2 = System.currentTimeMillis();
    System.out.println("cost " + (a2 - a1) + " ms");
    // 为了方便查看堆内存中对象个数，线程sleep
    try {
        Thread.sleep(100000);
    } catch (InterruptedException e1) {
        e1.printStackTrace();
    }
}
private static void alloc() {
    User user = new User();
}
static class User {
}
{% endhighlight %}

其实代码内容很简单，就是使用for循环，在代码中创建100万个User对象。

我们在alloc方法中定义了User对象，但是并没有在方法外部引用他。也就是说，这个对象并不会逃逸到alloc外部。经过JIT的逃逸分析之后，就可以对其内存分配进行优化。

我们指定以下JVM参数并运行：

`-Xmx4G -Xms4G -XX:-DoEscapeAnalysis -XX:+PrintGCDetails -XX:+HeapDumpOnOutOfMemoryError`

在程序打印出 cost XX ms 后，代码运行结束之前，我们使用[jmap]命令，来查看下当前堆内存中有多少个User对象：

{% highlight java %}
➜  ~ jps
2809 StackAllocTest
2810 Jps
➜  ~ jmap -histo 2809
 num     #instances         #bytes  class name
----------------------------------------------
   1:           524       87282184  [I
   2:       1000000       16000000  StackAllocTest$User
   3:          6806        2093136  [B
   4:          8006        1320872  [C
   5:          4188         100512  java.lang.String
   6:           581          66304  java.lang.Class
{% endhighlight %}

从上面的jmap执行结果中我们可以看到，堆中共创建了100万个StackAllocTest$User实例。

在关闭逃避分析的情况下（-XX:-DoEscapeAnalysis），虽然在alloc方法中创建的User对象并没有逃逸到方法外部，但是还是被分配在堆内存中。

也就说，如果没有JIT编译器优化，没有逃逸分析技术，正常情况下就应该是这样的。即所有对象都分配到堆内存中。

接下来，我们开启逃逸分析，再来执行下以上代码。再来看看堆内存中有多少个User对象

{% highlight java %}
➜  ~ jps
709
2858 Launcher
2859 StackAllocTest
2860 Jps
➜  ~ jmap -histo 2859
 num     #instances         #bytes  class name
----------------------------------------------
   1:           524      101944280  [I
   2:          6806        2093136  [B
   3:         83619        1337904  StackAllocTest$User
   4:          8006        1320872  [C
   5:          4188         100512  java.lang.String
   6:           581          66304  java.lang.Class
{% endhighlight %}

从以上打印结果中可以发现，开启了逃逸分析之后（-XX:+DoEscapeAnalysis），在堆内存中只有8万多个 StackAllocTest$User 对象。

也就是说在经过JIT优化之后，堆内存中分配的对象数量，从100万降到了8万。

除了以上通过jmap验证对象个数的方法以外，还可以尝试将堆内存调小，然后执行以上代码，根据GC的次数来分析，也能发现，开启了逃逸分析之后，在运行期间，GC次数会明显减少。

正是因为很多堆上分配被优化成了栈上分配，所以GC次数有了明显的减少。

# 锁消除

通过对运行上下文的扫描，去除不可能存在共享资源竞争的锁，通过锁消除，可以节省毫无意义的请求锁时间。

{% highlight java %}
package com.winwill.lock;
public class TestLockEliminate {
    public static String getString(String s1, String s2) {
        StringBuffer sb = new StringBuffer();
        sb.append(s1);
        sb.append(s2);
        return sb.toString();
    }
    public static void main(String[] args) {
        long tsStart = System.currentTimeMillis();
        for (int i = 0; i < 1000000; i++) {
            getString("TestLockEliminate ", "Suffix");
        }
        System.out.println("一共耗费：" + (System.currentTimeMillis() - tsStart) + " ms");
    }
}
{% endhighlight %}

getString()方法中的StringBuffer数以函数内部的局部变量，作用于方法内部，不可能逃逸出该方法，因此他就不可能被多个线程同时访问，也就没有资源的竞争，但是StringBuffer的append操作却需要执行同步操作:

{% highlight java %}
@Override
public synchronized StringBuffer append(String str) {
    toStringCache = null;
    super.append(str);
    return this;
}
{% endhighlight %}

逃逸分析和锁消除分别可以使用参数-XX:+DoEscapeAnalysis和-XX:+EliminateLocks(锁消除必须在-server模式下)开启。使用如下参数运行上面的程序：

`-XX:+DoEscapeAnalysis -XX:-EliminateLocks`

结果： 一共耗费：233 ms
使用如下命令运行程序： `-XX:+DoEscapeAnalysis -XX:+EliminateLocks`
结果：一共耗费：192 ms

由上面的例子可以看出，关闭了锁消除之后，StringBuffer每次append都会进行锁的申请，浪费了不必要的时间，开启锁消除之后性能得到了提高。

# 公共子表达式消除

如果一个表达式E已经计算过了，并且从先前的计算到现在E中所有变量的值都没有发生变化，那么E的这次出现就成为了公共子表达式。 

对于这种表达式，没有必要花时间再对它进行计算，只需要直接用前面计算过的表达式结果代替E就可以了。

# 方法内联
  
该方法是针对Client而言的，方法调用本身是有代价的，要从常量池找到方法地址，然后保存当前栈帧状态，压入新栈帧启动调用过程，调用完弹出，并恢复调用者栈帧。

而在运行期，如果方法很频繁的执行，就会运行期把方法内联到调用者方法内部，减少频繁调用的开销。

`-XX:+PringInlining` 来查看方法内联信息，`-XX:MaxInlineSize=35` 控制编译后文件大小。

# 数据边界检查

jvm在数组访问过程中会检查访问的下标是否越界，这本来是一个为了安全性提供的功能，但是像下边这段代码，在数组访问过程中，每次都去校验，性能损耗也是很大的。

jvm在运行期做了优化，只用校验用来访问数组的起始下标在0到数组最大长度-1之内那么整个循环中就可以把数组的上下界检查消除掉，这可以节省很多次的条件判断操作。通过数据流分析实现。

{% highlight java %}
int [] arrs = new int[10000];
for(int i = 0;i < 10000;i++){
  arrs[i] = i;
}
{% endhighlight %}
  
# Java即时编译与C/C++编译对比

**劣势**
- Java即时编译是在运行时，故会占用用户程序的运行时间。而C/C++是静态编译为机器码的，完全不占用运行时间。
- Java运行时不能进行一些比较耗时的优化，故能做的优化也没有C那么多
- 多态选择频率远高于C，需要建立虚方法表。也正是多态的存在，使得编译优化难度远高于C。因为多态较难预测代码跳转分支。
- Java运行时可以加载新的类，如网络中的二进制流。这使得编译器无法看清程序全貌，全局优化很难进行。
- Java对象都是在堆上分配（除了class对象在方法区），而C/C++既可以在堆上，又可以在栈上。栈上5分配可以减轻垃圾回收压力，且速度远快于堆。

**优势**
- 可以进行性能监控，热点探测，分支频率预测，调用频率预测，从而有选择性的优化代码

# 摘录资料
《HotSpot实战》
[链接：深入浅出 JIT 编译器][1]
[链接：什么是即时编译（JIT）！？OpenJDK HotSpot VM剖析][2]
[链接：深入分析Java的编译原理-HollisChuang's Blog][3]
[链接：对象和数组并不是都在堆上分配内存的。-HollisChuang's Blog][4]

[1]:https://www.ibm.com/developerworks/cn/java/j-lo-just-in-time/index.html
[2]:http://www.infoq.com/cn/articles/OpenJDK-HotSpot-What-the-JIT
[3]:http://www.hollischuang.com/archives/2322
[4]:http://www.hollischuang.com/archives/2398