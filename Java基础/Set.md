>主要说说Set的三个儿子：LinkedHashSet ，TreeSet，HashSet。这一块的内容不看源码就太无聊了。🕵
## Set
---
>一个不包含重复元素并且无序的 collection集合。它是一个接口。Set的实现基本上是依靠于相应的Map实现，所以要了解Set，只需要去分析相应的Map就可以了。

![在这里插入图片描述](https://img-blog.csdn.net/20180604203233253?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FzYWhpbm9rYXdh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**Set基本方法**：

![](https://i.imgur.com/or8cBoj.png)

这些方法没有什么特别之处，都是从Collection继承而来的基础方法，照着用就行了。

![](https://i.imgur.com/Y3i6U1T.png)

## HashSet 🙋
---
>必须得简单的看看源码了，翻开源码我们我可以清楚地看到HashSet中有一个变量map，它的类型是HashMap。不难想象，HashMap的键值是一个不可重复的集合，它刚好就可以当做是一个Set，也不难理解为什么要叫HashSet。简单粗暴。由此我们基本上可以想到哈希算法了，咋们接着往后看。
* HashSet类概述？
	* 不保证 set 的迭代顺序，就是**无序**。
	* 特别是它不保证该顺序恒久不变，因为计算出来的**哈希值**不是永远不变的。
* HashSet如何保证元素唯一性？
	* 底层数据结构是哈希表---->(元素是链表的数组)
	* 哈希表依赖于哈希值存储----->**哈希算法**
	* 添加（add）功能底层依赖两个方法：（核心）
		* int hashCode()
		* boolean equals(Object obj)

### 构造方法：


```java
	// HashSet一共有五个构造方法，先码出来
	 public HashSet() {
        map = new HashMap<>();
    }
	 public HashSet(Collection<? extends E> c) {
        map = new HashMap<>(Math.max((int) (c.size()/.75f) + 1, 16));
        addAll(c);
    }
     public HashSet(int initialCapacity, float loadFactor) {
        map = new HashMap<>(initialCapacity, loadFactor);
    } 
     public HashSet(int initialCapacity) {
        map = new HashMap<>(initialCapacity);
    }
     HashSet(int initialCapacity, float loadFactor, boolean dummy) {
        map = new LinkedHashMap<>(initialCapacity, loadFactor);
    }
    
```
再看增删改查：基本上是调用HashMap的相应操作方法。增加元素的时候，值传PRESENT给HashMap。

```java // 用来充当Map键值对中的值
private static final Object PRESENT = new Object();
public boolean contains(Object o) {
    return map.containsKey(o);
}
public boolean add(E e) {// 值传PRESENT
    return map.put(e, PRESENT)==null;
}
public boolean remove(Object o) {
    return map.remove(o)==PRESENT;
}
public boolean isEmpty() {
    return map.isEmpty();
}
public int size() {
    return map.size();
}
public Iterator<E> iterator() {
    return map.keySet().iterator();
}
public void clear() {
    map.clear();
}
```
从整体上面来看，HashSet是对HashMap的二次封装。关键还是在于HashMap的实现之上。


**源码解析：**

咋们暂时就看它是如何实现去重的：
```java
	class HashSet implements Set {
		private static final Object PRESENT = new Object();
		private transient HashMap<E,Object> map;
		
		public HashSet() {
			map = new HashMap<>();
		}
		// HashSet中的add方法实质上是将数据给HashMap处理的，我们调到HashMap看看。
		public boolean add(E e) { //e=hello,world
	        return map.put(e, PRESENT)==null;
	    }
	}
	// HashMap 实现了Map接口，这样看起来就明朗多了，实质上add调用了put方法，put方法是处理K-V键值对，在这里我们只是使用了K做判断。
	public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {
	
		public V put(K key, V value) { //key=e=hello,world
		
			//看哈希表是否为空，如果空，就开辟空间
	        if (table == EMPTY_TABLE) {
	            inflateTable(threshold);
	        }
	        
	        //判断对象是否为null
	        if (key == null)
	            return putForNullKey(value);
	        
	        int hash = hash(key); //我们拿对象生成一个**hash值**这里是重点
	        
	        //在哈希表中查找hash值
	        int i = indexFor(hash, table.length);
	        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
	        	//这次的e其实是第一次的world
	            Object k; //自己和自己的hash值比较... 
	            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
	                V oldValue = e.value;
	                e.value = value;
	                e.recordAccess(this);
	                return oldValue;
	                //走这里其实是没有添加元素
	            }
	        }
	
	        modCount++;
	        addEntry(hash, key, value, i); //把元素添加
	        return null;
	    }
	    
	    transient int hashSeed = 0;
	    
	    final int hash(Object k) { //k=key=e=hello,
	        int h = hashSeed;
	        if (0 != h && k instanceof String) {
	            return sun.misc.Hashing.stringHash32((String) k);
	        }
	
	        h ^= k.hashCode(); //这里调用的是对象的hashCode()方法
	
	        // This function ensures that hashCodes that differ only by
	        // constant multiples at each bit position have a bounded
	        // number of collisions (approximately 8 at default load factor).
	        h ^= (h >>> 20) ^ (h >>> 12);
	        return h ^ (h >>> 7) ^ (h >>> 4);
	    }
	}
	
	// 存值样例
	hs.add("hello");
	hs.add("world");
	hs.add("java");
	hs.add("world");
```
步骤总结：
* 首先比较哈希值
	* 如果相同，继续走，比较地址值或者走equals()
	* 如果不同,就直接添加到集合中	

 按照方法的步骤来说：	
* 先看hashCode()值是否相同
	* 相同:继续走equals()方法
		  		返回true：	说明元素重复，就不添加
		 		返回false：说明元素不重复，就添加到集合
	* 不同：就直接把元素添加到集合

如果类没有重写这两个方法，默认使用的Object()。一般来说不会相同。而String类重写了hashCode()和equals()方法，所以，它就可以把内容相同的字符串去掉。只留下一个。

总的来说使用HashSet可以做到一个很好的去重，前提是你得实现hashCode()  和 equals(Object obj)。一般来说没有特殊需求就**一键生成**。

![](https://i.imgur.com/KAjumta.png)

```java

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + age;
		result = prime * result + ((name == null) ? 0 : name.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj) //跟自己比
			return true;	
		if (obj == null) // 判空
			return false;
		if (getClass() != obj.getClass()) //判类型
			return false;
		Student other = (Student) obj;
		if (age != other.age) 
			return false;
		if (name == null) {
			if (other.name != null)
				return false;
		} else if (!name.equals(other.name))
			return false;
		return true;
	}
```

## LinkedHashSet 🙋
---
>具有可预知迭代顺序的 Set 接口的哈希表和链接列表实现。此实现与 HashSet 的不同之外在于，后者维护着一个运行于所有条目的双重链接列表。哈希表保证元素唯一性。链表保证元素有序（存储和取出是一致的）。

特点是元素有序唯一,为什么呢?

* 由链表保证元素有序
* 由哈希表保证元素唯一


```java
public LinkedHashSet(int initialCapacity, float loadFactor) {
    super(initialCapacity, loadFactor, true);
}
public LinkedHashSet(int initialCapacity) {
    super(initialCapacity, .75f, true);
}
public LinkedHashSet() {
    super(16, .75f, true);
}
public LinkedHashSet(Collection<? extends E> c) {
    super(Math.max(2*c.size(), 11), .75f, true);
    addAll(c);
}
```
## TreeSet🙋
----
>基于 TreeMap 的 NavigableSet 实现。使用元素的自然顺序对元素进行排序，或者根据创建 set 时提供的 Comparator接口(强行对某个对象 collection 进行整体排序 的比较函数) 进行排序，具体取决于使用的构造方法。TreeSet是非线程安全的。 

保证**有序**，且唯一。**底层数据结构是红黑树**。内部默认实现了集合元素的排序。

![](https://i.imgur.com/TesgRAm.png)

* 自然排序

	![](https://i.imgur.com/rnlo7HC.png)

使用无参构造是根据元素的自然顺序进行排序。或者使用Comparable接口，实现compareTo方法。默认情况return 0 只添加一个跟节点。
```java
	/*
	 * 如果一个类的元素要想能够进行自然排序，就必须实现自然排序接口
	 */
	public class Student implements Comparable<Student> {
		private String name;
		private int age;
	
		public Student() {
			super();
		}
	
		public Student(String name, int age) {
			super();
			this.name = name;
			this.age = age;
		}
	
		public String getName() {
			return name;
		}
	
		public void setName(String name) {
			this.name = name;
		}
	
		public int getAge() {
			return age;
		}
	
		public void setAge(int age) {
			this.age = age;
		}
	
		@Override
		public int compareTo(Student s) {
			// return 0; // 默认相同
			// return 1; 
			// return -1;
	
			// 这里返回什么，其实应该根据我的排序规则来做
			// 按照年龄排序,主要条件
			int num = this.age - s.age;
			// 次要条件
			// 年龄相同的时候，还得去看姓名是否也相同
			// 如果年龄和姓名都相同，才是同一个元素才选择忽略。
			int num2 = num == 0 ? this.name.compareTo(s.name) : num;
			return num2;
		}
	}

	/*
	 * TreeSet存储自定义对象并保证排序和唯一。
	 * 
	 * A:你没有告诉我们怎么排序
	 * 		自然排序，按照年龄从小到大排序
	 * B:元素什么情况算唯一你也没告诉我
	 * 		成员变量值都相同即为同一个元素
	 */
	public class TreeSetDemo2 {
		public static void main(String[] args) {
			// 创建集合对象
			TreeSet<Student> ts = new TreeSet<Student>();
	
			// 创建元素
			Student s1 = new Student("linqingxia", 27);
			Student s2 = new Student("zhangguorong", 29);
			Student s3 = new Student("wanglihong", 23);
			Student s4 = new Student("linqingxia", 27);
			Student s5 = new Student("liushishi", 22);
			Student s6 = new Student("wuqilong", 40);
			Student s7 = new Student("fengqingy", 22);
	
			// 添加元素
			ts.add(s1);
			ts.add(s2);
			ts.add(s3);
			ts.add(s4);
			ts.add(s5);
			ts.add(s6);
			ts.add(s7);
	
			// 遍历
			for (Student s : ts) {
				System.out.println(s.getName() + "---" + s.getAge());
			}
		}
	}
```
* 比较器排序
```java
		import java.util.Comparator;
		
		public class MyComparator implements Comparator<Student> {
	
		@Override
		public int compare(Student s1, Student s2) {
			// int num = this.name.length() - s.name.length();
			// this -- s1
			// s -- s2
			// 姓名长度
			int num = s1.getName().length() - s2.getName().length();
			// 姓名内容
			int num2 = num == 0 ? s1.getName().compareTo(s2.getName()) : num;
			// 年龄
			int num3 = num2 == 0 ? s1.getAge() - s2.getAge() : num2;
			return num3;
		}
	
	}

	或者使用匿名内部类，实现接口方法

	/*
	 * 需求：请按照姓名的长度排序
	 * 
	 * TreeSet集合保证元素排序和唯一性的原理
	 * 唯一性：是根据比较的返回是否是0来决定。
	 * 排序：
	 * 		A:自然排序(元素具备比较性)
	 * 			让元素所属的类实现自然排序接口 Comparable
	 * 		B:比较器排序(集合具备比较性)
	 * 			让集合的构造方法接收一个比较器接口的子类对象 Comparator
	 */
	public class TreeSetDemo {
		public static void main(String[] args) {
			// 创建集合对象
			// TreeSet<Student> ts = new TreeSet<Student>(); //自然排序
			// public TreeSet(Comparator comparator) //比较器排序
			// TreeSet<Student> ts = new TreeSet<Student>(new MyComparator());
	
			// 如果一个方法的参数是接口，那么真正要的是接口的实现类的对象
			// 而匿名内部类就可以实现这个东西
			TreeSet<Student> ts = new TreeSet<Student>(new Comparator<Student>() {
				@Override
				public int compare(Student s1, Student s2) {
					// 姓名长度
					int num = s1.getName().length() - s2.getName().length();
					// 姓名内容
					int num2 = num == 0 ? s1.getName().compareTo(s2.getName())
							: num;
					// 年龄
					int num3 = num2 == 0 ? s1.getAge() - s2.getAge() : num2;
					return num3;
				}
			});
	
			// 创建元素
			Student s1 = new Student("linqingxia", 27);
			Student s2 = new Student("zhangguorong", 29);
			Student s3 = new Student("wanglihong", 23);
			Student s4 = new Student("linqingxia", 27);
			Student s5 = new Student("liushishi", 22);
			Student s6 = new Student("wuqilong", 40);
			Student s7 = new Student("fengqingy", 22);
			Student s8 = new Student("linqingxia", 29);
	
			// 添加元素
			ts.add(s1);
			ts.add(s2);
			ts.add(s3);
			ts.add(s4);
			ts.add(s5);
			ts.add(s6);
			ts.add(s7);
			ts.add(s8);
	
			// 遍历
			for (Student s : ts) {
				System.out.println(s.getName() + "---" + s.getAge());
			}
		}
	}
```
**如何保证排序和唯一：**
>底层使用红黑树实现，保证唯一是通过节点对比，如果值相同则忽略。至于有序就是一个简单的平衡二叉树的中序遍历。

![](https://i.imgur.com/PffZq64.png)

**核心功能源码分析**

* 实质上是对TreeMap的再次封装。核心还是通过TreeMap的方法实现。

```java
public class TreeSet<E> extends AbstractSet<E>
    implements NavigableSet<E>, Cloneable, java.io.Serializable
{
    // NavigableMap对象
    private transient NavigableMap<E,Object> m;

    // TreeSet是通过TreeMap实现的，
    // PRESENT是键-值对中的值。
    private static final Object PRESENT = new Object();

    // 不带参数的构造函数。创建一个空的TreeMap
    public TreeSet() {
        this(new TreeMap<E,Object>());
    }

    // 将TreeMap赋值给 "NavigableMap对象m"
    TreeSet(NavigableMap<E,Object> m) {
        this.m = m;
    }

    // 带比较器的构造函数。
    public TreeSet(Comparator<? super E> comparator) {
        this(new TreeMap<E,Object>(comparator));
    }

    // 创建TreeSet，并将集合c中的全部元素都添加到TreeSet中
    public TreeSet(Collection<? extends E> c) {
        this();
        // 将集合c中的元素全部添加到TreeSet中
        addAll(c);
    }

    // 创建TreeSet，并将s中的全部元素都添加到TreeSet中
    public TreeSet(SortedSet<E> s) {
        this(s.comparator());
        addAll(s);
    }

    // 返回TreeSet的顺序排列的迭代器。
    // 因为TreeSet时TreeMap实现的，所以这里实际上时返回TreeMap的“键集”对应的迭代器
    public Iterator<E> iterator() {
        return m.navigableKeySet().iterator();
    }

    // 返回TreeSet的逆序排列的迭代器。
    // 因为TreeSet时TreeMap实现的，所以这里实际上时返回TreeMap的“键集”对应的迭代器
    public Iterator<E> descendingIterator() {
        return m.descendingKeySet().iterator();
    }

    // 返回TreeSet的大小
    public int size() {
        return m.size();
    }

    // 返回TreeSet是否为空
    public boolean isEmpty() {
        return m.isEmpty();
    }

    // 返回TreeSet是否包含对象(o)
    public boolean contains(Object o) {
        return m.containsKey(o);
    }

    // 添加e到TreeSet中
    public boolean add(E e) {
        return m.put(e, PRESENT)==null;
    }

    // 删除TreeSet中的对象o
    public boolean remove(Object o) {
        return m.remove(o)==PRESENT;
    }

    // 清空TreeSet
    public void clear() {
        m.clear();
    }

    // 将集合c中的全部元素添加到TreeSet中
    public  boolean addAll(Collection<? extends E> c) {
        // Use linear-time version if applicable
        if (m.size()==0 && c.size() > 0 &&
            c instanceof SortedSet &&
            m instanceof TreeMap) {
            SortedSet<? extends E> set = (SortedSet<? extends E>) c;
            TreeMap<E,Object> map = (TreeMap<E, Object>) m;
            Comparator<? super E> cc = (Comparator<? super E>) set.comparator();
            Comparator<? super E> mc = map.comparator();
            if (cc==mc || (cc != null && cc.equals(mc))) {
                map.addAllForTreeSet(set, PRESENT);
                return true;
            }
        }
        return super.addAll(c);
    }

    // 返回子Set，实际上是通过TreeMap的subMap()实现的。
    public NavigableSet<E> subSet(E fromElement, boolean fromInclusive,
                                  E toElement,   boolean toInclusive) {
        return new TreeSet<E>(m.subMap(fromElement, fromInclusive,
                                       toElement,   toInclusive));
    }

    // 返回Set的头部，范围是：从头部到toElement。
    // inclusive是是否包含toElement的标志
    public NavigableSet<E> headSet(E toElement, boolean inclusive) {
        return new TreeSet<E>(m.headMap(toElement, inclusive));
    }

    // 返回Set的尾部，范围是：从fromElement到结尾。
    // inclusive是是否包含fromElement的标志
    public NavigableSet<E> tailSet(E fromElement, boolean inclusive) {
        return new TreeSet<E>(m.tailMap(fromElement, inclusive));
    }

    // 返回子Set。范围是：从fromElement(包括)到toElement(不包括)。
    public SortedSet<E> subSet(E fromElement, E toElement) {
        return subSet(fromElement, true, toElement, false);
    }

    // 返回Set的头部，范围是：从头部到toElement(不包括)。
    public SortedSet<E> headSet(E toElement) {
        return headSet(toElement, false);
    }

    // 返回Set的尾部，范围是：从fromElement到结尾(不包括)。
    public SortedSet<E> tailSet(E fromElement) {
        return tailSet(fromElement, true);
    }

    // 返回Set的比较器
    public Comparator<? super E> comparator() {
        return m.comparator();
    }

    // 返回Set的第一个元素
    public E first() {
        return m.firstKey();
    }

    // 返回Set的最后一个元素
    public E first() {
    public E last() {
        return m.lastKey();
    }

    // 返回Set中小于e的最大元素
    public E lower(E e) {
        return m.lowerKey(e);
    }

    // 返回Set中小于/等于e的最大元素
    public E floor(E e) {
        return m.floorKey(e);
    }

    // 返回Set中大于/等于e的最小元素
    public E ceiling(E e) {
        return m.ceilingKey(e);
    }

    // 返回Set中大于e的最小元素
    public E higher(E e) {
        return m.higherKey(e);
    }

    // 获取第一个元素，并将该元素从TreeMap中删除。
    public E pollFirst() {
        Map.Entry<E,?> e = m.pollFirstEntry();
        return (e == null)? null : e.getKey();
    }

    // 获取最后一个元素，并将该元素从TreeMap中删除。
    public E pollLast() {
        Map.Entry<E,?> e = m.pollLastEntry();
        return (e == null)? null : e.getKey();
    }

    // 克隆一个TreeSet，并返回Object对象
    public Object clone() {
        TreeSet<E> clone = null;
        try {
            clone = (TreeSet<E>) super.clone();
        } catch (CloneNotSupportedException e) {
            throw new InternalError();
        }

        clone.m = new TreeMap<E,Object>(m);
        return clone;
    }

    // java.io.Serializable的写入函数
    // 将TreeSet的“比较器、容量，所有的元素值”都写入到输出流中
    private void writeObject(java.io.ObjectOutputStream s)
        throws java.io.IOException {
        s.defaultWriteObject();

        // 写入比较器
        s.writeObject(m.comparator());

        // 写入容量
        s.writeInt(m.size());

        // 写入“TreeSet中的每一个元素”
        for (Iterator i=m.keySet().iterator(); i.hasNext(); )
            s.writeObject(i.next());
    }

    // java.io.Serializable的读取函数：根据写入方式读出
    // 先将TreeSet的“比较器、容量、所有的元素值”依次读出
    private void readObject(java.io.ObjectInputStream s)
        throws java.io.IOException, ClassNotFoundException {
        // Read in any hidden stuff
        s.defaultReadObject();

        // 从输入流中读取TreeSet的“比较器”
        Comparator<? super E> c = (Comparator<? super E>) s.readObject();

        TreeMap<E,Object> tm;
        if (c==null)
            tm = new TreeMap<E,Object>();
        else
            tm = new TreeMap<E,Object>(c);
        m = tm;

        // 从输入流中读取TreeSet的“容量”
        int size = s.readInt();

        // 从输入流中读取TreeSet的“全部元素”
        tm.readTreeSet(size, s, PRESENT);
    }

    // TreeSet的序列版本号
    private static final long serialVersionUID = -2479143000061671589L;
}

--------------------------------------------------------------------------------------------------------------------------------------
	//重点看一个add是如何存放元素的，前面说了实质上是对TreeMap的调用
	
	class TreeMap implements NavigableMap {
		 public V put(K key, V value) {
	        Entry<K,V> t = root;
	        if (t == null) {
	            compare(key, key); // type (and possibly null) check
	            root = new Entry<>(key, value, null);
	            size = 1;
	            modCount++;
	            return null;
	        }
	        int cmp;
	        Entry<K,V> parent;
	        // split comparator and comparable paths
	        Comparator<? super K> cpr = comparator;
	        if (cpr != null) {
	            do {
	                parent = t;
	                cmp = cpr.compare(key, t.key);
	                if (cmp < 0)
	                    t = t.left;
	                else if (cmp > 0)
	                    t = t.right;
	                else
	                    return t.setValue(value);
	            } while (t != null);
	        }
	        else {
	            if (key == null)
	                throw new NullPointerException();
	            Comparable<? super K> k = (Comparable<? super K>) key;
	            do {
	                parent = t;
	                cmp = k.compareTo(t.key);
	                if (cmp < 0)
	                    t = t.left;
	                else if (cmp > 0)
	                    t = t.right;
	                else
	                    return t.setValue(value);
	            } while (t != null);
	        }
	        Entry<K,V> e = new Entry<>(key, value, parent);
	        if (cmp < 0)
	            parent.left = e;
	        else
	            parent.right = e;
	        fixAfterInsertion(e);
	        size++;
	        modCount++;
	        return null;
	    }
	}
	
	class TreeSet implements Set {
		private transient NavigableMap<E,Object> m;
		
		public TreeSet() {
			 this(new TreeMap<E,Object>());
		}
	
		public boolean add(E e) {
	        return m.put(e, PRESENT)==null;
	    }

	}
```	

小结：真正的比较是依赖于元素的 **compareTo()** 方法，而这个方法是定义在 Comparable里面的。所以，你要想重写该方法，就必须是先实现 **Comparable接口**。这个接口表示的就是自然排序。

---
## 总结

对于上面的内容，简单来说：眼看API+无脑操作。如果需要**排序**就用TreeSet，其他情况就用HashSet。🙃