---
title: "报表开发<内表操作>"
date: 2018-09-12T17:20:58+08:00
draft: true
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - 报表开发

---





## 内表操作 

### SQL 语句的执行顺序

书写顺序：SELECT [DISTINCT]-->FROM-->WHERE-->GROUP BY-->HAVING-->UNION-->ORDER BY

其执行顺序为：FROM-->WHERE-->GROUP BY-->HAVING-->SELECT-->DISTINCT-->UNION->ORDER BY

1、FROM 才是 SQL 语句执行的第一步，并非 SELECT 

2、SELECT 是在大部分语句执行了之后才执行的，严格的说是在 FROM 和 GROUP BY 之后执行的。

3、无论在语法上还是在执行顺序上， UNION 总是排在在 ORDER BY 之前。



### 【增删改】


```JS
<1> Loop AT循环(sy-tabix:索引号)
    LOOP AT itab {INTO wa}|{ASSIGNING <fs> [CASTING]} | {TRANSPORTING NO FILDS} 
      [[USING KEY key_name] [FROM idx1] [TO idx2] [WHERE log_exp|(cond_syntax)]].
    ENDLOOP.
<2> INSERT <wa> INTO TABLE <itab>.[单条]
    INSERT <wa> INTO <itab> INDEX <idx>.[索引]
    INSERT LINES OF <itab> [FROM <n1>] [TO <n2>] INTO TABLE <itab>.[批量插入]
    APPEND <wa> TO <itab>.
    APPEND LINES OF <itab1> [FROM<n1>] [TO<n2>] TO <itab2>.
<3> READ TABLE <itab> WITH KEY {<k1>=<f1>...[BINARY SEARCH]} INTO <wa>}
    [COMPARING <f1><f2>...|ALL FIELDS]  [TRANSPORTING <f1> <f2> ...|ALL FIELDS
                                        |NO FIELDS] | ASSIGNING <fs>.
    COMPARING:根据关键字读取指定的单行与工作区<wa>中的相应组件进行比较。内容相同SY-SUBRC=0
<4> MODIFY itab1.
    MODIFY itab INDEX idx FROM <wa> [TRANSPORTING <f1> <f2> ...].
    MODIFY TABLE <itab> FROM <wa> [TRANSPORTING <f1> <f2>...].[修改单条]
    MODIFY <itab> FROM <wa> TRANSPORTING <f1> <f2> ... WHERE <cond>.[修改多条]
    UPDATE dbtab SET f1=g1 ... fi=gi WHERE <con>.
<5> DELETE TABLE <itab> FROM <wa> [USING KEY key_name].[删除单条]
    DELETE TABLE <itab> WITH TABLE KEY <k1>=<f1>.. .
    DELETE <itab> WHERE <cond>.[删除多行]
    DELETE <itab> [INDEX <idx>].
<6>删除重复
  (1)SORT record_tab. //首先进行排序处理
  (2)DELETE ADJACENT DUPLICATES ENTRIES FROM itab [USING KEY key_name] 
                                     [COMPARING K1 K2...] [ALL FIELDS].//删除
<7> COLLECT <wa> INTO <itab>.
<8> 第二索引
    UNIQUE KEY.唯一升序第二索引
    NON-UNIQUE KEY.非唯一升序第二索引
    通过第二索引在无法使用主键时，可以加快大量处理数据的速度。
```

### 【查】

```JS
 SELECT SINGLE ... INTO [CORRESPONDING FIELDS OF] <wa> WHERE ...
  SELECT ... INTO|APPENDING CORRESPONDING FIELDS OF TABLE <itab>...
  RANG条件内.表：
    RANGES sel FOR obj [OCCURS n].
    SELECT-OPTIONS selcrit FOR {dobj|(name)}.
  FOR ALLENTRIES:
    1:会自动删除重复行   2:WHERE后还有其他条件，会忽略后续条件
  表连接：
    INNER JOIN、LEFT OUTER JOIN.
```

### 【SAP锁】

```JS
通用数据库表锁函数：ENQUEUE_E_TABLE、DEQUEUE_E_TABLE、DEQUEUE_ALL
特定数据库表函数：ENQUEUE_<LOCK OBJ>、DEQUEUE_<LOCK OBJ>
自定义锁对象：EZ_/EY_命名
S:共享锁     E:可重入的排他锁     X:排他锁
1、在SE11里创建锁对象，自定义的锁对象都必须以EZ或者EY开头来命名。一个锁对象里只包含一个
     PRIMARY TABLE，可以包含若干个SECONDARY TABLE，锁的模式有三种：E，S，X。
   模式E：当更改数据的时候设置为此模式。   (Shared lock, read lock) 【一般使用E】
   模式S：本身不需更改数据，但是希望显示的数据不被别人更改 (Exclusive lock, write lock)
   模式X：和E类似，但是不允许累加，完全独占。 
          (Exclusive lock, extended write lock, cannot be cumulated)
2、上锁的一般步骤
      先上锁，上锁成功之后，从数据库取数据，然后更改数据，接着更新到数据库，最后解锁。
   按照这个步骤，才能保证更改完全运行在锁的保护机制下。
3、上锁与解锁
   ENQUEUE_<lock object的名字> 对象 EZZSOPR0032 要求的锁定
   DEQUEUE_<lock object的名字> 释放对象 EZZSOPR0032 的锁定
     有些情况下，程序中设置成功的逻辑锁会隐式的自己解锁。比如说程序结束发生的时候
  （MESSAGE TYPE为A或者X的时候），使用语句LEAVE PROGRAM，LEAVE TO TRANSACTION，或者在
   命令行输入/n回车以后。使用DEQUEUE FUNCTION MODULE来解锁的时候，不会产生EXCEPTION。
   要解开你在程序中创建的所有的逻辑锁，可以用FM：DEQUEUE_ALL.

```

### 【事务处理】

```JS
COMMIT WORK.         异步更新。
COMMIT WORK AND WAIT.同步跟新，执行结果可通过sy-subrc判断是否提交成功。
ROLLBACK WORK.
```



## Focus

1. 多表的结合的查询，首先进行各自的关键字查询，然后进行表与表转换可以提升效率
2. 表相应字段非关键字时，寻找有该关键字的相对应的关联表进行查询并合并表数据
3. binary search 和 sort 进行使用时注意sort的关键字与binary search 保持统一
4. MODIFY itab1 INDEX sy-tabix.进行修改时最好定义变量记录sy-tabix.
5. inner join 和 left outer join 的连接条件最好都是关键字.
6. 双层loop循环时，第二个内部表定义为SORTED TABLE会大大的提高处理速度


