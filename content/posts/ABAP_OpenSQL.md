---
title: "ABAP Open SQL"
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

- Native SQL：数据库自身的 SQL，可以直接访问数据库，不够安全。
- Open SQL：集成到ABAP中的标准 SQL 子集，通过 SAP 数据库接口识别不同的数据库，然后将语句进行转换成底层数据库对应的语言。

基本语法：与其他SQL语句类似，主要包括增删改查：Select、Update、Insert、Delete、Modify

#### SELECT 语法  

```JS
SELECT [SINGLE] <result> FROM <dbtab> :SINGLE表示查询单行数据
  INTO <target>                :内表或结构
  [INTO <f1>...<fn>]           :将查询结果赋值到具体字段
  [INTO CORRESPONDING FILES OF <itab>] :将查询结果按字段匹配赋值给具体的表或者结构体
  WHERE <condition>            :查询条件
  GROUP BY <fields>            :分组查询条件
  ORDER BY <fields>.           :排序条件 
```

表连接:

```JS
INNER JOIN:查询结果包含两个连接表中彼此相对应的数据记录.
LEFT OUTER JOIN:查询结果集中包含左表中的所有数据记录,右表中仅查询出包含相匹配的数据.
连接语句:
  使用 ON 和 AND 语句将左右两个表关联起来,一般使用主键.
  INNER JOIN不能使用NOT,LIKE,IN;
  LEFT OUTER JOIN只能使用 EQ;
WHERE语句:
  至少有一个表达式的两个操作数分别来自于左右两个表建立连接关系
  LEFT OUTER JOIN的右表字段不能在WHERE中使用 
```

以内表为查询条件：

-  `SELECT <f1...fn> FROM <dbtab> FOR ALL ENTRIED IN <itab> WHERE <cond>.`


- 使用 `FOR ALL ENTRIED IN itab` 前，一定要检查itab表是否为空，否则会造成SQL的执行效率极低。


获取限制读取数据的条数：前n条

-  `SELECT * FROM <dbtab> INTO <itab> UP TO <n> ROWS. `

  标准函数：

| 函数    | 描述                         | 函数  | 描述                     |
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



