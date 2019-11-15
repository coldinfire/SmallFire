---
title: "报表开发<内表操作>"
date: 2018-06-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---





## 内表操作 

### SQL 语句的执行顺序

书写顺序：SELECT [DISTINCT]-->FROM-->WHERE-->GROUP BY-->HAVING-->UNION-->ORDER BY

其执行顺序为：FROM-->WHERE-->GROUP BY-->HAVING-->SELECT-->DISTINCT-->UNION->ORDER BY

1、FROM 才是 SQL 语句执行的第一步，并非 SELECT 

2、SELECT 是在大部分语句执行了之后才执行的，严格的说是在 FROM 和 GROUP BY 之后执行的。

3、无论在语法上还是在执行顺序上， UNION 总是排在在 ORDER BY 之前。

### 【清空字段，内表，结构】

​	使用字段，内表，结构前需要清空其内容，以避免在程序内使用时产生数据的混乱。循环结束后，循环开始前需要根据程序需求选择是否需要清空内表内容，对于带有Headline 的内表要注意。

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
    TRANSPORTING:根据后面的字段进行比较，当字段出现不一致现象时，触发修改操作
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
RANGES ：
  RANGES sel FOR obj [OCCURS n].
  SELECT-OPTIONS selcrit FOR {dobj|(name)}.
FOR ALLENTRIES:
  1:会自动删除重复行   
  2:WHERE后还有其他条件，会忽略后续条件
表连接：
  INNER JOIN、LEFT OUTER JOIN.
```

### 【事务处理】

```JS
CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
  EXPORTING
     wait = 'X'.
  IMPORTING
     return = commback.
CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'
  IMPORTING
     return = commback.

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


