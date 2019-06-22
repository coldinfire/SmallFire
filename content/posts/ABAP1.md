---
title: "ABAP 语法详解(一)"
date: 2018-05-12T17:20:58+08:00
draft: true
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abap

---

## ABAP基本数据类型

  基本数据类型

| C : Character text                 | D : Date(YYYYMMDD)     | P : Packed(包类型:1-16) |
| :--------------------------------- | :--------------------- | :---------------------- |
| **N : Numeric text(不能进行计算)** | **T : Time(HHMMSS)**   | **X : 十六进制**        |
| **I : Interger**                   | **F : Floating point** |                         |

   常用系统变量：

| sy-uname:用户登录名     | sy-datum:日期                     | sy-uzeit:时间          |
| :---------------------- | :-------------------------------- | :--------------------- |
| **sy-subrc:**报表返回值 | **sy-index:循环编号**             | **sy-tabix:循环编号**  |
| **sy-dynnr:屏幕的编号** | **sy-dbcnt:DB操作处理过的表行号** | **sy-host:服务器名称** |

## 变量的声明
- 透明表，数据字典，结构：既是类型又是对象，可用type和like。

- 只能使用LIKE引用另一定义变量的类型，type不可以

```JS
<1> DATA <var>(len) TYPE <type> VALUE <value> . <自定义变量类型>
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



## 定义常量、宏
```JS
常量定义
  CONSTANTS <var>(len) TYPE <type> VALUE <value>.
  CONSTANTS <var>(len) LIKE <var2> VALUE <value>.
宏定义 
  1. 定义：
    DEFINE operation.
      result = &1 &2 &3.
      output   &1 &2 &3 result.
    END-OF-DEFINITION.

    DEFINE output.
      write: / 'The result of &1 &2 &3 is', &4.
    END-OF-DEFINITION.
  2. 使用：
    operation 4+3.
```
##  DESCRIBE使用
```JS
DESCRIBE TABLE lt_mat LINES lv_cont.
  这行的意思是 计算内表 lt_mat 的行数 ，将行数放到变量 lv_cont 里。
Field Properties:describe field <field> [mes var]...
                                (一个data的类型、长度、小数点、输出长度等信息)
Internal Table:describe table itab [kind knd] [LINES lin] [COCCURS n].
Distance two fields:describe distance ...
```


未完待续......[ABAP语法详解(二)](https://coldinfire.github.io/2019/ABAP2)