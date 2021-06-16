---
title: "JQuery基础"
date: 2017-10-29
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - JQuery

---

## jQuery 知识总结

[jQuery](https://jquery.com/download/) 是一个快速，小巧，功能丰富的 JavaScript 库。最主要的用途是用来做查询，能让我们对 HTML 文档遍历和操作 DOM、事件处理、执行动画以及 Ajax 变得更加简单；jQuery 极大地简化了 JavaScript 编程。

jQuery 的基本设计思想和主要用法，就是**"选择某个网页元素，然后对其进行某种操作"**。

jQuery 的优势：轻量级、强大的选择器、出色的 DOM 操作的封装、可靠的事件处理机制、完善的 Ajax、出色的浏览器兼容性、链式操作方式、隐式迭代、行为层与结构层的分离、不污染顶级变量、丰富的插件支持。

#### [不同版本](https://www.bootcdn.cn/jquery/)的差异

- **1.x**：兼容ie6、7、8，使用最为广泛的，官方只做BUG维护，功能不再新增。因此一般项目来说，使用1.x版本就可以了，最终版本：1.12.4 (2016年5月20日)；推荐版本 1.9.1(移除了弃用接口，清理代码)
- **2.x**：不兼容ie6、7、8，**很少有人使用**，官方只做BUG维护，功能不再新增。如果不考虑兼容低版本的浏览器可以使用2.x，最终版本：2.2.4 (2016年5月20日)
- **3.x**：不兼容ie6、7、8，只支持最新的浏览器。除非特殊要求，一般不会使用3.x版本的，很多老的 jQuery 插件不支持这个版本。目前该版本是官方主要更新维护的版本。

### $符号

`$`是著名的 jQuery 符号。实际上，jQuery 把所有功能全部封装在一个全局变量jQuery中，而`$`也是一个合法的变量名，它是变量jQuery的别名。$ 其实和 jQuery 是等价的，是一个函数。

```javascript
(function(){
  window.jQuery = window.$ = jQuery;
}());
```

`$` 是一个函数，参数传递不同，效果也不一样。

```tex
// CSS选择器
$(document) //选择整个文档对象
$('#myId')  //选择ID为myId的网页元素
$('div.myClass') // 选择class为myClass的div元素
$('input[name=first]') // 选择name属性等于first的input元素
// jQuery特有表达式
$('a:first') //选择网页中第一个a元素
$('tr:odd')  //选择表格的奇数行
$('#myForm :input') // 选择表单中的input元素
$('div:visible') //选择可见的div元素
$('div:gt(2)') // 选择所有的div元素，除了前三个
$('div:animated') // 选择当前处于动画状态的div元素
```

#### 入口函数

```javascript
$(function(){
  ...
});
```

