>众所周知对于一个Java程序员,一定程度的JVM知识学习是需要的,也是重要的.在Java里面内存是由JVM自动管理的,所以一旦出现内存泄漏或溢出的问题，不了解JVM的内存结构和各个内存区域的工作职责，将对解决问题带来很大的麻烦.----你敢不学吗?😂

　　按照Java虚拟机规范的规定，JVM自动管理的内存将会包括以下几个运行时数据区域。
![在这里插入图片描述](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1547716212396&di=58f59858875e5597de75bacef8b6d8b2&imgtype=0&src=http%3A%2F%2Fimg.ctolib.com%2FuploadImg%2F20170914%2F20170914174843_972.jpg)

在此一一介绍图上各个模块的特点和功能.

**程序计数器**

程序计数器（Program Counter Register）是JVM中一块较小的内存区域，保存着当前线程执行的虚拟机字节码指令的内存地址。Java多线程的实现，其实是通过线程间的轮流切换并分配处理器执行时间的方式来实现的，在任何时刻，处理器都只会执行一个线程中的指令。在多线程场景下，为了保证线程切换回来后，还能恢复到原先状态，找到原先执行的指令，所以每个线程都会设立一个程序计数器，并且各个线程之间不会互相影响，程序计数器为"**线程私有**"的内存区域。

**虚拟机栈**

Java虚拟机也是**线程私有**的，它的生命周期和线程**相同**。虚拟机栈描述的是Java方法执行的内存模型：每个方法在执行的同时都会创建一个栈帧（Stack Frame）用于存储**局部变量表、操作数栈、动态链接、方法出口**等信息。

咱们常说的堆内存、栈内存中，栈内存指的就是虚拟机栈。局部变量表存放了编译期可知的各种基本数据类型（**8个基本数据类型**）、对象引用（地址指针）、returnAddress类型。

局部变量表所需的内存空间在编译期间完成分配。在运行期间**不会**改变局部变量表的大小。

这个区域规定了两种异常状态：如果线程请求的栈深度大于虚拟机所允许的深度，则抛出StackOverflowError异常；如果虚拟机栈可以动态扩展，在扩展是无法申请到足够的内存，就会抛出OutOfMemoryError异常。

![在这里插入图片描述](https://images2015.cnblogs.com/blog/1135193/201704/1135193-20170403165214707-670478299.png)
**本地方法栈**

本地方法栈与虚拟机栈所发挥作用**非常相似**，它们之间的区别不过是虚拟机栈为虚拟机**执行Java方法（也就是字节码）服务**，而本地方法栈则为虚拟机使用到的**native方法服务**。

**方法区**

方法区（Method Area）是用于存储**类结构信息**的地方，包括**常量池、静态变量、构造函数等**类型信息，类型信息是由类加载器在类加载时从类文件中提取出来的。
- 方法区同样存在垃圾收集，因为用户通过自定义加载器加载的一些类同样会成为垃圾，JVM会回收一个未被引用类所占的空间，以使方法区的空间达到最小。
- 方法区中还存在着常量池，常量池包含着一些常量和符号引用（加载类的连接阶段中的解析过程会将符号引用转换为直接引用）。
- 方法区是线程共享的。

**Java堆**

Java堆是Java虚拟机所管理的内存中最大的一块，是被所有**线程共享**的一块内存区域，在虚拟机启动时创建。此内存区域的唯一目的就是存放**对象实例**，这一点在Java虚拟机规范中的描述是：所有的对象实例以及数组都要在堆上分配。

Java堆是垃圾收集器管理的主要区域，因此也被成为“GC堆”（Garbage Collected Heap）。从内存回收角度来看java堆可分为：**新生代和老生代**。从内存分配的角度看，线程共享的Java堆中可能划分出多个线程私有的分配缓冲区（Thread Local Allocation Buffer，TLAB）。无论怎么划分，都与存放内容无关，无论哪个区域，存储的都是对象实例，进一步的划分都是为了更好的回收内存，或者更快的分配内存。

根据Java虚拟机规范的规定，java堆可以处于物理上不连续的内存空间中。当前主流的虚拟机都是可扩展的（通过 -Xmx 和 -Xms 控制）。如果堆中没有内存完成实例分配，并且堆也无法再扩展时，将会抛出OutOfMemoryError异常。

✍上代码
```java

public class JVMTest1 {
    public static void main(String[] agrs) {
        String a=new String("hello"); // ab为String的引用 存放在栈空间里,分别指向不同的实例也就是不同的地址空间.
        String b=new String("hello");
        String c="hello"; //cd指向常量池的同一内空间
        String d="hello";

        System.out.println(a==b); //false
        System.out.println(c==d);  //ture
        System.out.println(a==d);  //false
    }

}

```

图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190117145529619.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE1ODMzMTY=,size_16,color_FFFFFF,t_70)
说明:
对于通过new产生一个字符串（假设为”hello”）时，会先去常量池中查找是否已经有了”hello”对象，如果没有则在常量池中创建一个此字符串对象，然后堆中再创建一个常量池中此”hello”对象的拷贝对象。

引出:
String s = new String(“xyz”);产生几个对象？一个或两个，如果常量池中原来没有”xyz”,就是两个。

✍上代码
```java
public class JVMTest2 {
    public static void main(String[] args) {
        People people=new People("xianglei");
        people.show();
    }
}

class People {
    String name;
    public People(String name){
        this.name=name;
    }
    public void show(){
        System.out.println(name);
    }
}
```
解析:

* 用户创建了一个People对象，运行时JVM首先会去**方法区**寻找该对象的类型信息，没有则使用**类加载器**classloader将People.class字节码文件加载至内存中的方法区，并将People类的类型信息存放至方法区。

* 接着JVM在**堆中**为新的People实例**分配内存空间**，这个实例持有着指向方法区的People类型信息的**引用**，引用指的是类型信息在方法区中的内存地址。

* 在此运行的JVM进程中，会首先起一个线程跑该用户程序，而创建线程的同时也创建了一个虚拟机栈，虚拟机栈用来跟踪线程运行中的一系列方法调用的过程，每调用一个方法就会创建并往栈中压入一个栈帧，栈帧用来存储方法的参数，局部变量和运算过程的临时数据。上面程序中的people是对People的引用，就存放**于栈中**，并持有指向堆中People实例的内存地址。

* JVM根据people引用持有的堆中对象的**内存地址**，定位到**堆中**的People实例，由于堆中实例持有指向**方法区**的People类型信息的引用，从而获得show()方法的字节码信息，接着执行show()方法包含的指令。

people栈(内存地址)-->People(堆)类信息的引用-->方法区(获得show方法)

----
### 总结
![](https://i.imgur.com/gYOrBvp.png)
* 方法区间:类的信息,常量,静态 (共享)
* **堆内存**:new出来的对象,数组等 (共享)
* **Java栈**:基本数据类型(8种),局部变量等  (线程独立)
* 本地方法栈:理解为调用外部语言的接口