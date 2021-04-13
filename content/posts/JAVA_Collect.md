---
title: "JAVA 集合框架"
date: 2017-10-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - 集合
---

### 集合

集合类的特点：集合只用于存储对象，集合长度是可变的，集合可以存储不同类型的对象。

所有集合类都位于 java.util 包下。

#### 集合框架图

集合框架主要由 Collection 和 Map 两个接口派生而出；两者区别：

- Collection 是单列集合；Map 是双列集合
- Collection 中只有 Set 系列要求元素唯一；Map 中键需要唯一，值可以重复
- Collection 的数据结构是针对元素的；Map 的数据结构是针对键的

![集合框架图](/images/JAVA/Collections_API0.png)

API 关系图

- 点线边框的是接口(6个)：Collection，Iterator，List等；
- 虚线边框的是抽象类(5个)：AbstractCollection，AbstractList，AbstractMap等；
- 实线边框的是实现类(8个)：ArrayList，LinkedList，HashMap等；对接口的具体实现

![集合框架图](/images/JAVA/Collections_API1.png)

### Collection 继承关系

List 和 Set 都继承自 Collection；Collection定义了集合框架的共性功能。

#### 操作方法

|      |      |      |      |
| ---- | ---- | ---- | ---- |
|      |      |      |      |
|      |      |      |      |
|      |      |      |      |
|      |      |      |      |
|      |      |      |      |

### List

存取有序，有索引，可以根据索引来进行取值，元素可以重复。

`Arrays.asList`：这个方法的实现依赖一个嵌套类，这个嵌套类也叫 ArrayList。

#### ArrayList

底层使用数组实现，查询速度快；增删速度慢；线程不安全。

#### LinkedList

基于链表结构实现，查询速度慢；增删速度快；增加了push/pop/remove/pull，其实都是 removeFirst。

#### Vector

线程安全，但是速度太慢，被 ArrayList 替代。

#### Stack

继承自Vector。Java 里其实没有纯粹的 Stack，可以自己实现，用组合的方式，封装一下LinkedList 即可。

#### Queue

本来是单独的一类，不过在 SUN 的 JDK 里就是用 LinkedList 来提供这个功能的，主要方法是offer/pull/peek。

### Set

元素是无序(存入和取出的顺序不一定一致)，元素不可以重复，无下标。

add/remove。可以用迭代器或者转换成 list。

#### HashSet

内部采用 HashMap 实现的，底层数据结构是哈希表；线程不安全；不同步。

HashSet 是如何保证元素唯一性的呢？

- 是通过元素的两个方法，hashCode 和 equals 来完成。
  - 如果元素的 hashCode 值相同，才会判断 equals 是否为 true。
  - 如果元素的 hashcode 值不同，不会调用 equals。

在向 HashSet 集合中存储自定义对象时，为了保证 set 集合的唯一性，那么必须重写hashCode 和 equals 方法。

#### LinkedHashSet

基于链表和哈希表共同实现(LinkedHashMap)；所以存取有序；元素唯一。

#### TreeSet

基于二叉树的数据结构；存取无序；元素唯一；线程不安全；可以对Set集合中的元素进行排序。

二叉树的存储过程：

如果是第一个元素，那么直接存入，作为根节点，下一个元素进来是会跟节点比较，如果大于节点放右边的，小于节点放左边；等于节点就不存储。后面的元素进来会依次比较，直到有位置存储为止。

TreeSet 保证元素唯一的两种方式

- 自定义对象实现 Comparable 接口，重写 comparaTo 方法，该方法返回0表示相等，小于0表示准备存入的元素比被比较的元素小，否则大于0；
- 在创建 TreeSet 的时候向构造器中传入比较器 Comparator 接口实现类对象，实现Comparator 接口重写 compara 方法。

### Map

Correction、Set、List接口都属于单值的操作，而 Map 中的每个元素都使用 key—>value的形式存储在集合中。

Map集合：该集合存储键值对。一对一对往里存。而且要保证键的唯一性。

#### HashMap / HashTable

基于哈希表结构实现，超过初始容量会对性能有损耗；存储自定义对象作为键时，必须重写 hasCode 和 equals 方法。

#### LinkedHashMap

继承自 HashMap，但通过重写嵌套类 HashMap.Entry 实现了链表结构，基于链表和哈希表结构的所以具有存取有序，键不重复的特性；同样有容量的问题。

#### TreeMap

TreeMap 底层使用的二叉树，其中存放进去的所有数据都需要排序，要排序，就要求对象具备比较功能。对象所属的类需要实现 Comparable 接口。或者给 TreeMap 集合传递一个 Comparator 接口对象。

#### Properties

继承 HashTable。