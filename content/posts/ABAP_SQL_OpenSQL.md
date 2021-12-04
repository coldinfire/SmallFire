---
title: " ABAP Open SQL "
date: 2018-05-24
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbasis

---

### Open SQL

ABAP可以通过两种方式与数据库交互

- Open SQL：为 SAP 支持的所有数据库提供了统一的语法和语义。通过 SAP 数据库接口识别不同的数据库平台，然后将 ABAP SQL 语句转换成底层数据库对应的 SQL 语句。

- Native SQL：允许在 ABAP 程序中使用特定数据自身的 SQL 语句，直接访问第三方数据库。这意味着可以使用不是由 ABAP 字典管理的数据库表，用来集成不属于 R/3 系统的数据源。

基本语法：与其他 SQL 语句类似，主要包括增删改查：Select、Insert、Update、Delete、Modify 等。

#### Open SQL 返回码

所有 Open SQL 语句都使用返回代码填充以下两个系统字段。

- `SY-SUBRC`：在每个 Open SQL 语句之后，如果操作成功，系统字段 SY-SUBRC 包含值 0，否则为非 0 值。

- `SY-DBCNT`：在 Open SQL 语句之后，系统字段 SY-DBCNT 包含处理的数据库行数。

### SQL 语句书写/执行顺序

#### 书写顺序

- `SELECT [DISTINCT]-->FROM-->INTO-->WHERE-->GROUP BY-->HAVING-->UNION-->ORDER BY.`

#### 执行顺序

- FROM-->WHERE-->GROUP BY-->HAVING-->SELECT-->DISTINCT-->UNION-->ORDER BY

- FROM 才是 SQL 语句执行的第一步，并非 SELECT 

- SELECT 是在大部分语句执行了之后才执行的，严格的说是在 FROM 和 GROUP BY 之后执行的。

- 无论在语法上还是在执行顺序上， UNION 总是排在在 ORDER BY 之前。

### 基本语法

#### SELECT 语法 

```ABAP
SELECT [SINGLE v1 v2] <result> FROM <dbtab> "SINGLE 表示查询单行数据"
  CLIENT SPECIFIED       "避免 CLIENT 不同"
  INTO [TABLE] <target>  "内表或结构"
  [INTO (<f1>,...,<fn>)] "将查询结果赋值到具体字段"
  [INTO CORRESPONDING FILES OF TABLE <itab>] "将查询结果按字段匹配赋值给具体的表或者结构体"
  WHERE <condition>      "查询条件"
  GROUP BY <fields>      "分组查询条件"
  ORDER BY <fields>.     "排序条件"
```

表连接使用

- INNER JOIN：查询结果包含两个连接表中彼此相对应的数据记录。

- LEFT OUTER JOIN：查询结果集中包含左表中的所有数据记录，右表中仅查询出包含相匹配的数据。

- 连接语句：使用 ON 和 AND 语句将左右两个表关联起来，一般使用主键。
  - INNER JOIN 不能使用 NOT、LIKE、IN
  - LEFT OUTER JOIN 只能使用 EQ

WHERE 条件语句：至少有一个表达式的两个操作数分别来自于左右两个表建立连接关系

- LEFT OUTER JOIN 的右表字段不能在 WHERE 中使用 

使用 FOR ALL ENTRIED IN itab 前，一定要检查内表是否为空。

- 如果内表为空会查询所有的数据，造成 SQL 的执行效率极低

-  `SELECT <f1...fn> FROM <dbtab> FOR ALL ENTRIED IN <itab> WHERE <cond>.`


获取限制读取数据的条数：前n条

-  `SELECT * FROM <dbtab> INTO <itab> UP TO <n> ROWS. `

标准函数：

- | 函数    | 描述                         | 函数  | 描述                     |
  | :------ | :--------------------------- | :---- | :----------------------- |
  | COUNT() | 统计查询总数                 | MAX() | 统计表中某个字段的最大值 |
  | SUM()   | 统计表中某个数值字段的总和   | MIN() | 统计表中某个字段的最小值 |
  | AVG()   | 统计表中某个数值字段的平均值 |       |                          |

#### UPDATE

  ```ABAP
UPDATE <dbtab> SET f1 = v1...fn = vn (WHERE <condition>).
UPDATE <dbtab> FROM <wa>.
UPDATE <dbtab> FROM TABLE <itab> (WHERE <condition>).
  ```

####  INSERT

  ```ABAP
INSERT INTO <dbtab> VALUES <conditin>.
INSERT <dbtab> FROM <wa>.
INSERT <dbtab> FROM TABLE <itab>.
  ```

#### DELETE

  ```ABAP
DELETE FROM <dbtab> WHERE <condition>.
DELETE <dbtab> FROM TABLE <itab>.
  ```

#### MODIFY(相当于Update & Insert)    

  ```ABAP
MODIFY <dbtab> FROM <wa>.  
MODIFY <dbtab> FROM TABLE <itab>.
  ```

#### 游标使用

- 打开游标：`OPEN CURSOR cursor_name FOR ...`
- 读取游标：`FETCH NEXT CURSOR cursor_name INTO wa.`
- 关闭游标：`CLOSE CURSOR cursor_name.`

```ABAP
TABLES mara.
DATA: cursor_mara TYPE cursor,
      wa LIKE mara.
OPEN CURSOR cursor_mara FOR 
  SELECT * FROM mara 
   WHERE matnr LIKE '%30198390%'
   ORDER BY PRIMARY KEY.
DO.
  FETCH NEXT CURSOR cursor_mara INTO wa.
  IF SY-SUBRC <> 0.
    CLOSE CURSOR cursor_mara.
    EXIT.
  ENDIF.
  WRITE: / wa-matnr,wa-mtart,wa-matkl,wa-aenam.
ENDDO.
```

