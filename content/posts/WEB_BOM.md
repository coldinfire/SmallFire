---
title: "网页 BOM 知识汇总"
date: 2017-09-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - JS

---

### 浏览器对象模型 (BOM)

BOM 使 JavaScript 有能力与浏览器"对话"。

#### 包含对象列表

| 对象   | 描述     | 对象     | 描述         | 对象      | 描述       |
| :----- | :------- | :------- | :----------- | :-------- | :--------- |
| Window | 窗口对象 | History  | 历史纪录对象 | Navigator | 浏览器对象 |
| Screen | 屏幕对象 | Location | 地址栏对象   |           |            |

### Window 浏览器窗口对象

所有 JavaScript 全局对象、函数以及变量均自动成为 window 对象的成员。

- 全局变量是 window 对象的属性。

- 全局函数是 window 对象的方法。

#### 属性列表

获取其他BOM对象：history、location、scrren、navigator

获取DOM对象：document，HTML DOM 的 document 也是 window 对象的属性之一

#### 方法列表

| Method                          | Desc                                              |
| :------------------------------ | :------------------------------------------------ |
| alert()                         | 显示带有消息和确认按钮的警告框                    |
| confirm()                       | 显示带有消息及确认和取消按钮的对话框              |
| prompt()                        | 显示用户输入对话框                                |
| close()                         | 关闭浏览器窗口，关闭调该方法的窗口                |
| open("URL")                     | 打开新的浏览器窗口，返回新的Window对象            |
| setInterval(fun_name,2000)      | 按照指定的周期(以毫秒计算)来调用函数或计算表达式  |
| clearInterval(intervalVariable) | 取消由 setInterval 方法设置的 timeout             |
| setTimeout(fun_name,3000)       | 在指定的毫秒数后调用函数或计算表达式;返回唯一标识 |
| clearTimeout(timeoutlVariable)  | 取消由 setTimeout 方法设置的 timeout              |

#### 特点

- Window 对象不需要创建可以直接使用 window.alert()
- window 引用可以省略，直接使用方法名 alert()

### Screen 对象

包含了用户显示器屏幕相关信息。通过该对象，可以访问用户显示器屏幕宽、高、色深等信息。

#### 属性列表

| Attribute  | Desc       | Attribute   | Desc         |
| ---------- | ---------- | ----------- | ------------ |
| width      | 屏幕总宽度 | availWidth  | 屏幕可用宽度 |
| height     | 屏幕总高度 | availHeight | 屏幕可用高度 |
| colorDepth | 色彩深度   | pixelDepth  | 色彩分辨率   |

### Location 地址栏对象

Location 对象包含有关当前 URL 的信息，是对当前窗口URL地址的解析。该对象提供了可以访问URL中不同部分的信息属性。用于获得当前页面的地址 (URL)，并把浏览器重定向到新的页面。

#### 属性列表

| Attribute | Desc                     | Attribute | Desc                         |
| :-------- | :----------------------- | :-------- | :--------------------------- |
| href      | 设置或返回页面完整的 URL | pathnmae  | 返回档期那页面的路径和文件名 |
| hostname  | 返回 web 主机的域名      | protocol  | 返回所使用的 web 协议        |
| port      | 返回 web 主机的端口      |           |                              |

#### 方法列表

| Method       | Desc                                                         |
| :----------- | :----------------------------------------------------------- |
| assign(url)  | 加载 URL 指定的新的 HTML 文档。就相当于一个链接，跳转到指定的url。 |
| reload()     | 重新加载当前文档                                             |
| replace(url) | 加载 URL 指定的文档来替换当前文档，前后两个页面共用一个窗口  |

### History 历史纪录对象

保存浏览器历史记录信息，也就是用户访问的页面。浏览器的前进与后退功能本质上就是 history 的操作。history 对象记录了用户浏览过的页面，通过该对象提供的API可以实现与浏览器前进/后退类似的导航功能。

#### 属性列表

length：返回当前窗口历史列表中的 URL 数量

#### 方法列表

| Method    | Desc                                                         |
| :-------- | :----------------------------------------------------------- |
| back()    | 加载 history 列表中的前一个 URL，与在浏览器点击后退按钮相同  |
| forward() | 加载 history 列表中的下一个 URL，与在浏览器点击向前按钮相同  |
| go(index) | 加载 history 列表中的某个具体页面 ，里面的参数表示跳转页面的个数，负数后退、整数前进 |

