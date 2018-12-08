>开始一个java基础知识的复习和一个java源码的阅读了，其实挺苦逼的，一天天磨磨唧唧的快到复习周了。白天复习晚上看代码👼。庆幸的是我在学了那么多回来看基础往往邮一种豁然开朗的感觉，还算能给我一点点短暂的快感吧！我打算是先从java基础开刀，逐渐到java源码再到设计模式、最后再到SSM框架源码解析。又是一段苦逼的日子咯。😤

## Object
---
>Object 是类层次结构的根类。俗称"所以类的爸爸"，每个类都使用 Object 作为超类。所有对象（包括数组）都实现这个类的方法。 它是从JDK1.0版本开始引入。

咱们定义类的时候通常都懒得写这个类的原因是，这些类是默认继承Object类的，当然你也可以写extends object。

#### 常用方法：
>看上去挺乱遭的，咱们先说几个常用的方法吧，图片里面有一些方法我会在后面的文章细说，因为挺重要的。

![在这里插入图片描述](https://images0.cnblogs.com/i/426802/201404/262136225915517.jpg)

* public int hashCode()
	* 不用对象的哈希码不同，这一般是通过将该对象的内部地址转换成一个整数来实现的。**哈希码不是地址值**，是由地址值根据哈希算法换算出来的一个值。当然你可以理解为地址值。
* public final Class getClass()
	* 返回此时运行的类。返回的是一个**Class**，通过getName（）返回该类的包名及其类名。
* public String toString()
	* 返回该对象的字符串表示。但是直接返回的值没有多大的意义。默认不重写则返回类名地址等信息。建议子类重写此方法（所有成员变量值作为返回即可）直接输出对象时等于调用的对象的toString（）方法。
	* 原理：getClass().getName() + '@' + Integer.toHexString(hashCode())====>cn.xianglei.student@42552c
	* toHexString（）把一个整数转换成一个十六进制表示的字符串

* public boolean equals(Object obj)
	* 指示其他某个对象是否与此对象“相等”。
	* equals（）方法几个特性：
		* **自反性**：对于任何非空引用值 x，x.equals(x) 都应返回 true。 
		* **对称性**：对于任何非空引用值 x 和 y，当且仅当 y.equals(x) 返回 true 时，x.equals(y) 才应返回 true。 
		* **传递性**：对于任何非空引用值 x、y 和 z，如果 x.equals(y) 返回 true，并且 y.equals(z) 返回 true，那么 x.equals(z) 应返回 true。 
		* **一致性**：对于任何非空引用值 x 和 y，多次调用 x.equals(y) 始终返回 true 或始终返回 false，前提是对象上 equals 比较中所用的信息没有被修改。 
		* 对于任何非空引用值 x，x.equals(null) 都应返回 false。


**注意：** equals方法默认情况下比的是**地址值**。通常我们需要重写他。当然这里不要和String类的equals方法混淆了，那个equal比的是值。

>我们需要重写他是一般用来比较类的成员变量值是否相同。当然如果有需求的话。

* 基础写法：

![](https://i.imgur.com/miA9weP.png)

* 优化写法：

 ![](https://i.imgur.com/RIOIys5.png)

友情提示：equals方法当然也可以自动生成，我就不多说了。

>==和equals方法的比较

![](https://i.imgur.com/tglUAWQ.png)

* protected void finalize()
	*  当垃圾回收器确定不存在对该对象的更多引用时，由对象的垃圾回收器调用此方法。用于垃圾回收，但是人工无法确定对象什么时候回收。这个东西面试必问，后面我专门开一篇文章来写 🙋
* protected Object clone()
	* 创建并返回此对象的一个副本。返回的是Object，通常需要重写。

		Object obj=s.clone();

		Student s2=(Student)obj;  

		此时会报错，Studnet必须添加Cloneable这个标记接口。告诉我们这个类可以实现对象的复制了。

浅克隆：----->当然还有一个深克隆，面试常问，后面开新文章细说。打个标记  🙋

![](https://i.imgur.com/I1DFlH3.png)

		林青霞---27
		刘意---30
		林青霞---27
		
结果就这样，如果不清除为什么是这个结果就问我吧，反正我知道为什么，哈哈😄



------

## 总结

复习篇就这样拉开序幕了，如果此时的你正在学习java知识，获取对你有一定的帮助哦！😼😼😼当然欢迎大家关注交流，要是有大佬来讨论一波那就更好了。我怕我这个菜鸟菜久了会漂起来😂😂😂