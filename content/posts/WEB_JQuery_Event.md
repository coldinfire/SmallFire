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

#### 标准绑定方式

`$(selector).bind(even[,data],fn);` 简写模式为：`$(selector).even(fn)`

事件类型：类型包括 blur、focus、load、resize、scroll、unload、click、dblclick、mousedown/up/over/out/enter/leave、change、select、submit、keydown/up、keypress、error

可选参数：作为 event.data 属性值传递给事件对象的额外数据对象

处理函数：事件触发后的具体处理内容

#### on / off 使用

`.on( events [, selector ] [, data ], handler(eventObject) )` ：在选定的元素上绑定一个或多个事件处理函数

`.off( events [, selector ] [, handler(eventObject) ] )`：移除一个或多个事件处理函数。不传递任何参数，则将组件上的所有事件全部解绑。

on 属于绑定事件处理器(event-handler-attachment)，可以绑定 dom 和 bom  的既有事件，也可以绑定自定义事件。推荐使用该方式弹性的绑定更多的事件。

#### 事件切换

toggle()：用于模拟鼠标连续单击事件，实现事件切换。

- toggle(fn1,fn2,...fnN);

### 事件冒泡



event.stopPropagation()：阻止事件冒泡行为



event.preventDefault()：阻止浏览器默认行为