---
title: "ABAP 语法详解(SQL)"
date: 2018-05-24T17:20:58+08:00
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abap

---

## MESSAGES

SE91定义:

  设置消息类，保存该Function的多条消息定义，通过‘&’定义多个占位符。

![定义消息类](/images/ABAP/SE91.jpg)

MESSAGE E001(ZTEST).

​	E:消息显示类型 (E[Error],W[Warning],I[Info],A[异常终止],S[Success])

​	001:自定义的消息字段

​	ZTEST:自定义的消息类

MESSAGE显示:

1. ID : "00"消息ID中的001消息本身未设置任何消息串，这条消息可以传递8个参数。

2. 消息常量 : MESSAGE 'xxxxxxxxxxxxxxx' TYPE 'S' [DISPLAY LIKE 'E']. 

## 数据格式化、转换

- 输入输出转换

  如果某个变量参照的数据所对应的Domain具有转换规则，在(Write,ALV,文本框显示)，最后结果会自动转换。

  通过转换规则输入输出函数手动转换，转换公式。CONVERSION_EXIT_ALPHA_INPUT/OUTPUT(前面补齐0，去掉前导0).

  去除前导0：`SHIT ITAB-FIELD LEFT DELETING LEADING ‘0’`

  ```JS
  ** 添加前导零 **
  CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
      EXPORTING
        input  = p_in     //传入变量
      IMPORTING
        output = p_out.   //添加前导零后数据
        
  ** 去除前导零 ** 
  CALL FUNCTION 'CONVERSION_EXIT_ALPHA_OUTPUT'
      EXPORTING
        input  = p_in     //传入变量
      IMPORTING
        output = p_out.   //去除前导零后数据
  ```

- 显示内容

   	基本数据类型是QUAN,其小数位由字段关联的度量衡单位决定。

      ALV显示时，如果是金额或数量时，需通过Fieldcat设置cfieldname、ctabname等才会正确显示。

- 单位换算

    UNIT_CONVERSION_SIMPLE

- 货币转换因子

```JS
CURRENCY_CONVERTING_FACTOR：输入币种，可以得到相应的转换比率。SE16中看到数据的经过转换后存入，取出时应做转换
BAPI_CURRENCY_CONV_TO_INTERNAL：转换为数据库中内部存储金额
BAPI_CURRENCY_CONV_TO_EXTERNAL：转换成外部的实际金额
CONVERT_TO_LOCAL_CURRENCY：自动将最近时间多的汇率作为转换的汇率
OB07、OB08：维护各币种之间的汇率
CONVERT_TO_FOREIGN_CURRENCY：将外币转换为本位币
CONVERT_TO_LOCAL_CURRENCY：将本位币转换为其他外币
```

## Open SQL

- ABAP可以通过两种方式与数据库交互

    - Native SQL:数据库自身的SQL，可以直接访问数据库，不够安全。


    - Open SQL:集成到ABAP中的标准SQL子集，通过SAP数据库接口识别不同的数据库，然后将语句进行转换成底层数据库对应的语言。

- 基本语法：与其他SQL语句类似，主要包括增删改查：Select、Update、Insert、Delete、Modify

  ​	<1> SELECT 语法  

  ```JS
  SELECT [SINGLE] <result> FROM <dbtab> ：SINGLE表示查询单行数据
         INTO <target>                ：内表或则结构
         [INTO <f1>...<fn>]           :将查询结果赋值到具体字段
         [INTO CORRESPONDING FILES OF <itab>]
                                       : 将查询结果按字段匹配赋值给具体的表或者结构体。
         WHERE <condition>            ：查询条件
         Group BY <fields>            ：分组查询条件
         ORDER BY <fields>.           ：排序条件 
  ```

  **表连接：**

  ```JS
  INNER JOIN：查询结果包含两个连接表中彼此相对应的数据记录。
  LEFT OUTER JOIN：查询结果集中包含左表中的所有数据记录，右表中仅查询出包含相匹配的数据。
  FULL OUTER JOIN：包含左右表所有的记录。
  ```


  以内表为查询条件：

  ​         `SELECT <f1...fn> FROM <dbtab> FOR ALL ENTRIED IN <itab> WHERE <cond>.`

  ​         使用 `FOR ALL ENTRIED IN itab` 前，一定要检查itab表是否为空，否则会造成SQL的执行效率极低。

  ​       获取限制读取数据的条数：前n条

​          	 `SELECT * FROM <dbtab> INTO <itab> UP TO <n> ROWS. `

  标准函数：

  ```JS
  COUNT()：统计查询总数。
  SUM()：统计表中某个数值字段的总和。
  AVG()：统计表中某个数值字段的平均值。
  MAX()：统计表中某个字段的最大值。
  MIN() ：统计表中某个字段的最小值。
  ```

  ​     <2> UPDATE（修改更新操作）

  ```JS
  UPDATE <dbtab> SET: f1...fn (WHERE <condition>).    
  UPDATE <dbtab> FROM TABLE <itab> (WHERE <condition>).
  ```

  ​     <3> INSERT(插入数据)

  ```JS
  INSERT INTO <dbtab> VALUES <conditin>.
  INSERT <dbtab> FROM TABLE <itab>.
  ```

  ​     <4> DELETE(删除操作)

  ```JS
  DELETE FROM <dbtab> WHERE <condition>.
  DELETE <dbtab> FROM TABLE <itab>.
  ```

  ​     <5> MODIFY(修改操作，相当于Update & Insert)    

  ```JS
  MODIFY <dbtab> FROM TABLE <itab>.
  ```

  

------

未完待续......[ABAP 语法详解(优化)](https://coldinfire.github.io/2018/ABAP5/)

