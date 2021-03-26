---
title: "网页 BOM 知识汇总"
date: 2017-09-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - BOM
---

### 浏览器对象模型 (BOM)

BOM 使 JavaScript 有能力与浏览器"对话"。

#### 包含对象列表

| 对象   | 描述     | 对象     | 描述         | 对象      | 描述       |
| :----- | :------- | :------- | :----------- | :-------- | :--------- |
| Window | 窗口对象 | History  | 历史纪录对象 | Navigator | 浏览器对象 |
| Screen | 屏幕对象 | Location | 地址栏对象   |           |            |

### Window 对象：浏览器窗口对象

所有 JavaScript 全局对象、函数以及变量均自动成为 window 对象的成员。

- 全局变量是 window 对象的属性。

- 全局函数是 window 对象的方法。

HTML DOM 的 document 也是 window 对象的属性之一

#### 方法列表

| Method    | Desc                                 | Method | Desc |
| :-------- | :----------------------------------- | :----- | :--- |
| alert()   | 显示带有消息和确认按钮的警告框       |        |      |
| prompt()  | 显示用户输入对话框                   |        |      |
| confirm() | 显示带有消息及确认和取消按钮的对话框 |        |      |
|           |                                      |        |      |
|           |                                      |        |      |



#### 特点

- Window 对象不需要创建可以直接使用 window.alert()
- window 引用可以省略，直接使用方法名 alert()