---
title: " JAVA 集合框架 "
date: 2017-10-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - javabasic

---

### 集合

集合类的特点：集合只用于存储对象，集合长度是可变的，集合可以存储不同类型的对象。

所有集合类都位于 `java.util`包下。

集合里只能保存对象（实际上只是保存对象的引用变量），基本数据类型的变量要转换成对应的包装类才能放入集合类中。

#### 基本类型包装类

| 基本类型 | 包装类  | 基本类型 | 包装类 | 基本类型 | 包装类  | 基本类型 | 包装类    |
| :------- | ------- | -------- | ------ | -------- | ------- | -------- | --------- |
| boolean  | Boolean | short    | Short  | int      | Integer | char     | Character |
| byte     | Byte    | long     | Long   | float    | Fload   | double   | Double    |

#### 集合框架图

集合框架主要由 Collection 和 Map 两个接口派生而出；两者区别：

- Collection 是单列集合；Map 是双列集合
- Collection 中只有 Set 系列要求元素唯一；Map 中键需要唯一，值可以重复
- Collection 的数据结构是针对元素的；Map 的数据结构是针对键的

![集合框架图](/images/JAVA/Collections_API0.png)

API 关系图

- 点线边框的是接口(6个)：Collection，Iterator，List等
- 虚线边框的是抽象类(5个)：AbstractCollection，AbstractList，AbstractMap等
- 实线边框的是实现类(8个)：ArrayList，LinkedList，HashMap等；对接口的具体实现
- Arrays 和 Collections。它们是操作数组、集合的两个工具类

![集合框架图](/images/JAVA/Collections_API1.png)

### 迭代器

**Iterator** 是遍历集合的工具，我们通常通过 Iterator 迭代器来遍历集合。Collection 依赖于 Iterator，是因为 Collection 的实现类都要实现 iterator() 函数，返回一个 Iterator 对象。

一般遍历数组都是采用 for 循环或者增强 for 循环，这两个方法也可以用在集合框架，但是还有一种方法是采用迭代器遍历集合框架，它是一个对象，实现了 Iterator 接口或 ListIterator 接口。迭代器遍历元素时，通过集合是不能修改元素的，会报异常：`ConcurrentModificationException` 可以通过迭代器修改元素 。

当使用 Iterator 对集合元素进行迭代时，Iterator 并不是把集合元素本身传给了迭代变量，而是把集合元素的值传给了迭代变量（就如同参数传递是值传递，基本数据类型传递的是值，引用类型传递的仅仅是对象的引用变量），所以修改迭代变量的值对集合元素本身没有任何影响。

ListIterator 继承了 Iterator，以允许双向遍历列表和修改元素。ListIterator 是专门为遍历 List 而存在的。

#### 方法列表

| Iterator Method    | Description                          | List Iterator Method      | Description                  |
| :----------------- | :----------------------------------- | :------------------------ | :--------------------------- |
| boolean hasNext(); | 判断集合里是否存在下一个元素         | boolean hasPrevious();    | 判断集合里是否存在上一个元素 |
| Object next();     | 返回集合里下一个元素并更新迭代器状态 | Object previous();        | 返回集合里上一个元素         |
| void remove()      | 删除集合里上一次next方法返回的元素   | void set(Object element); | 设置元素值                   |
|                    |                                      | void add(Object element); | 在指定位置插入一个元素       |

### Collection 继承关系

Collection 是一个接口，是高度抽象出来的集合，Collection定义了集合框架的共性功能。

List 和 Set 都继承自 Collection。

#### 操作方法

![Collection Methods](/images/JAVA/Collection_Methods.png)

### List 集合

存取有序，有索引，可以根据索引来进行取值，元素可以重复。可以动态增长，根据实际存储的数据的长度自动增长 List 的长度。

数组和 List 之间的转换：

- 数组转 List：使用 Arrays. asList(array) 进行转换。
- List 转数组：使用 List 自带的 toArray() 方法。

特有方法：

| Method                                         | Description                              |
| :--------------------------------------------- | :--------------------------------------- |
| Object get(int index);                         | 根据索引获取指定位置的元素               |
| Object set(int index, Object element);         | 根据索引修改元素，返回被修改的元素       |
| void add(int index, Object element);           | 在指定索引位置添加元素                   |
| Object remove(int index);                      | 根据索引删除元素，返回删除的元素         |
| int indexOf(Object o);                         | 获取指定元素的索引                       |
| int lastIndexOf(Object o);                     | 获取集合最后一个元素的坐标               |
| Object[] toArray();                            | 返回按适当顺序包含列表中的所有元素的数组 |
| void sort(Comparator c);                       | 根据Comparator参数对List集合的元素排序   |
| `List<E> subList(int fromIndex, int toIndex);` | List 集合分隔，返回 List 集合            |
| `ListIterator<E> listIterator();`              | List集合特有的迭代器                     |

#### ArrayList

底层基于数组实现，查询速度快；增删速度慢；线程不安全；效率高。

每一个 ArrayList 都有一个初始容量（10），该容量代表了数组的大小。随着容器中的元素不断增加，容器的大小也会随着增加。在每次向容器中增加元素的同时都会进行容量检查，当快溢出时，就会进行扩容操作。所以如果我们明确所插入元素的多少，最好指定一个初始容量值，避免过多的进行扩容操作而浪费时间、效率。

特有方法：

- `void ensureCapacity(int minCapacity);`：如有必要，增加此 ArrayList 实例的容量，以确保它至少能够容纳最小容量参数所指定的元素数。

- `void trimToSize();`：将此 ArrayList 实例的容量调整为列表的当前大小。

#### LinkedList

底层基于双向链表结构实现，查询速度慢；增删速度快；线程不安全；效率高。

LinkedList 中包含了大量操作首尾元素的方法。所以当我们需要实现的需求增删操作很多，查询很少或者查询很多但都是查询手尾的时候，我们就可以使用 LinkedList 集合。

特有方法：

| Method                   | Description                | Method                 | Description              |
| :----------------------- | :------------------------- | :--------------------- | :----------------------- |
| Object getFirst();       | 获取并返回第一个元素       | Object peek();         | 返回集合第一个元素       |
| void addFirst(Object e); | 添加元素到集合头部         | Object poll();         | 删除并返回第一个元素     |
| Object getLast();        | 获取并返回集合最后一个元素 | void push(Object obj); | 添加元素到集合头部       |
| Object removeFirst();    | 删除并返回集合第一个元素   | Object pop();          | 删除并返回集合第一个元素 |
| Object removeLast();     | 删除并返回集合最后一个元素 |                        |                          |

#### Vector

线程安全，但是速度太慢，被 ArrayList 替代。

#### Stack

继承自Vector。用户模拟“栈”这种数据结构，“栈”通常是指“后进先出”(LIFO)的容器。最后“push”进栈的元素，将被最先“pop”出栈。

Java 里其实没有纯粹的 Stack，可以自己实现，用组合的方式，封装一下LinkedList 即可。

#### Queue

队列通常是指“先进先出”(FIFO，first-in-first-out)的容器。JDK 里使用 LinkedList 来提供这个功能。

新元素插入(offer)到队列的尾部，访问元素(poll)操作会返回队列头部的元素。队列不允许随机访问队列中的元素。

方法列表：

![Collection Methods](/images/JAVA/Queue_Methods.png)

### Set 集合

元素是无序(存入和取出的顺序不一定一致)；元素不可以重复；无下标。

Set 集合与 Collection 集合基本相同，没有提供任何额外的方法。

可以用迭代器或者转换成 list。

#### HashSet

内部采用 HashMap 实现的，底层数据结构是哈希表；`允许有 null 值`；线程不安全；不同步。

HashSet 是如何保证元素唯一性的呢？

- 是通过元素的两个方法，hashCode 和 equals 来完成。
  - 如果元素的 hashCode 值相同，才会判断 equals 是否为 true。
  - 如果元素的 hashcode 值不同，不会调用 equals。

在向 HashSet 集合中存储自定义对象时，为了保证 set 集合的唯一性，那么必须重写 hashCode 和 equals 方法。

#### LinkedHashSet

是 HashSet 的子类基于链表和哈希表共同实现(LinkedHashMap)；所以存取有序；元素唯一。

#### TreeSet

基于二叉树的数据结构；存取无序；元素唯一；线程不安全；可以对Set集合中的元素进行排序。

二叉树的存储过程：

如果是第一个元素，那么直接存入，作为根节点，下一个元素进来是会跟节点比较，如果大于节点放右边的，小于节点放左边；等于节点就不存储。后面的元素进来会依次比较，直到有位置存储为止。

TreeSet 保证元素唯一的两种方式

- 自定义对象实现 Comparable 接口，重写 comparaTo 方法，该方法返回0表示相等，小于0表示准备存入的元素比被比较的元素小，否则大于0；
- 在创建 TreeSet 的时候向构造器中传入比较器 Comparator 接口实现类对象，实现Comparator 接口重写 compara 方法。

### Collections 工具类

Collections 工具类常用方法：排序、查找、替换操作。

#### 操作方法

| Method                                                       | Description                                              |
| :----------------------------------------------------------- | :------------------------------------------------------- |
| void reverse(List list);                                     | 反转集合                                                 |
| void shuffle(List list);                                     | 随机排序                                                 |
| void sort(List list);                                        | 按自然排序的升序排序                                     |
| void sort(List list, Comparator c);                          | 定制排序，由Comparator控制排序逻辑                       |
| void swap(List list, int i , int j);                         | 交换两个索引位置的元素                                   |
| int max(Collection coll);                                    | 根据元素的自然顺序，返回最大的元素                       |
| int max(Collection coll, Comparator c);                      | 根据定制排序，返回最大元素，排序规则由Comparatator类控制 |
| void fill(List list, Object obj);                            | 用指定的元素代替指定list中的所有元素。                   |
| int frequency(Collection c, Object o);                       | 统计元素出现次数                                         |
| boolean replaceAll(List list, Object oldVal, Object newVal); | 用新元素替换旧元素                                       |

### Map 集合

Collection、Set、List 接口都属于单值的操作，而 Map 中的每个元素都使用 key—>value 的形式存储在集合中。

Map 集合：该集合存储键值对。一对一对往里存。而且要保证键的唯一性。

#### 方法列表

![Map Methods](/images/JAVA/Map_Methods.png)

Map 迭代方式：

- keySet()：只获取 key 值，然后可以通过 get(key) 获取对应的 value
- values()：直接获取所有的 value 值
- entrySet()：返回包含映射关系的 Set 视图

#### Entry 类

方法：

| Method                    | Description                    |
| :------------------------ | :----------------------------- |
| K getKey();               | 返回与此项对应的键             |
| V getValue();             | 返回与此项对应的值             |
| V setValue(V value);      | 用指定的值替换与此项对应的值   |
| boolean equals(Object o); | 比较指定对象与当前对象是否相等 |

#### HashMap / HashTable

基于哈希表结构实现，超过初始容量会对性能有损耗；存储自定义对象作为键时，必须重写 hasCode 和 equals 方法。

HashMap 最多允许有一条记录的键为 null；线程不同步。

#### LinkedHashMap

继承自 HashMap，但通过重写嵌套类 HashMap.Entry 实现了链表结构，基于链表和哈希表结构的所以具有存取有序，键不重复的特性；同样有容量的问题。

#### TreeMap

TreeMap是SortedMap接口的实现类。TreeMap 是一个**有序的key-value集合**，它是通过红黑树实现的，每个key-value对即作为红黑树的一个节点。

TreeMap 底层使用的二叉树，其中存放进去的所有数据都需要排序，要排序，就要求对象具备比较功能。对象所属的类需要实现 Comparable 接口。或者给 TreeMap 集合传递一个 Comparator 接口对象。

#### Properties

Properties 继承于 Hashtable，表示一个持久的属性集，属性列表中每个键及其对应值都是一个字符串。

文本中的数据，必须是键值对形式，可以使用空格、等号、冒号等符号分隔。

常用方法列表：

- Object setProperty(String key,String value)：添加键值对属性值
-  String getProperty(String key)：通过键，获取对应的属性值
- Set stringPropertyNames()：获取所有键的集合，返回键的集合集
- void load(Reader reader)：把键值对形式的文本文件内容以字符流方式加载到集合中
- void load(InputStream inStream)：把键值对形式的文本文件内容以字节流方式加载到集合中
- void store(Writer writer,String comments)：把集合中的数据存储到文本文件中
- void store(OutputStream out,String comments)：把集合中的数据存储到文本文件中

