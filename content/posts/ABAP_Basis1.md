---
title: "ABAP 数据类型"
date: 2018-05-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abap

---

### ABAP基本数据类型

  基本数据类型

| C : Character text                 | D : Date(YYYYMMDD)     | P : Packed(包类型:1-16) |
| :--------------------------------- | :--------------------- | :---------------------- |
| **N : Numeric text(不能进行计算)** | **T : Time(HHMMSS)**   | **X : 十六进制**        |
| **I : Interger**                   | **F : Floating point** |                         |

   常用系统变量：

| SY-UNAME:用户登录名     | SY-DATUM:当前系统日期                 | SY-UZEIT:当前系统时间 |
| :---------------------- | :-------------------------------- | :--------------------- |
| **SY-SUBRC:表示系统执行成功与否** | **SY-INDEX:DO-ENDDO 中是有效的** | **SY-TABIX:LOOP索引，Read内表索引** |
| **SY-DYNNR:屏幕的编号** | **SY-DBCNT:DB操作处理过的表行号** | **SY-HOST:服务器名称** |
| **SY-CPROG:当前程序名** | **SY-TCODE:当前执行的TCode** | **SY-TMAXL:内表总行数** |

### 变量的声明
- 透明表，数据字典，结构：既是类型又是对象，可用type和like。

- 只能使用LIKE引用另一定义变量的类型，type不可以

```JS
<1> DATA <var>(len) TYPE <type> VALUE <value> [<decimals>]. <自定义变量类型>
<2> DATA <var1> like  <var2> . <参考定义变量>
	   DATA <var1> type <var2>. 
```

- 继承结构

```JS
DATA:BEGIN OF STAFFINFO. <此处是.操作符>
    INCLUDE STRUCTURE USER_INFO.
 DATA:BIRTHDAY TYPE D,
    ADDRESS(50) TYPE C,
 END OF STAFFINFO.
```

### 定义常量、宏

**常量定义**

​	  `CONSTANTS <var>(len) TYPE <type> VALUE <value>.`

​	  `CONSTANTS <var>(len) LIKE <var2> VALUE <value>.
`

**宏定义** 

定义：

```ABAP
DEFINE alv_ref1.
  clear gw_field.
  gw_field-fieldname = &1.
  gw_field-coltext  = &2.
  gw_field-outputlen = &3.
  gw_field-ref_field = &4.
  gw_field-ref_table = &5.
  gw_field-edit = &6.
  gw_field-f4availabl = &7.
 
append gw_field to gt_field.
ENDDEFINE alv_ref1.
```
使用：



###  DESCRIBE使用

`DESCRIBE TABLE lt_mat LINES lv_cont.`：这行的意思是 计算内表 lt_mat 的行数 ，将行数放到变量 lv_cont 里。

字段属性：`DESCRIBE FIELD <field> [mes var]...
 `     (一个data的类型、长度、小数点、输出长度等信息)

内表：`DESCRIBE TABLE itab [kind knd] [LINES lin] [COCCURS n].`

两个字段不同：`DESCRIBE distance ...`

### MESSAGE ：SE91

 **消息类的操作**

​	使用T-CODE:SE91对Message定义，还能够对Message进行创建，修改及删除等维护操作。Message Short Text字段为类描述，可以定义输入参数&，通过‘&’定义多个占位符,如"1&2&3&"表示有三个输入参数。

![定义消息类](/images/ABAP/SE91.jpg)

MESSAGE E001(ZTEST).

​	E:消息显示类型 (Message共分以下几种类型：E:错误、W:警告、I：信息、A：异常中止、S:成功)

​	001:自定义的消息字段

​	ZTEST:自定义的消息类

MESSAGE显示:

```JS
EX: Message W001(ZTEST) WITH 'P1' 'P2' 'P3'.
	1. 消息ID MESSAGE e001(00) WITH '12345678'. //利用定义的参数
	2. MESSAGE 'XXXXXXXXXX' TYPE 'X'.          //直接附加消息
	3. MESSAGE s001(00) WITH 'No data' DISPLAY LIKE 'E'.
   	   EXIT.                                   //Screen 界面查询数据无，则返回原界面
```

消息存储的内表：T100/T100C/T100S/T100U/T160M

- T100 这个表包括所有的消息
- T100C 通常包括修改后的消息，即修改默认消息类型后的值存在该表中
- T100S 就是表示可以修改消息类型的表

