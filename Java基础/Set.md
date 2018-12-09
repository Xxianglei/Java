## Set

>一个不包含重复元素并且无序的 collection集合。它是一个接口。

 无序 + 唯一

**基本方法**：

![](https://i.imgur.com/or8cBoj.png)


![](https://i.imgur.com/Y3i6U1T.png)

## HashSet 


* HashSet类概述
	* 不保证 set 的迭代顺序
	* 特别是它不保证该顺序恒久不变。
* HashSet如何保证元素唯一性
	* 底层数据结构是哈希表(元素是链表的数组)
	* 哈希表依赖于哈希值存储
	* 添加功能底层依赖两个方法：
		* int hashCode()
		* boolean equals(Object obj)


**源码：**

	interface Collection {
		...
	}
	
	interface Set extends Collection {
		...
	}
	
	class HashSet implements Set {
		private static final Object PRESENT = new Object();
		private transient HashMap<E,Object> map;
		
		public HashSet() {
			map = new HashMap<>();
		}
		
		public boolean add(E e) { //e=hello,world
	        return map.put(e, PRESENT)==null;
	    }
	}
	
	class HashMap implements Map {
		public V put(K key, V value) { //key=e=hello,world
		
			//看哈希表是否为空，如果空，就开辟空间
	        if (table == EMPTY_TABLE) {
	            inflateTable(threshold);
	        }
	        
	        //判断对象是否为null
	        if (key == null)
	            return putForNullKey(value);
	        
	        int hash = hash(key); //和对象的hashCode()方法相关
	        
	        //在哈希表中查找hash值
	        int i = indexFor(hash, table.length);
	        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
	        	//这次的e其实是第一次的world
	            Object k;
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
	
	
	hs.add("hello");
	hs.add("world");
	hs.add("java");
	hs.add("world");

	步骤：
		首先比较哈希值
		如果相同，继续走，比较地址值或者走equals()
		如果不同,就直接添加到集合中	
	 按照方法的步骤来说：	
		先看hashCode()值是否相同
		 	相同:继续走equals()方法
		  		返回true：	说明元素重复，就不添加
		 		返回false：说明元素不重复，就添加到集合
		  	不同：就直接把元素添加到集合
		 如果类没有重写这两个方法，默认使用的Object()。一般来说不会相同。
		 而String类重写了hashCode()和equals()方法，所以，它就可以把内容相同的字符串去掉。只留下一个