---
title: "Smartforms"
date: 2018-07-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---



### 查找Smartforms

- TCode：NACE可以查找 (例如。采购订单，销售订单等)
  - NACE 是用于链接应用程序类型，输出类型及其处理例程（如驱动程序和附加的脚本表单或 Smartforms）的 Tcode。
- Table：TNAPR根据smartform名字查找对应的smartform程序

### 创建

- TCode:smartforms

### Smartforms组成

​	Smartforms 包括页格式 + FORM 内容，一般 form 里包含表头,表身,表尾;可以单页，也可以有多页，

调用方式是通过函数‘XXX_SSF’ 将 form 生产一个函数，然后调用函数
打印 form 。

- 记录总页数：sfsy-page(当前页数) / sfsy-formpages(总页数)

### Smartforms使用

​	在 SAP 的 ABAP 编程中，一般开发过程都是在 Report 程序中取出所有需要的数据，将数据进行相应的处理以后保存到输出内表中，再打印内表中的数据。但是 SmartForms 是一个独立的外部 Function Module，对于程序内部定义的内表数据不能直接传递，需要定义外部的数据结构 Structure 或者使用标准的表结构，如果程序变更，需要传递的数据发生变化，那么该 Sturcture 也需要修改，这是 SmartForms 中不方便的地方。

​	我们也可以在 SmartForms 内部写取数据的逻辑，但是在 SmartForms 中编程不是很方便，而且有时我们的数据需要首先以 List 或者 ALV List 的方式显示，然后再打印，所以在 smartforms 中书写取数据逻辑只能对一些要求非常简单的场合适用。

​	Smartforms的执行顺序是根据右边菜单从上到下执行的。


