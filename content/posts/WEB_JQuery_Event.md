---
title: "JQuery事件"
date: 2017-12-10
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - JQuery

---



## 事件处理

### 事件绑定

`bind( type [, data] , fn );`

事件类型：类型包括 blur、focus、load、resize、scroll、unload、click、dblclick、mousedown/up/over/out/enter/leave、change、select、submit、keydown/up、keypress、error

可选参数：作为 event.data 属性值传递给事件对象的额外数据对象

处理函数：事件触发后的具体处理内容

toggle()：用于模拟鼠标连续单击事件。

- toggle(fn1,fn2,...fnN);

### 事件冒泡

