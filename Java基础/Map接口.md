## Map接口

>将键映射到值的对象，一个映射不能包含重复的键，每个键最多只能映射到一个值


### Map接口和Collection接口的不同

* Map是双列的,Collection是单列的
* Map的键唯一,Collection的子体系Set是唯一的
* Map集合的数据结构值针对键有效，跟值无关
* Collection集合的数据结构是针对元素有效

### Map集合常用功能

* 添加功能
	* V put(K key,V value):添加元素。
		* 如果键是第一次存储，就直接存储键值对元素，返回null
 		* 如果键不是第一次存在，就用值把以前的值替换掉，**返回以前的值**
* 删除功能
	* V remove(Object key)：根据键删除键值对元素，并把值返回
* 判断功能
	* boolean containsKey(Object key)：判断集合是否包含指定的键
 	* 	boolean containsValue(Object value):判断集合是否包含指定的值
 	* 	boolean isEmpty()：判断集合是否为空
* 获取功能
	*   Set<Map.Entry<K,V>> entrySet():返回此映射中包含的映射关系的 Set 视图。
	* 	V get(Object key):根据键获取值
 	* 	Set<K> **keySet()**:获取集合中所有键的集合
 	* 	Collection<V> **values()**:获取集合中所有值的集合
* 长度功能
	* int size()：返回集合中的键值对的对数

### 代码实例

	public class MapDemo {
		public static void main(String[] args) {
			// 创建集合对象
			Map<String, String> map = new HashMap<String, String>();
	
			// 添加元素
			// System.out.println("put:" + map.put("文章", "马伊俐"));
			// System.out.println("put:" + map.put("文章", "姚笛"));
	
			map.put("邓超", "孙俪");
			map.put("黄晓明", "杨颖");
			map.put("周杰伦", "蔡依林");
			map.put("刘恺威", "杨幂");
	
			// void clear():移除所有的键值对元素
			// map.clear();
	
			// V remove(Object key)：根据键删除键值对元素，并把值返回
			// System.out.println("remove:" + map.remove("黄晓明"));
			// System.out.println("remove:" + map.remove("黄晓波"));
	
			// boolean containsKey(Object key)：判断集合是否包含指定的键
			// System.out.println("containsKey:" + map.containsKey("黄晓明"));
			// System.out.println("containsKey:" + map.containsKey("黄晓波"));
	
			// boolean isEmpty()：判断集合是否为空
			// System.out.println("isEmpty:"+map.isEmpty());			
			//int size()：返回集合中的键值对的对数
			System.out.println("size:"+map.size());
	
			// 输出集合名称
			System.out.println("map:" + map);
		}
	}

获取功能：
	
	public class MapDemo2 {
		public static void main(String[] args) {
			// 创建集合对象
			Map<String, String> map = new HashMap<String, String>();
	
			// 创建元素并添加元素
			map.put("邓超", "孙俪");
			map.put("黄晓明", "杨颖");
			map.put("周杰伦", "蔡依林");
			map.put("刘恺威", "杨幂");
	
			// V get(Object key):根据键获取值
			System.out.println("get:" + map.get("周杰伦"));
			System.out.println("get:" + map.get("周杰")); // 返回null
			System.out.println("----------------------");
	
			// Set<K> keySet():获取集合中所有键的集合
			Set<String> set = map.keySet();
			// 遍历Set 增强for 或者迭代器 反正就是Collection的遍历方式
			for (String key : set) {
				System.out.println(key);
			}
			System.out.println("----------------------");
	
			// Collection<V> values():获取集合中所有值的集合
			Collection<String> con = map.values();
			for (String value : con) {
				System.out.println(value);
			}
		}
	}

![](https://i.imgur.com/DOlW5vL.png)

Map集合的遍历：

>遍历思路一： A:获取所有的键-->	B:遍历键的集合，获取得到每一个键 -->C:根据键去找值。

示例：
	
		// 创建集合对象
		Map<String, String> map = new HashMap<String, String>();
	
		// 创建元素并添加到集合
		map.put("杨过", "小龙女");
		map.put("郭靖", "黄蓉");
		map.put("杨康", "穆念慈");
		map.put("陈玄风", "梅超风");
	
		// 遍历
		// 获取所有的键
		Set<String> set = map.keySet();
		// 遍历键的集合，获取得到每一个键
		for (String key : set) {
			// 根据键去找值
			String value = map.get(key);
			System.out.println(key + "---" + value);
		}


>遍历思路二： A:获取所有键值对对象的集合-->B:遍历键值对对象的集合，得到每一个键值对对象-->C:根据键值对对象获取键和值。

示例:

		// 创建集合对象
		Map<String, String> map = new HashMap<String, String>();

		// 创建元素并添加到集合
		map.put("杨过", "小龙女");
		map.put("郭靖", "黄蓉");
		map.put("杨康", "穆念慈");
		map.put("陈玄风", "梅超风");

		// 获取所有键值对对象的集合
		Set<Map.Entry<String, String>> set = map.entrySet();
		// 遍历键值对对象的集合，得到每一个键值对对象
		for (Map.Entry<String, String> me : set) {
			// 根据键值对对象获取键和值
			String key = me.getKey();
			String value = me.getValue();
			System.out.println(key + "---" + value);
		}

图示对比：

![](https://i.imgur.com/W8KGyIc.png)

## HashMap

>键是哈希表结构，可以保证键的唯一性.需要注意的是当键为类类型的时候，需要重写类的equals和hashcode方法.

![](https://i.imgur.com/DnvOrMa.png)

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
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
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

## LinkedHashMap

>Map 接口的哈希表和链接列表实现，具有可预知的迭代顺序。此实现与 HashMap 的不同之处在于，后者维护着一个运行于所有条目的双重链接列表。此链接列表定义了迭代顺序，该迭代顺序通常就是将键插入到映射中的顺序（插入顺序）。

![](https://i.imgur.com/4IQtPwh.png)

## TreeMap

>键是红黑树结构，可以保证键的排序和唯一性。无参构造则是自然排序。

示例：

	// 创建集合对象
		TreeMap<Student, String> tm = new TreeMap<Student, String>(
				new Comparator<Student>() {
					@Override
					public int compare(Student s1, Student s2) {
						// 主要条件
						int num = s1.getAge() - s2.getAge();
						// 次要条件
						int num2 = num == 0 ? s1.getName().compareTo(
								s2.getName()) : num;
						return num2;
					}
				});

		// 创建学生对象
		Student s1 = new Student("潘安", 30);
		Student s2 = new Student("柳下惠", 35);
		Student s3 = new Student("唐伯虎", 33);
		Student s4 = new Student("燕青", 32);
		Student s5 = new Student("唐伯虎", 33);

		// 存储元素
		tm.put(s1, "宋朝");
		tm.put(s2, "元朝");
		tm.put(s3, "明朝");
		tm.put(s4, "清朝");
		tm.put(s5, "汉朝");

		// 遍历
		Set<Student> set = tm.keySet();
		for (Student key : set) {
			String value = tm.get(key);
			System.out.println(key.getName() + "---" + key.getAge() + "---"
					+ value);
		}

统计字符串中每个字符出现次数：

>图解思路

![](https://i.imgur.com/zxkv77S.png)

>示例代码

	/*
	 * 需求 ："aababcabcdabcde",获取字符串中每一个字母出现的次数要求结果:a(5)b(4)c(3)d(2)e(1)
	 * 
	 * 分析：
	 * 		A:定义一个字符串(可以改进为键盘录入)
	 * 		B:定义一个TreeMap集合
	 * 			键:Character
	 * 			值：Integer
	 * 		C:把字符串转换为字符数组
	 * 		D:遍历字符数组，得到每一个字符
	 * 		E:拿刚才得到的字符作为键到集合中去找值，看返回值
	 * 			是null:说明该键不存在，就把该字符作为键，1作为值存储
	 * 			不是null:说明该键存在，就把值加1，然后重写存储该键和值
	 * 		F:定义字符串缓冲区变量
	 * 		G:遍历集合，得到键和值，进行按照要求拼接
	 * 		H:把字符串缓冲区转换为字符串输出
	 * 
	 * 录入：linqingxia
	 * 结果：result:a(1)g(1)i(3)l(1)n(2)q(1)x(1)
	 */
	public class TreeMapDemo {
		public static void main(String[] args) {
			// 定义一个字符串(可以改进为键盘录入)
			Scanner sc = new Scanner(System.in);
			System.out.println("请输入一个字符串：");
			String line = sc.nextLine();
	
			// 定义一个TreeMap集合
			TreeMap<Character, Integer> tm = new TreeMap<Character, Integer>();
			
			//把字符串转换为字符数组
			char[] chs = line.toCharArray();
			
			//遍历字符数组，得到每一个字符
			for(char ch : chs){
				//拿刚才得到的字符作为键到集合中去找值，看返回值
				Integer i =  tm.get(ch);
				
				//是null:说明该键不存在，就把该字符作为键，1作为值存储
				if(i == null){
					tm.put(ch, 1);
				}else {
					//不是null:说明该键存在，就把值加1，然后重写存储该键和值
					i++;
					tm.put(ch,i);
				}
			}
			
			//定义字符串缓冲区变量
			StringBuilder sb=  new StringBuilder();
			
			//遍历集合，得到键和值，进行按照要求拼接
			Set<Character> set = tm.keySet();
			for(Character key : set){
				Integer value = tm.get(key);
				sb.append(key).append("(").append(value).append(")");
			}
			
			//把字符串缓冲区转换为字符串输出
			String result = sb.toString();
			System.out.println("result:"+result);
		}
	}

## Collections 类

>public class Collectionsextends Object 是一个针对集合操作的工具类，都是静态方法。


### 常见的几个小方法：
	A:public static <T> void sort(List<T> list) 默认情况自然排序
	B:public static <T> int binarySearch(List<?> list,T key) 二分查找
	C:public static <T> T max(Collection<?> coll) 最大值
	D:public static void reverse(List<?> list)翻转
	E:public static void shuffle(List<?> list)随机置换

### 案例

* 基础方法使用

	    public static void main(String[] args) {
		// 创建集合对象
		List<Integer> list = new ArrayList<Integer>();

		// 添加元素
		list.add(30);
		list.add(20);
		list.add(50);
		list.add(10);
		list.add(40);

		System.out.println("list:" + list);

		// public static <T> void sort(List<T> list)：排序 默认情况下是自然顺序。
		// Collections.sort(list);
		// System.out.println("list:" + list);
		// [10, 20, 30, 40, 50]

		// public static <T> int binarySearch(List<?> list,T key):二分查找
		// System.out
		// .println("binarySearch:" + Collections.binarySearch(list, 30));
		// System.out.println("binarySearch:"
		// + Collections.binarySearch(list, 300));

		// public static <T> T max(Collection<?> coll):最大值
		// System.out.println("max:"+Collections.max(list));

		// public static void reverse(List<?> list):反转
		// Collections.reverse(list);
		// System.out.println("list:" + list);
		
		//public static void shuffle(List<?> list):随机置换
		Collections.shuffle(list);
		System.out.println("list:" + list);
	}

* 自定义对象排序

		// 创建集合对象
		List<Student> list = new ArrayList<Student>();

		// 创建学生对象
		Student s1 = new Student("林青霞", 27);
		Student s2 = new Student("风清扬", 30);
		Student s3 = new Student("刘晓曲", 28);
		Student s4 = new Student("武鑫", 29);
		Student s5 = new Student("林青霞", 27);

		// 添加元素对象
		list.add(s1);
		list.add(s2);
		list.add(s3);
		list.add(s4);
		list.add(s5);

		// 排序
		// 自然排序
		// Collections.sort(list);
		// 比较器排序
		// 如果同时有自然排序和比较器排序，以比较器排序为主
		Collections.sort(list, new Comparator<Student>() {
			@Override
			public int compare(Student s1, Student s2) {
				int num = s2.getAge() - s1.getAge();
				int num2 = num == 0 ? s1.getName().compareTo(s2.getName())
						: num;
				return num2;
			}
		});

		// 遍历集合
		for (Student s : list) {
			System.out.println(s.getName() + "---" + s.getAge());
		}

* 模拟斗地主洗牌发牌


	import java.util.ArrayList;
	import java.util.Collections;
	
	/*
	 * 模拟斗地主洗牌和发牌
	 * 
	 * 分析：
	 * 		A:创建一个牌盒
	 * 		B:装牌
	 * 		C:洗牌
	 * 		D:发牌
	 * 		E:看牌
	 */
	public class PokerDemo {
		public static void main(String[] args) {
			// 创建一个牌盒
			ArrayList<String> array = new ArrayList<String>();
	
			// 装牌
			// 黑桃A,黑桃2,黑桃3,...黑桃K
			// 红桃A,...
			// 梅花A,...
			// 方块A,...
			// 定义一个花色数组
			String[] colors = { "♠", "♥", "♣", "♦" };
			// 定义一个点数数组
			String[] numbers = { "A", "2", "3", "4", "5", "6", "7", "8", "9", "10",
					"J", "Q", "K" };
			// 装牌
			for (String color : colors) {
				for (String number : numbers) {
					array.add(color.concat(number));
				}
			}
			array.add("小王");
			array.add("大王");
	
			// 洗牌
			Collections.shuffle(array);
	
			// System.out.println("array:" + array);
	
			// 发牌
			ArrayList<String> xianglei = new ArrayList<String>();
			ArrayList<String> linQingXia = new ArrayList<String>();
			ArrayList<String> liuYi = new ArrayList<String>();
	
			ArrayList<String> diPai = new ArrayList<String>();
	
			for (int x = 0; x < array.size(); x++) {
				if (x >= array.size() - 3) {
					diPai.add(array.get(x));
				} else if (x % 3 == 0) {
					fengQingYang.add(array.get(x));
				} else if (x % 3 == 1) {
					linQingXia.add(array.get(x));
				} else if (x % 3 == 2) {
					liuYi.add(array.get(x));
				}
			}
	
			// 看牌
			lookPoker("向磊", fengQingYang);
			lookPoker("林青霞", linQingXia);
			lookPoker("刘意", liuYi);
	
			lookPoker("底牌", diPai);
			}
	
			public static void lookPoker(String name, ArrayList<String> array) {
				System.out.print(name + "的牌是：");
				for (String s : array) {
					System.out.print(s + " ");
				}
				System.out.println();
				}
			}

如何对发出来的牌进行排序？

**图解：**
![](https://i.imgur.com/V0l4Cik.png)

**代码**
	/*
	 * 思路：
	 * 		A:创建一个HashMap集合
	 * 		B:创建一个ArrayList集合
	 * 		C:创建花色数组和点数数组
	 * 		D:从0开始往HashMap里面存储编号，并存储对应的牌
	 *        同时往ArrayList里面存储编号即可。
	 *      E:洗牌(洗的是编号)
	 *      F:发牌(发的也是编号，为了保证编号是排序的，就创建TreeSet集合接收)
	 *      G:看牌(遍历TreeSet集合，获取编号，到HashMap集合找对应的牌)
	 */
	public class PokerDemo {
		public static void main(String[] args) {
			// 创建一个HashMap集合
			HashMap<Integer, String> hm = new HashMap<Integer, String>();
	
			// 创建一个ArrayList集合
			ArrayList<Integer> array = new ArrayList<Integer>();
	
			// 创建花色数组和点数数组
			// 定义一个花色数组
			String[] colors = { "♠", "♥", "♣", "♦" };
			// 定义一个点数数组
			String[] numbers = { "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q",
					"K", "A", "2", };
	
			// 从0开始往HashMap里面存储编号，并存储对应的牌,同时往ArrayList里面存储编号即可。
			int index = 0;
	
			for (String number : numbers) {
				for (String color : colors) {
					String poker = color.concat(number);
					hm.put(index, poker);
					array.add(index);
					index++;
				}
			}
			hm.put(index, "小王");
			array.add(index);
			index++;
			hm.put(index, "大王");
			array.add(index);
	
			// 洗牌(洗的是编号)
			Collections.shuffle(array);
	
			// 发牌(发的也是编号，为了保证编号是排序的，就创建TreeSet集合接收)
			TreeSet<Integer> fengQingYang = new TreeSet<Integer>();
			TreeSet<Integer> linQingXia = new TreeSet<Integer>();
			TreeSet<Integer> liuYi = new TreeSet<Integer>();
			TreeSet<Integer> diPai = new TreeSet<Integer>();
	
			for (int x = 0; x < array.size(); x++) {
				if (x >= array.size() - 3) {
					diPai.add(array.get(x));
				} else if (x % 3 == 0) {
					fengQingYang.add(array.get(x));
				} else if (x % 3 == 1) {
					linQingXia.add(array.get(x));
				} else if (x % 3 == 2) {
					liuYi.add(array.get(x));
				}
			}
	
			// 看牌(遍历TreeSet集合，获取编号，到HashMap集合找对应的牌)
			lookPoker("风清扬", fengQingYang, hm);
			lookPoker("林青霞", linQingXia, hm);
			lookPoker("刘意", liuYi, hm);
			lookPoker("底牌", diPai, hm);
		}
	
		// 写看牌的功能
		public static void lookPoker(String name, TreeSet<Integer> ts,
				HashMap<Integer, String> hm) {
			System.out.print(name + "的牌是：");
			for (Integer key : ts) {
				String value = hm.get(key);
				System.out.print(value + " ");
			}
			System.out.println();
		}
	}


### 面试题附录：

HashMap和Hashtable的区别？

 * Hashtable:线程**安全**，效率低。**不允许**null键和null值
 * HashMap:线程**不安全**，效率高。**允许**null键和null值

List,Set,Map等接口是否都继承自Map接口？

 * List，Set不是继承自Map接口，它们继承自Collection接口
 * Map接口本身就是一个顶层接口

Collection和Collections的区别

 * Collection 是单列集合的顶层接口，有两个子接口List和Set
 * Collections 是针对集合进行操作的工具类，可以对集合进行排序和查找等

