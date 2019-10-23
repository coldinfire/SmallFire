---
title: "ABAP 语法详解(Form&Func)"
date: 2018-05-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abap

---

### 全局变量，局部变量
​	报表程序中：选择屏幕事件块(AT SELECTION-SCREEN),逻辑数据库事件块，及methods，subroutines (FORM子过程)、Function Modules中声明的变量为局部的。

​	其他块里的变量属于全局的（报表事件块、列表事件块、对话module）效果与在程序开头定义的变量一样。

### Form、Function

#### Form中的参数：

1. TABLES：Type和like只能接标准内表类型或标准内表对象
       

   - `FORM <name> TABLES itab1...itabn. `：以表的方式传输数据

2. USING：值传递，则对形参的修改不会改变实参

   - ` FORM <name> USING [p1....pn].`

3. CHANGING:使用排序表或则哈希表，如果CHANGE值传递，对形参的修改还是会改变实参，在form或则function执行完后才去修改

   - `FORM <name> CHANGING [p1....pn].`

4. 调用指定程序中的子程序：不同的ABAP程序中的子程序是可以共用的

   - `PERFORM <sub_name> [CHANGING p1..pn] IN PROFRAM <prog_name>.`

5. 通过TCode调用指定程序中的子程序

   - `CALL TRANSACTION <TCode>.  `

6. 使用SUBMIT调用另一个程序：

    SUBMIT <程序名>.
    

   -  ...`USING SELECTION-SCREEN <SCR>`. "调用子屏幕
      
   - ...`IVA  SELECTION-SCREEN.`        "显示所调用程序的初始屏幕
     
   -  ...`AND RETURN.`    "调用指定程序执行后可返回上一屏幕

#### Function Group （SE37）

  系统会自动创建Main program与相应的include程序。

​    `SAPL<fgrp>：`主程序名，将Function Group里的所有的Include文件包括进来，只有Include语句

​	`L<fgrp>TOP：`有FUNCTION-POOL语句，以及所有Function model都可以使用的全局数据定义

​	`L<fgrp>UXX：`只有include语句，为相应具体Function Model所对应的Include文件名：`L<fgrp>U01...`包含了对应的FM代码。

​	`L<fgrp>U01：`01,02编号对应UXX，代表其创建先后的序号。

​	`L<fgrp>FXX：`用来存放一些Form子程序，可以被所有Function Modules所使用。

​	`L<fgrp>PAI，L<fgrp>PBO：`PAI,PBO事件。

### AT...END (SUM)

1. AT...END：中的组件名不一定要是结构中的关键字段，但是需要在声明结构时按调用顺序排序。
      - f1，f2，fn.  f1，f2，fn
2. SUM:根据结构中对应的字段进行分组求和，且指定字段之前的依次分组

### 事件终止

- RETURN : 用来退出当前执行的程序块，而不仅仅是退出循环。如FORM，METHOD，报表事件块

- STOP : 

  - INITIALIZATION中STOP会导致跳转到AT SELECTION-SCREEN OUTPUT事件块

  - 如果STOP在AT SELECTION-SCREEN OUTPUT块里，则只是退出当前块，STOP后面语句不执行而已

  - 如果STOP在循环中，FORM，METHOD直接从被调用的点退出所在事件块

- EXIT : INITIALIZATION中的EXIT会跳转到AT SELECTION-SCREEN OUTPUT事件

  - 如果EXIT在AT SELECTION-SCREEN OUTPUT块里，则只是退出当前块，EXIT后面语句不执行而已

  - 如果EXIT 在循环中，只是跳出当前循环而已；循环之外的模块则与RETURN类似

- CHECK :

  - CHECK只是跳出当前事件块，继续下个事件块的处理，相当于方法的RETURN

  - CHECK在循环中，只是跳出循环（DO,WHILE,LOOP）类似于CONTINUE,在循环外则跳出当前执行的程序块

- LEAVE : 

  - LEAVE PROGRAM.退出整个程序

  - `LEAVE TO TRANSACTION <tcode>`.跳转到另外的TCode

  - LEAVE LIST-PROCESSING.从List processor回到Dialog processor

  - LEAVE TO LIST-PROCESSING. 控制权从dialog processor 转交到list processor

  - LEAVE {SCREEN | {TO SCREEN dynnr}}

- REJECT : 用在逻辑数据库GET event blocks中，可以从循环或则一个FORM中直接跳出所在的GET事件块



未完待续......[ABAP 语法详解(SQL)](https://coldinfire.github.io/2018/ABAP4)



