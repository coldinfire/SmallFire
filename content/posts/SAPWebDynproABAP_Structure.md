---
title: " Web Dynpro ABAP - Structure "
date: 2018-10-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro
---



### 一般的程序框架

![Web Dynpro Role](/images/webdynproABAP/Portal24.png)

#### Component Controller

组件控制器是定义全局的组建，与视图相似，组件控制器是一个程序对外的部分，是整个程序最开始执行的环节 ，也是控制多个视图间数据交互的纽带，一般考虑到程序的扩展性会优先使用组件控制器，然后关联各视图。

#### Component Interface

组件接口是用来引入一些外部组件接口的。引入的组件接口可添加到相应的视图窗口中使用

#### Views

视图是一个DYNPRO程序显示的部分，可有多个视图，视图间可进行跳转，每个视图中需要显示的字段结构表等信息需要单独定义在该视图的节点中（CONTEXT）注意：组件控制器中也可以添加节点，作为全局节点属性，如果将它与某视图中的节点进行MAPPING，则可以在视图结束后，程序没结束的时候保存节点属性。一般界面跳转进行如此操作。

#### Windows

窗口与视图相似，只是每个程序每次显示只能有一个单独的窗口，可定义多个窗口，窗口间跳转，与视图跳转相似，都是在Inbound Plugs（入站）和Outbound Plugs（出站）里做对应的绑定。

#### Application

应用程序，单独的执行程序。