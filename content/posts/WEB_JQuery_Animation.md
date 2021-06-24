---
title: "JQuery动画"
date: 2017-12-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - JQuery

---

## JQuery 动画

### 默认方式

| 方法                          | 描述                                            |
| :---------------------------- | :---------------------------------------------- |
| hide([speed],[easing],[fn])   | 将选择元素的 display 样式改为 none              |
| show([speed],[easing],[fn])   | 将元素的 display 样式设置为之前的显示状态(其它) |
| toggle([speed],[easing],[fn]) |                                                 |

hide() 方法在隐藏的时候会先保存原先的 display 属性值(其它除了 none 之外的值)。当调用 show() 方法时，会根据 hide() 方法保存的属性值来显示元素。

### 滑动方式

| 方法                               | 描述 |
| :--------------------------------- | :--- |
| slideDown([speed],[easing],[fn])   |      |
| slideUp([speed],[easing],[fn])     |      |
| slideToggle([speed],[easing],[fn]) |      |



### 淡入淡出方式

| 方法                              | 描述 |
| :-------------------------------- | :--- |
| fadeIn([speed],[easing],[fn])     |      |
| fadeOut([speed],[easing],[fn])    |      |
| fadeToggle([speed],[easing],[fn]) |      |

### 自定义方法

`animate(params,speed,callback)`