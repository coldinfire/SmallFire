---
title: " ABAP 模块化使用 "
date: 2018-05-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbasis

---

### 代码模块化

当模块化源代码时，可以将一个完整功能的 ABAP 语句放置在一个模块中。 然后在后续编写程序时，不必将所有语句都放在主程序中，而只需调用模块。生成程序时，模块化单元中的源代码被视为实际存在于主程序中。

#### 模块化的优点

- 改进程序的结构
- 易于阅读的代码
- 易于维护和更新代码
- 避免冗余并促进代码重用

#### 各种模块化技术

- Macros：宏的使用
- Include：包含文件的使用
- Subroutines：Form 子程序的使用
- Function Modules：功能模块的使用

### 全局变量，局部变量

#### 全局变量

其他功能块里声明的变量属于全局变量（报表事件块、列表事件块、对话 module），效果与在程序开头定义的变量一样。

#### 局部变量

报表程序中选择屏幕事件块(AT SELECTION-SCREEN)、逻辑数据库事件块，以及 macro、methods、subroutines (FORM子过程)、Function Modules 中声明的变量为局部变量。

### ABAP Macro

如果要在程序中多次重复使用同一组语句，可以将它们包含在宏中。

***只能在定义它的程序中使用宏，并且只能在其定义之后的程序行中调用它。***

通过宏来设置 FIELDCATALOG 字段属性，可以很好的提示代码编写效率：

```ABAP
DEFINE fieldcat.  
  clear gs_fieldcat.  
  gs_fieldcat-fieldname = &1.  
  gs_fieldcat-seltext_l = &2.  
  gs_fieldcat-seltext_m = &2.  
  gs_fieldcat-seltext_s = &2.
  APPEND gs_fieldcat TO gt_fieldcat.
END-OF-DEFINITION.
fieldcat 'CARRID' '航线承运人'.
```

### Include Programs

语法：`INCLUDE <include program name>.`

- 包含程序不能调用自己本身
- 包含程序必须包含完整的可执行语句

Include Programs 仅用于模块化源代码，没有参数接口。

包含程序允许在不同的程序中使用相同的源代码。 如果想在不同的程序中使用冗长的数据声明，它们会很有用。

### Subroutines

子程序是可以在任何 ABAP 程序中定义并且也可以从任何程序调用的过程。 子程序通常在内部调用，也就是说，它们包含在本地经常使用的代码或算法部分。

 如果你希望某个功能在整个系统中可重用，请使用 Function Modules。

- 子程序中允许嵌套调用（即 PERFORM 在 FORM ... ENDFORM 中），递归调用也是可能的。

#### 定义子程序

`FORM <Subroutine> [<pass>].  <Statement block>. ENDFORM.`

Form 中的参数解析：

- 形式参数：在定义子程序时用 FORM 语句定义。


- 实际参数：在使用 PERFORM 语句调用子程序期间指定。


TABLES：Type 和 Like 只能参照标准内表类型或标准内表对象

- `FORM <name> TABLES itab1...itabn. `：以表的方式传输数据

USING：值传递，对形参的修改不会改变实参

- ` FORM <name> USING [p1....pn].`

CHANGING：使用排序表或则哈希表，如果 CHANGE 值传递，对形参的修改还是会改变实参，在 Form 或则 Function 执行完后才去修改

- `FORM <name> CHANGING [p1....pn].`

#### 调用方式

调用指定程序中的子程序：不同的ABAP程序中的子程序是可以共用的。

- `PERFORM <subroutine>(<Program>) [<pass>] [IF FOUND].`

- `PERFORM (<subroutine>) IN PROFRAM (<prog_name>) [<pass>]  [IF FOUND].`
- `PERFORM <index> OF <subroutine1> <subroutine2> <subroutine3> [<pass>].`

通过TCode调用指定程序中的子程序

- `CALL TRANSACTION <TCode>.  `

使用SUBMIT调用另一个程序：

- `SUBMIT <program_name>.`


-  ...`USING SELECTION-SCREEN <SCR>`. 调用子屏幕
-  ...`VIA  SELECTION-SCREEN.`  显示所调用程序的初始屏幕
-  ...`AND RETURN.` 调用指定程序执行后可返回上一屏幕

### Function Modules （SE37）

功能模块是任何人都可以使用的通用 ABAP/4 例程。 事实上，有大量的标准功能模块可用。

功能模块被组织成功能组：逻辑相关功能的集合。 一个功能模块总是属于一个功能组。

#### 定义

```ABAP
FUNCTION <function module>.
  <Statements>
ENDFUNCTION.
```

#### 使用

```ABAP
CALL FUNCTION <module>
  [EXPORTING  f1 = a1 ... fn = an]
  [IMPORTING  f1 = a1 ... fn = an]
  [CHANGING   f1 = a1 ... fn = an]
  [TABLES     f1 = a1 ... fn = an]
  [EXCEPTIONS e1 = e1 ... en = en 
    [ERROR_MESSAGE = r E]   
    [OTHERS = ro]].
```

#### Function Groups 

功能组是功能模块的容器。SAP 系统中有大量的标准功能组。一个功能组中的所有功能模块都可以访问该组的全局数据。

- 无法执 Function Groups 
- Function Groups 的名称最长可达 26 个字符

创建 Function Group 时，系统会自动创建 Main program 与相应的 include 程序。

- `SAPL<fgrp>`：主程序名，将Function Group里的所有的Include文件包括进来，只有Include语句

- `L<fgrp>TOP`：有FUNCTION-POOL语句，以及所有Function model都可以使用的全局数据定义

- `L<fgrp>UXX`：只有 include 语句，为相应具体 Function Model 所对应的 Include 文件名：`L<fgrp>U01...`包含了对应的 FM 代码。

- `L<fgrp>U01`：01,02编号对应 UXX，代表其创建先后的序号。

- `L<fgrp>FXX`：用来存放一些 Form 子程序，可以被所有 Function Modules 所使用。

- `L<fgrp>PAI L<fgrp>PBO`：PAI,PBO事件。

### AT...END (SUM)

AT...END：中的组件名不一定要是结构中的关键字段，但是需要在声明结构时按调用顺序排序。

- f1，f2，fn.  f1，f2，fn

SUM：根据结构中对应的字段进行分组求和，且指定字段之前的依次分组。

### 事件终止

#### RETURN

用来退出当前执行的程序块，而不仅仅是退出循环。如FORM，METHOD，报表事件块

#### STOP 

- INITIALIZATION 中 STOP 会导致跳转到 AT SELECTION-SCREEN OUTPUT 事件块

- 如果 STOP 在 AT SELECTION-SCREEN OUTPUT 块里，则只是退出当前块，STOP 后面语句不执行而已

- 如果 STOP 在循环中，FORM、METHOD 直接从被调用的点退出所在事件块

#### EXIT

INITIALIZATION 中的 EXIT 会跳转到 AT SELECTION-SCREEN OUTPUT 事件

- 如果 EXIT 在 AT SELECTION-SCREEN OUTPUT 块里，则只是退出当前块，EXIT 后面语句不执行而已

- 如果 EXIT 在循环中，只是跳出当前循环而已；循环之外的模块则与 RETURN 类似

#### CHECK

- CHECK 只是跳出当前事件块，继续下个事件块的处理，相当于方法的 RETURN

- CHECK 在循环中，只是跳出循环（DO,WHILE,LOOP）类似于 CONTINUE，在循环外则跳出当前执行的程序块

#### LEAVE 

- `LEAVE PROGRAM.`：退出整个程序

- `LEAVE TO TRANSACTION <tcode>.`：跳转到另外的 TCode

- `LEAVE LIST-PROCESSING.`：从 List Processor 回到 Dialog processor

- `LEAVE TO LIST-PROCESSING. `：控制权从 Dialog processor 转交到 List processor

- LEAVE {SCREEN | {TO SCREEN dynnr}}

#### REJECT

用在逻辑数据库 GET event blocks 中，可以从循环或则一个 FORM 中直接跳出所在的 GET 事件块。