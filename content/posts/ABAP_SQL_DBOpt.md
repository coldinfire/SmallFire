---
title: " ABAP 性能优化(数据操作) "
date: 2018-06-01
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbasis

---

### 性能分析工具

ST05：性能分析，追踪 SQL，分析哪条 SQL Statement 语句，最耗时间

STAD：得到某个程序或事务运行时的总体分析数据，系统时间，CPU 时间等

SE30： 分析某个事务或程序的执行时间，有一些性能分析的例子

### 数据库

1. 不要使用 SELECT * ...，选择需要的字段, SELECT * 既浪费CPU，还需占用大量的ABAP内存

2. 不要使用SELECT DISTINCT .，会绕过缓存，可使用 SORT BY + DELETE ADJACENT DUPLICATES 

3. 少用相关子查询，因为子查询对外层查询结果集中的每条记录都会执行一次

4. 少用嵌套SELECT … ENDSELECT，可以使用联合查询或FOR ALL ENTRIES来替换,减少循环次数

5. 如果确定只查一条数据时，使用 SELECT SINGLE... 或者是 SELECT ...UP TO 1 ROWS ...

6. 统计时，直接使用SQL聚合函数，而不是将数据读取出来后在程序里再进行统计

7. 使用 Filed-Symbol 读取数据，这样省掉了将从数据库中的取记录放入内表的INTO语句这一过程开销

8. 多使用inner join，必要时才使用left join

9. inner join获取数据时，尽量不要用太多的表关联，特别是大表关联，关联顺序为：小表-大表；换言之，让查询第一个表后所得到的结果集就尽可能小;确定了表连接的次序后，应考虑将查询条件尽量限制在靠前的表里；两个表之间进行连接的时候，应考虑关键字段或索引字段的作用

10. where 条件里面多用索引、主键，顺序也要遵循小表-大表

11. inner join 条件放置的位置应该按照 On、Where、Having 的顺序放，因为SQL条件的的执行一般是按这个顺序来执行的，将条件放在最开始执行，则可过滤掉大部数据；但要注意Left Outer Join，是否可以将ON中的条件移动到Where 从句则要考虑（如果真能放在Where从句中，则应该用 Inner Join，而非Left Outer Join，因为Where条件会过滤掉那些包括在右表中不存在的左表数据），因为此时条件放在 On 后面与放在 Where 语句后面结果是不一样的（因为不管on中的条件是否为真，左表中在右边表不存在的数据也会被返回，但如放在where条件中，则会对On产生的数据再次过滤的条件，会滤掉不满足条件的记录，包括左表在右表中找不到的记录，这时已经没有left join的含义）
    
12. 要根据主键或索引字段查找数据，且 WHERE 从句中的条件字段需按 INDEX 字段顺序书写，且将索引字段条件靠前（左边），检查条件组合字段是否是主键，或者是上在上面创建了索引，避免条件组合字段即不是主键又没有索引

13. SELECT语句 WHERE 条件，应该先将主键相关条件放在前面，然后按照比较符 = 、< 、>、 <> 、LIKE、IN 的顺序排列

14. 使用部分索引字段问题：如果一个索引是由多个字段组成的，只使用一部分关键字段来进行查询时，也是可以使用到索引，但使用时要注意要按照索引定义的顺序且取其前面部分

15. 请根据索引字段进行 ORDER BY，否则通过程序进行 SORT BY。与其在数据库在通过非索引字段进行排序，不如在程序中用 SORT BY 语句进行排序，因为此情况下应用服务器上的执行速度要比数据库服务器快(应用服务器上采用的是内存排序)

16. 避免在索引字段上使用：not、<>、!=、IS NULL、IS NOT NULL，可以用 > 与 < 来替代避免使用 LIKE，但LIKE '销售组1000'和LIKE '销售组1000%'可以用到，而LIKE '%销售组1000'（百分号前置）则用不到索引不要使用 OR 来连接
     多个索引字段(但同一字段多个值之间可以使用OR)；对于同一索引字段，可以使用IN来替代OR；带有 BETWEEN 的 WHERE 条件不能通过索引来搜索，也可使用IN代替。

17. 在下面情况下使用FOR ALL ENTRIES IN:在循环内表时 Loop...at itab 时 join 超过三个表会出现性能问题，当使用JOIN连接超过3个表时表数据非常大时，使用JOIN会很慢，此时改用FOR ALL ENTRIES IN

18. 避免使用以下语句，因为使用这些语句时，不能使用 Table Buffer：

    ```JS
    Aggregate expressions
    Select distinct
    Select … for update
    Order by、group by、having从句
    joins，使用JOIN时，会绕过SAP缓存，可以使用FOR ALL ENTRIES来代替
    where从句中使用Sub queries（子查询）
    shere从句中使用IS NULL条件
    ```

    - 使用内表批量操作数据库，而不要使用工作区一条条操作,如：

    ```JS
    SELECT ... INTO TABLE itab
    INSERT dbtab FROM TABLE itab
    DELETE dbtab FROM TABLE itab
    UPDATE/MODIFY dbtab FROM TABLE itab		 
    ```

19. 针对 STANDARD TABLE 排序后可以进行二分法查找，使用 SORTED TABLE 也可进行二分法查找，那么二者有什么区别呢？

    1. 由于 SORTED TABLE 自始至终都保持排序，如果需要对内部进行频繁的插入、删除操作，则不推荐使用 SORTED TABLE，性能会很差。
    2. 而另一方面，如果我们要用的是类似 LOOP AT it_lips WHERE vgbel = ** 的语法（而非 READ TABLE）时，对于 STANDARD TABLE 就无法采用二分法查找，或者说会很麻烦。而对于 SORTED TABLE，系统会自动采用二分法优化查找过程。

20. 随着业务的持续，VBRK 表的条目将越来越多，而 “未导出” 的条目则会维持在一个较为平稳的数字上，为了有效区分历史数据和现用数据，可添加 TABLE INDEX，提高报表的查询速度。很多 SAP 标准表都自带了一些索引，这些索引大都比较实用。

    创建索引需要注意以下几点：

    （1）索引会占用额外的数据库空间，还会降低插入 / 修改的速度（虽然可提高查询速度），所以需要考虑实用性，肯定不是越多越好。如果表中已有类似的索引，则不推荐新建。而对于容量大的、被多个程序访问的表加索引就更要谨慎了，比如 VBFA、MSEG、FAGLFLEXA、LIPS、VBAP、EDIDC、STXH 等等。

    （2）创建索引时应注意字段的先后次序，MANDT 是必须的而且都要放在第一位。字段的先后次序取决于实际业务需要。另外索引的字段不宜太多，字段越多占用的数据库空间就越多，对于插入 / 修改的影响也更大。





