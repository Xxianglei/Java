>Collection接口作为集合的一个根接口，它提供了对集合对象进行基本操作的通用接口方法，接口在Java 类库中有很多具体的实现。其意义是为各种具体的集合提供了最大化的统一操作方式。

## 接口 Collection

![](https://i.imgur.com/QQ4nVIo.png)

>为什么出现集合类？


面向对象语言对事物的体现都是以对象的形式，所以为了方便对多个对象的操作，Java就提供了集合类。作为一个容器类的变量。一般情况下的对象数组是无法适应变化的需求。

>数组和集合类同是容器，有何不同？


数组虽然也可以存储对象，但长度是固定的；集合长度是可变的。数组中可以存储基本数据类型且是同一种类型，集合可以存储不同类型的元素。特别注意的是:数组可以存基本类型也可以存储引用类型，而集合只能存引用类型也就是对象。

>集合类的特点?

* 集合只用于存储对象
* 集合长度是可变的
* 集合可以存储不同类型的对象

![](https://i.imgur.com/pqK8m5w.png)

### Collection基础功能

![](https://i.imgur.com/S348Ld5.png) 

常用功能：

1. 添加功能 
	1. add  添加一个元素
	2. addAll 添加一个集合的元素
2. 删除功能
	1. clear 移除所有元素
	2. remove 删除一个元素
	3. removeAll 一处多个元素只要有一个被移除了就返回true
3. 判断功能
	1. contains 判断集合中是否存在指定元素
	1. containsAll 判断集合中是否包含指定的集合元素 只有包含所有元素才返回true
	2. isEmpty 判断是否是空
4. 获取功能
	1. iterator 迭代器，集合的专用遍历方式
5. 长度功能
	1. size 返回元素个数  没有length方法
6. 交集功能
	1. retainAll 两个集合都有的元素，A对B做交集，最终结果保存在A中并不变，返回值表示A是否发生过改变。改变了就是true，不改变就是false
7. 把集合转换为数组
	1. toArray 把集合转成数组，可以实现集合的遍历

### 集合的遍历

* 集合转数组

![](https://i.imgur.com/xKyQ6Ac.png)

* 用迭代器遍历 

	hasNext：如果仍有元素可以迭代，则返回 true

	next：获取元素并且移动到下一个元素没有下一个元素就报错

	remove：从迭代器指向的 collection 中移除迭代器返回的最后一个元素

![](https://i.imgur.com/k14HUHU.png)

for 在循环结束时，迭代对象就被作为垃圾回收了，效率较好但是结构没有while明朗。建议还是用while。

### 迭代器原理分析

>迭代器为什么是一个接口？

一个集合通常需要一个遍历功能，如果存在一个判断功能，就不容易出错。集合的一个基础功能可以说为判断+遍历。但是每种集合的方式不太一样，我们就可以通过接口来处理。通过接口的实现类来实现不同集合的遍历。也就是多态的一种表现。

![](https://i.imgur.com/yPGrMrw.png)


```java
	public interface Inteator {
		boolean hasNext();
		Object next(); 
	}
	
	public interface Iterable {
	    Iterator iterator();
	}
	
	public interface Collection extends Iterable {
		Iterator iterator();
	}
	
	public interface List extends Collection {
		Iterator iterator();
	}
	
	public class ArrayList implements List {
		public Iterator iterator() {
	        return new Itr();
	    }
	    
	    private class Itr implements Iterator {
	    	public boolean hasNext() {}
			public Object next(){} 
	    }
	}
	
	
	Collection c = new ArrayList();
	c.add("hello");
	c.add("world");
	c.add("java");
	Iterator it = c.iterator();	 //new Itr();
	while(it.hasNext()) {
		String s = (String)it.next();
		System.out.println(s);
	}
	//编译看左边运行看右边，在运行的时候找的是实现类的方法，此时实现类返回一个new Itr()此类是一个重写了父类的三个迭代方法的内部类。
```

