---
title: "报表开发<概述>"
date: 2018-08-26T17:20:58+08:00
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - 报表开发

---

## 执行程序的使用范围，报表事件

- LOAD-OF-PROGRAM.

- INITIALIZATION.          （Before display the selection screen）

  - AT-SELECTION SCREEN ON fiedl.（在PAI事件结束后执行，进行校验和检查输入值）

  - AT SELECTION-SCREEN ON VALUE-REQUEST FOR Z_XXX.

- AT SELECTION-SCREEN. //After enter the option data check

  - ​	PERFORM check_input.

- START-OF-SELECTION.//Begin the main programer

  - xxxx

- END-OF-SELECTION.

- Interactive Eventrs. (User for interactive reporting)

## 报表程序大体逻辑结构
​	**抬  头:** 报表的主要信息(抬头信息)

​	**行项目:** 查询出的每行记录信息

​	**明  细:** 每个行项目出关键字外其他明细内容

## 字段符号

**定义：** FIELD-SYMBOLS <FS> LIKE structure.

​    <1> FS必须和某个变量，结构或者内表绑定后才能使用，在使用FS前必须分配给某个变量，不然会发生FS未分配的运行时错误。如果LOOP内表时ASSIGNING到FS，之后假如有REFRESH内表的操作的话，FS也会再次回到初始未被ASSIGN的状态，这时如果使用FS也会发生FS未分配的RUNTIME ERROR。

​	<2> `ASSIGN structure TO <fs>`:将某个内存区域分配给字段符号，这样字段符号就代表了该内存区域，即该内存区域别名字段符号可以看作仅是已经被解引用的指针，即某个变量的别名。
  `ASSIGN <Val> TO <fs>`: 将某个内存区域分配给字段符号，这样字段符号就代表了该内存区域。

UNASSIGN: 该语句是初始化`<FS>`字段符号，执行后字段符号将不再引用内存区域，`<fs> is assigned`返回假。

CLEAR: 与UNASSIGN不同的是，只有一个作用就是初始化它所指向的内存区域，而不是解除分配。

**循环：**LOOP内表INTO Structure和 LOOP内表ASSIGNING Structure的比较

​    LOOP内表INTO结构时，系统会把先把当前行的数据复制到结构，如果结构的值改了，还需要使用
  MODIFY语句把更改后的值传回内表。也就是说，结构是内表里的数据的一个副本，操作这个副本不会
  影响内表里的数据。为了提高效率，可以使用FS，FS直接指向内表数据，省去了复制数据到结构的过程
  修改FS的值也就是相当于直接修改内表里的数据，不需要再使用MODIFY语句。

**读表：**READ TABLE内表

ASSIGN隐式强转

```JS
TYPES: BEGIN OF t_date,
​    year(4) TYPE  n,
​        month(2) TYPE n,
​    day(2) TYPE n,
  END OF t_date.
FIELD-SYMBOLS <fs> TYPE t_date."将<fs>定义成了具体限定类型
ASSIGN sy-datum TO <fs> CASTING. "后面没有指定具体类型，所以使用定义时的类型进行隐式转换
```

ASSIGN显示强转

```JS
  DATA txt(8) TYPE c VALUE '19980606'.
  FIELD-SYMBOLS <fs>.
  ASSIGN txt TO <fs> CASTING TYPE d."由于定义时未指定具体的类型，所以这里需要显示强转
```

**动态引用：**通过循环赋值给定义的字段符号，对其进行修改，等于直接修改原内表。

```JS
  field-symbols:<l_shortageqty> type mng01.
  loop at <dyn_table> assigning <dyn_wa>.
​    assign component 'SHORTAGEQTY' of structure <dyn_wa> to <l_shortageqty>.
​    <l_shortageqty> = <l_shortageqty> - <l_fvalue>.
```

## MESSAGE ：SE91
<1> 消息类的操作

​	使用T-CODE:SE91对Message定义，还能够对Message进行创建，修改及删除等维护操作。Message Short Text字段为类描述，可以定义输入参数&，如"1&2&3&"表示有三个输入参数。

​	Message共分以下几种类型：E:错误、W:警告、I：信息、A：异常中止、S:成功。

​	引用语法为：Message W000(00)，表示调用'00'类的'000' Message类型为警告。

```JS
EX: Message W001(ZTEST) WITH 'P1' 'P2' 'P3'.
	1. 消息ID MESSAGE e001(00) WITH '12345678'. //利用定义的参数
	2. MESSAGE 'XXXXXXXXXX' TYPE 'X'.          //直接附加消息
	3. MESSAGE s001(00) WITH 'No data' DISPLAY LIKE 'E'.
   	   EXIT.                                   //Screen 界面查询数据无，则返回原界面
```







