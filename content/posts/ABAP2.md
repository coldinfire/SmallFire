---
title: "ABAP 语法详解(内表)"
date: 2018-05-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abap

---

## 内表定义和使用

### 内表定义

**工作区域：**工作区域可以存放多个变量数据。

​	直接定义：`DATA: BEGIN OF <str>   END OF <str>.`

​	参照DB或则结构：`DATA <wa> TYPE <dbtab>|<str>.`

​	参照内表：`DATA  <wa>  LIKE LINE OF <dbtab>.`

​	继承结构：

```JS
DATA: BEGIN OF <str>.
  INCLUDE STRUCTURE <str_Other>.
  DATA: <ele> TYPE <db-ele> ,
END OF <str>.
```
 **内表类型：**

​	索引内表：标准内表(Standard table)、排序表(Sorted table)
​      

​	标准内表：每一行数据都有关键字和系统生成的逻辑索引。
​     

​	排序表：排序表按照关键字升序排序后再进行存储。

​	哈希表：Hashed table，没有索引的表，只能靠关键字进行寻址，用哈希算法管理数据。

**内表定义：**可以参考结构体、其他内表、透明表
    

​	参考结构：`DATA <table_name> TYPE STANDARD TABLE OF <structure> [WITH HEADER LINE].
  `  

​	参考内表：`DATA <table_name> TYPE TABLE OF <内表或透明表> [WITH HEADER LINE].`

   - with header line,用 itab[] 和 itab 来区分内表和工作区
   - 分别定义内表和工作区
   - 定义结构时用occur 0直接定义
        . 
   - LIKE LINE OF后面接一个内表，表示一个data参数具有和内表一样的结构，可当做Work Area使用
   - LIKE TABLE OF后面接一个结构，表示一个data参数是一个内表，这个内表和后面接的结构一样

### 内表操作

**内表清空：**
     

​	`CLEAR <ITAB>`：仅清空HEADER LINE，对内表数据存储空间不影响。

​    `REFRESH <ITAB>`：清空内表数据存储空间，对HEADER LINE不影响。

​    `REFRESH <itab> FROM TABLE <dbtab>`：清空内表存储空间，填充从数据库表所获数据。
​    

   `FREE <ITAB>`：清空内表数据存储空间，对HEADER LINE不影响。

**APPEND(增加，内表赋值)**
    

​	有HEADER LINE内表，数据被赋给内表HEADER LINE后再APPEND到表中最后一行。
​      

- `APPEND ITAB.
  `    

​    对于没有HEADER LINE的内表，只能通表外部WORK AREA来传递数据。
​      

- `APPEND (<work area> into) <ITAB>.`

**INSERT(向内表插入数据)**
    

​	INSERT itab INDEX idx. :若itab有多行数据，将新记录新增到第一行

​    INSERT wa INTO TABLE itab.   :将结构体中数据新增到内表

​    INSERT LINES OF itab1 [FROM idx] [TO idx2] INTO itab2 [INDEX idx3].:将itab1指定范围数据插入到itab2

**DELETE(删除数据)**

​	DELETE TABLE itab WITH TABLE KEY k1=v1...kn=vn.：按具体值删除。

​    DELETE TABLE itab [FROM wa].：参照其它内表值删除。

​    DELETE itab INDEX idx.：根据索引删除具体行数据。

​    DELETE itab FROM idx1 TO idx2.：根据索引删除具体行数范围间数据。

​    DELETE ADJACENT DUPLICATES FROM itab.：删除重复数据，执行此条件前必须先排序。

**MODIFY:(修改内表数据)**
    

​	按内表位置或者具体内表字段值相等条件修改内表数据。
​    

​	MODIFY itab [FROM wa] [INDEX idx] [TRANSPORTING f1...fn] WHERE cond.

 **LOOP....ENDLOOP：（循环读取内表数据)**

​    循环读取内表数据，在循环中使用系统变量SY-TABIX可获取当前所执行的行数。

​    LOOP AT ITAB [INTO WA] FROM n1 TO n2.：读取内表具体行数间数据。

​    LOOP AT ITAB [INTO WA] WHERE cond.：按具体字段条件读取内表。

**AT...ENDAT（设置内表循环触发条件）**

​    该语法为事件控制函数，应用于LOOP循环语句中，用于获取内表的数据变化事件。

​    AT NEW f.：当某个字段数据与上一行数据值不同时触发该事件。

​    AT END OF f.：当内表中某个字段当前行值与下一行值不同时触发该事件。

​    AT FIRST.：当执行内表第一行时触发该事件。

​    AT LAST.：当执行内表最后一行时触发该事件。

**READ:(读取)**

​    READ TABLE itab [INTO wa] FROM wa.

​    READ TABLE itab [INTO wa] WITH [TABLE] KEY k1=v1...kn=vn [BINARY SEARCH].

​    READ TABLE itab [INTO wa] INDEX i.

**COLLECT（内表数据分类汇总）**

​    将内表中相同的字段合并，若有类型为`I`的字段，则将其值加总。

- COLLECT [wa INTO] itab.

**DESCRIBE（获取内表具体属性）**

- DESCRIBE TABLE itab LINES n：获取内表当前总行数，n为整型(i)。

### **Table:透明表(Transparent table)、簇表 (Cluster table)、 池表(Pool table)**

​    **透明表：**和数据库具有相同结构的表存储结构。数据库中有一个相应得物理表。

​    **簇表：**该表在SAP Dict展现在我们眼前的结构，由透明表转化<Extras—Change table category>从单个表去理解与透明表没有差异，但是多个表组成簇表，它们在物理上对主键只存储一遍，故对簇表的关联查询可以极大提高访问速度。
​    

​	**池表：**由透明表转换，可用来存储控制数据，与数据库中的表是多对一关系。

- INSERT 透明表 INTO 簇表。 只要透明的簇表的主键都在透明表里面就行。就是透明表主键多了也无所谓。

- INSERT 透明表 INTO 池表. 透明表和池表的主键必须相同的。
      

​	**表簇：**几个簇表可以组合成一个表簇，几个簇表存贮在数据库中一个相应的表里，DB层的物理表。

​	**结构：**结构在数据库不存在数据记录。结构用于在程序之间或程序与屏幕之间的接口定义。
​    

​	**附加结构：**附加结构定义字段的子集，该字段属于其他表格或结构，但是在修正管理中作为单独的对象。

### SAP数据表的组成元素

**Data element：**构成结构、表的基本组件 search help、parameter ID、标签描述。Goto > Documentation > Change ：修改提示文档信息

**Domain：**定义数据元素的技术属性，类型，长度，精度。

​	 Definition：
​       

- Format、Output Charact
  
- Converse Routine(转换规则)：注意前导0的补充问题，将该字段设置为`ALPHA`。
  
- Lower Case：不勾选，默认会全部转换为大写字母。

- value range：设置该Domain的固定取值列表和其含义,C,Y,F类型中的一种。

  ```JS
  DEC:double    FLTP:Float  INT1:0~255  INT2:-32768~32767     INT4:4字节   
  NUMC:数字字符(1-255)   CHAR:字符(1-255)    STRING
  CURR:货币字段(1-17)    CUKY:货币代码(5)     QUAN:金额(1-17)  UNIT:单位(2-3)   
  DATS:Date(8)          TIMS:Time(6)       CLNT:Client(3)    
  LANG:Language(internal 1,external 2)
  ```

**Field:**透明表字段，可以作为透明表的主 / 外键，继承了 Data Element 的所有属性。

​	透明表中的Technical Setting设置Log data changes后可以使用SCU3，STAD查看日志。【非特殊情况不设置，会占用很大的内存】

#### 数据分类

- **Master Data**：业务主数据；即：SAP 各模块中的主数据：总账科目、供应商主数据、物料主数据等；

- **Transaction Data**：业务处理数据；在业务处理操作中，生成的凭证号等数据，如：销售订单凭证号、采购订单凭证号、收货凭证号等；

- **System Data**：系统数据；包括 ABAP 程序的源码、元数据、文档等数据；

- **Configuration Data**：配置数据；存放企业项目实施时，配置的初始化信息，如：货币汇率、订单类型、变式等

### 维护ABAP数据字典

- **SE80 – Repository Browser**

- **SE15 – Repository Information System**

- **SE16 /SE16N – Data Browser**

- **SE11 – ABAP Dictionary**

- **SE13 – Dictionary technical settings**

- **SM30 – Maintain Table Views**

- **SM31 – Table Maintenance**

### 表维护创建

```JS
SM30：表维护 Utilities ----->Table maintenance generator
          Technical Dialog Details
          Authorization Group：&NC&
          Function group：ZFG_tablename
          Maintenance Screens : 
          Maintenance type：one step
```

未完待续......[ABAP 语法详解(Field&Form)](https://coldinfire.github.io/2018/ABAP3)

参考.......[报表开发<内表操作>](https://coldinfire.github.io/2018/ABAPTable3)

