---
title: "报表开发<概述>"
date: 2018-06-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---



### 报表格式

- 程序说明：包括程序名称，实现的业务功能等信息
- 数据定义
- Include内容
- 定义选择屏幕
- 执行程序业务代码
- 创建TCode：SE93

### 执行程序的使用范围，报表事件

- LOAD-OF-PROGRAM.

- INITIALIZATION. //初始化事件，用来填充选择屏幕默认值

  - AT-SELECTION SCREEN ON fiedl.  //在PAI事件结束后执行，进行校验和检查输入值
- AT SELECTION-SCREEN ON VALUE-REQUEST FOR Z_XXX. //选择屏幕字段选择功能扩展
  
- AT SELECTION-SCREEN OUTPUT.   //（PBO）显示选择屏幕之前触发
- AT SELECTION-SCREEN.   // (PAI)选择屏幕中执行某些功能后触发

  - ​	PERFORM check_input.

- START-OF-SELECTION.//Begin the main programer

  - xxxx

- END-OF-SELECTION. 

- Interactive Eventrs. (User for interactive reporting)

### 报表程序大体逻辑结构
​	**抬  头:** 报表的主要信息(抬头信息)

​	**行项目:** 查询出的每行记录信息

​	**明  细:** 每个行项目出关键字外其他明细内容

### 报表程序性能问题

- **1、报表查询维度多，不同的查询维度是完全不同的查询路径**

  ​	如果大量的查询逻辑和处理代码都是不同的，就应该设计成不同的查询报表。否则将带来极大的运维成本

- **2、报表展示维度杂乱，用户希望在一个普通 ALV 中展示，导致运算处理逻辑混乱、低效**

  ​	需求不规范，一般情况下一张普通的报表就应该只有一个维度，不同维度的应该用多个报表或者树状报表展示

- **3、业务逻辑复杂，取数过程繁琐，造成大量的性能问题**

  ​	可以根据具体需求，通过增强、自定义表等方式，在不同的业务数据间建立桥梁，简化业务逻辑，简化取数过程。











