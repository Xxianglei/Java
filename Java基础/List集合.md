>List集合算是用的太多了，尤其是ArrayList，这一块的有些东西是需要去看一点源码的，这样理解更加深入。目前我还是做个基础的总结，先打个标记源码后面再去分析吧。

>
## List
---
>List 组件为用户提供了一个可滚动的文本项列表。可设置此 list，使其允许用户进行单项或多项选择。


### List集合的特点：

* **有序的** collection（也称为序列）。此接口的用户可以对列表中每个元素的**插入位置进行精确地控制**。用户可以根据元素的整数**索引**（在列表中的位置）访问元素，并搜索列表中的元素。与 set 不同，列表通常允许**重复**的元素。

### 基础方法

![](https://i.imgur.com/SLuetvL.png)

### 特有方法

* void add(int index,E element) 
	* 在指定位置添加一个元素
* E remove(int index)
	* 根据所以删除元素，返回删除的元素
* E get(int index)
	* 获取指定位置的元素
* E set(int index,E element)
	* 根据索引修改元素，返回被修改的元素
* ListIterator listIterator()
	* 列表迭代器是List集合**特有**的迭代器，但是是继承了Iterator。
	* boolean hasPrevious()
	* E previous()

### 集合遍历

基础方法 ：

![](https://i.imgur.com/BGIi8Ag.png)

列表迭代器：

>处理基础迭代器向后遍历的功能，其次还提供后逆向遍历的功能。**注意**:需要逆向遍历的时候你得保证指针在最后面，也就是得先实现正向遍历。所以感觉没多大意思。一般也没多大用。

![](https://i.imgur.com/xqm4e3O.png)

### 并发修改异常

>ConcurrentModificationException:当方法检测到对象的并发修改，但不允许这种修改时则抛出异常。


![](https://i.imgur.com/eRjuxvX.png)

* 产生的原因
	* 迭代器是依赖于集合存在的，在判断成功后集合中新加了元素，但是此时迭代器却不知道，所以就报错了。实质上意思就是迭代器遍历时集合无法修改元素的。
* 解决方案
	* 迭代器迭代元素，迭代器修改元素
		* add(E e)  将指定的元素插入列表（列表迭代器特有）。
![](https://i.imgur.com/MiW8WSw.png)

	* 普通for循环遍历
	
### List 三个子类的特点

	ArrayList:
		底层数据结构是数组，查询快，增删慢。
		线程不安全，效率高。
	Vector:(替代了)
		底层数据结构是数组，查询快，增删慢。
		线程安全，效率低。
	LinkedList:
		底层数据结构是链表，查询慢，增删快。
		线程不安全，效率高。
		
	List有三个儿子，我们到底使用谁呢?
		看需求(情况)。
		
## ArrayList 🙋
---
>ArrayList约等于List，没什么特殊的方法。里面新出现的方法很少用。

![](https://i.imgur.com/aUo6HdU.png)

三种遍历方式：

![](https://i.imgur.com/O3YdKFr.png)

**源码：**
```java /**
     * Appends the specified element to the end of this list.
     *
     * @param e element to be appended to this list
     * @return <tt>true</tt> (as specified by {@link Collection#add})
     */
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
      transient Object[] elementData; // non-private to simplify nested class access
```
可以看出ArrayList 底层实质上就是一个数组，数据类型使用了泛型来定义。
## Vector🙋
---
>Vector 是矢量队列，它是JDK1.0版本添加的类。继承于AbstractList，实现了List, RandomAccess, Cloneable这些接口。

* Vector 继承了AbstractList，实现了List；所以，它是一个队列，支持相关的添加、删除、修改、遍历等功能。
* Vector 实现了RandmoAccess接口，即提供了随机访问功能。RandmoAccess是java中用来被List实现，为List提供快速访问功能的。在Vector中，我们即可以通过元素的序号快速获取元素对象；这就是快速随机访问。
* Vector 实现了Cloneable接口，即实现clone()函数。它能被克隆。
* 和ArrayList不同，Vector中的操作是线程安全的。


特有功能:

* public void addElement(E obj) 添加内容
* public E elementAt(int index) 获取对应索引的值

	![](https://i.imgur.com/iICZW8N.png)

* public Enumeration elements() 返回此向量的枚举Enumeration是一个接口

![](https://i.imgur.com/1IJ7KjF.png)

## LinkedList🙋
---
>LinkedList 是一个继承于AbstractSequentialList的双向链表。它也可以被当作堆栈、队列或双端队列进行操作。
LinkedList 实现 List 接口，能对它进行队列操作。
LinkedList 实现 Deque 接口，即能将LinkedList当作双端队列使用。
LinkedList 实现了Cloneable接口，即覆盖了函数clone()，能克隆。
LinkedList 实现java.io.Serializable接口，这意味着LinkedList支持序列化，能通过序列化去传输。
LinkedList 是非同步的。

特有功能:

* public void addFirst(E e) 及 addLast(E e) 
	* 在开头/结尾添加 = add
* public E getFirst() 及 getLast()
	* 获取开头/结尾的元素
* public E removeFirst() 及 public E removeLast()
	* 删除开头/结尾的元素并返回

	![](https://i.imgur.com/5VFDizp.png)
	
   LinkedList实际上是通过双向链表去实现的。既然是双向链表，那么它的顺序访问会非常高效，而随机访问效率比较低。
    既然LinkedList是通过双向链表的，但是它也实现了List接口{也就是说，它实现了get(int location)、remove(int location)等“根据索引值来获取、删除节点的函数”}。LinkedList是如何实现List的这些接口的，如何将“双向链表和索引值联系起来的”？
    实际原理非常简单，它就是通过一个计数索引值来实现的。例如，当我们调用get(int location)时，首先会比较“location”和“双向链表长度的1/2”；若前者大，则从链表头开始往后查找，直到location位置；否则，从链表末尾开始先前查找，直到location位置。
    
#### 案例：

定义一个集合类，在这个集合类内部可以使用LinkedList模拟一个栈结构。

![](https://i.imgur.com/ApyfYHY.png)
![](https://i.imgur.com/tXd9OG4.png)

---

