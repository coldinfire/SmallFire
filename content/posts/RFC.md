---
title: "RFC外部调用"
date: 2018-10-12T15:15:42+08:00
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - RFC
  - abap


---



1. 通过TCode:<SE37>,进入RFC开发初始界面

2. 创建RFC与Report不同的是，RFC程序需要先定义一个FunctionGroup，一个Group下可包含多个Function。

3. Goto > Function Groups > Create Group

4. 对Function进行属性设置

5. Functino编辑器分为七个页面：
   - Attributes：设置Short text和Processing Type,是否允许外部调用。
   - Import：数据输入接口，接口参数可以为单个变量或则为一个结构体。
   - Export：数据输出接口，接口参数可以为单个变量或一个结构体。
   - Changing：将传入的表数据修改后返回
   - Tables：可同时作为输入输出接口，参数可谓单个变量或Struct或内表
   - Exceptions：异常输入几口，参数可以为单个变量或内表
   - Source code：ABAP代码编辑器，定义RFC的具体功能。