---
title: " 报表开发<内表操作> "
date: 2018-06-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

### SQL 语句的执行顺序

书写顺序：SELECT [DISTINCT]-->FROM-->WHERE-->GROUP BY-->HAVING-->UNION-->ORDER BY

其执行顺序为：FROM-->WHERE-->GROUP BY-->HAVING-->SELECT-->DISTINCT-->UNION->ORDER BY

- FROM 才是 SQL 语句执行的第一步，并非 SELECT 


- SELECT 是在大部分语句执行了之后才执行的，严格的说是在 FROM 和 GROUP BY 之后执行的。


- 无论在语法上还是在执行顺序上， UNION 总是排在在 ORDER BY 之前。

### 清空:字段、内表、结构

使用字段，内表，结构前需要清空其内容，以避免在程序内使用时产生数据的混乱。

循环结束后，循环开始前需要根据程序需求选择是否需要清空内表内容，对于带有 HEAD LINE 的内表要注意。

### 查询数据

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

### 事务处理

#### 直接执行

- `COMMIT WORK.`：异步更新

- `COMMIT WORK AND WAIT.`：同步更新，执行结果可通过 sy-subrc 判断是否提交成功

- `ROLLBACK WORK.`：回滚

#### 调用函数

```ABAP
"Commit"
CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
  EXPORTING
     wait = 'X'.
  IMPORTING
     return = commback.
"Rollback"   
CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'
  IMPORTING
     return = commback.
```

### Focus

1. 多表的结合的查询，首先进行各自的关键字查询，然后进行表与表转换可以提升效率
2. 表相应字段非关键字时，寻找有该关键字的相对应的关联表进行查询并合并表数据
3. binary search 和 sort 进行使用时注意sort的关键字与binary search 保持统一
4. MODIFY itab1 INDEX sy-tabix.进行修改时最好定义变量记录sy-tabix.
5. inner join 和 left outer join 的连接条件最好都是关键字.
6. 双层loop循环时，第二个内部表定义为SORTED TABLE会大大的提高处理速度


