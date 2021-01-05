---
title: "DOM 知识汇总"
date: 2017-09-19
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - DOM

---

## HTML DOM (文档对象模型)

当网页被加载时，浏览器会创建页面的文档对象模型（Document Object Model）。

![HTML DOM](/images/WEB/HTML_DOM.png)

通过可编程的对象模型，JavaScript 获得了足够的能力来创建动态的 HTML。

- JavaScript 能够改变页面中的所有 HTML 元素
- JavaScript 能够改变页面中的所有 HTML 属性
- JavaScript 能够改变页面中的所有 CSS 样式
- JavaScript 能够对页面中的所有事件做出反应

### 查找 HTML 元素

找到需要操作的 HTML 元素的方法有以下三种：

#### 通过 id 找到 HTML 元素

- var x = document.getElementById("id_name");

#### 通过标签名找到 HTML 元素

- var x = document.getElementById("id_name");
- var y = x.getElementsByTagName("span");

#### 通过类名找到 HTML 元素

- var x = document.getElementsByClassName("class_name");