---
layout: post
title: "Java中的常见细节问题"
date: 2017-04-17 21:00:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Java
meta:
  _edit_last: '1'
---

### 1.HashTable与HashMap区别
* 1.HashTable线程安全,HashMap线程不安全,HashMap要线程安全可以使用`HashMap map = Collection.synchronized(new HashMap<Integer>()).`
* 2.HashTable不能有null的key,HashMap可以有.

### 2.TreeSet与TreeMap的底层实现
它们都是用红黑树实现的, 它们的Key都是有序的.
红黑树是一种二叉搜索树, 但是是平衡的. 它有如下性质:

* 根节点是黑色的.
* 红色节点的左右节点都是黑色的.
* 叶节点都是null, 且叶节点都是黑色的.
* 节点非黑即红.
* 任意节点到其子叶节点路径中黑色节点的数量都是相同的.

因此,如果黑色高度为N,则根节点到叶节点的距离最小为N,最大为2 * (N - 1).
且最大路径情况为黑红交替,最小路径情况为纯黑..

### 3.Java重载Overload和重写Override区别
重载是同一个类中,同一函数名但参数不同,调用时根据参数来区分调用具体哪个方法,也就是静态多态性

重写是子类继承基类,函数名相同,参数也相同,但是把这个方法重写,也就是动态多态性.

### 4.Java动态绑定
在运行时，java虚拟机会根据实际创建的对象类型决定使用那个方法(重写).

<!--more-->

### 5.static什么意思,能否重写static方法
static是静态的意思,一个类即使未实例化,它的static变量和方法也会被初始化.不能重写static方法,static是静态绑定的,而重写是动态绑定的.

### 6.Java自动拆装箱什么意思
Integer到int,Double到double的转化就是自动拆箱,反之是自动装箱.

### 7.Java中抽象类和接口的区别
* 抽象类只能继承一个,但是接口可以implements多个.
* 接口中的全部方法都是要具体实现的,但是抽象类中的方法可以根据定义abstract与否来决定是否完成这个方法的实现.
* 接口中的变量都是默认final的,不可改变,但是抽象类中可以自行定义.

### 8.Java中进程和线程的区别是什么

### 9.Java中volatile关键字有什么作用
volatile有两个语义.
* 第一个是volatile修饰的变量对所有线程都有可见性,也就是变量被更改后会立刻更新主内存,同时工作内存调用该变量时也会立刻从主内存更新到工作内存.
* 第二个是禁止指令重排序优化.

但是它依然无法完全保证同步,因为当两个线程同时在机器码中执行到了某个改变volatile修饰的变量时,它依然会出问题.

### 10.Java内存模型(JMM)中原子性操作有哪些
read,load,assign,use,store,write

### 11.Java中volatile和synchronized的区别
* volatile是保证可见性,也就是说有更新主内存和工作内存的过程.而synchronized是保证当前只有一个线程对该部分进行访问.
* volatile仅有可见性,而synchronized有可见性和原子性.
* volatile不会造成线程阻塞,而synchronized有可能会.
* volatile是作用于变量的,而synchronized还可以作用于方法.

### 12.Java中lock和synchronized区别
synchronized是JVM层面的,lock是程序层面的,需要开发者定义lock与unlock.

### 13.Java内存回收算法

* 标记-清除法,标记要回收的对象,然后清除它们,但效率太低,还有内存碎片.
* 复制法,将内存分为两块,每次只用一块,回收时将仍然存留的按顺序复制到另一块内存.浪费内存.
* 标记-整理法,同样是标记要回收的对象,但是清除后对其进行整理,按顺序将留下的部分向前整理.
* 分代收集算法,就是按照新生代和老年代来采用不同的回收算法,新生代用复制法,老年代用标记清除法或标记整理法.

### 14.Java内存区域组成
堆,虚拟机栈,方法区,程序计数器

堆是线程共享的,用来存放对象实例.

虚拟机栈是线程独占的,用于存储局部变量表,动态链接,方法出口等信息.

方法区是线程共享的,用来存储编译时的静态变量,类信息,常量和编译后的代码等.

程序计数器存储的是下一条指令的地址.

### 15.对方法区中类进行垃圾回收的条件是什么
* 该类在堆中没有任何实例
* 加载该类的classLoader已经被回收
* 该类对应的Java.lang.class没有在任何地方被引用

### 16.虚拟机类加载过程
加载,连接(验证,准备,解析),初始化,其中解析可以在初始化后执行(动态链接).

加载:首先通过类的全限定名获取类的二进制字节流.然后将二进制字节流放到方法区形成相应数据结构.最后在堆中生成一个java.lang.class对象,最为方法区访问入口.

验证:四个验证过程,文件验证,元数据验证,字节码验证,符号引用验证.

准备:为类的静态变量分配内存,赋零值.

初始化:获取静态成员,设置静态成员,调用静态成员或者new一个类时会立即初始化这个类.

### 17.类加载器与类相同有无关系
有关系,只有相同的类加载器加载的class文件才是相同的,不然不同的加载器即使class文件相同加载后的类也是不同的.

### 18.类加载器种类
双亲委派模型:启动类加载器<-扩展类加载器<-应用程序加载器<-自定义加载器,当收到加载一个类的请求时,都会先委派给父加载器,当父加载器不能加载时才会考虑自己加载.

### 19.Java线程start和run的区别
start是启动一个线程，而直接调用run只是一个普通方法调用，并没有启动线程。

### TO BE CONTINUED！



