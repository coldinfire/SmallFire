---
title: "JQuery基础"
date: 2017-12-10
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - JQuery

---

### jQuery 了解

[jQuery](https://jquery.com/download/) 是一个快速，小巧，功能丰富的 JavaScript 库。最主要的用途是用来做查询，能让我们对 HTML 文档遍历和操作 DOM、事件处理、执行动画以及 Ajax 变得更加简单；jQuery 极大地简化了 JavaScript 编程。

jQuery 的基本设计思想和主要用法，就是**"选择某个网页元素，然后对其进行某种操作"**。

jQuery 的优势：轻量级、强大的选择器、出色的 DOM 操作的封装、可靠的事件处理机制、完善的 Ajax、出色的浏览器兼容性、链式操作方式、隐式迭代、行为层与结构层的分离、不污染顶级变量、丰富的插件支持。

引入 jQuery：`<script src="../js/jquery-1.9.1.min.js"></script>`

#### [不同版本](https://www.bootcdn.cn/jquery/)的差异

- **1.x**：兼容ie6、7、8，使用最为广泛的，官方只做BUG维护，功能不再新增。因此一般项目来说，使用1.x版本就可以了，最终版本：1.12.4 (2016年5月20日)；推荐版本 1.9.1(移除了弃用接口，清理代码)
- **2.x**：不兼容ie6、7、8，**很少有人使用**，官方只做BUG维护，功能不再新增。如果不考虑兼容低版本的浏览器可以使用2.x，最终版本：2.2.4 (2016年5月20日)
- **3.x**：不兼容ie6、7、8，只支持最新的浏览器。除非特殊要求，一般不会使用3.x版本的，很多老的 jQuery 插件不支持这个版本。目前该版本是官方主要更新维护的版本。

#### 链式操作风格

链式调用是通过 return this 的形式来实现的。通过对象上的方法最后加上 return this，把对象再返回回来，对象就可以继续调用方法，实现链式操作了。节省代码量，代码看起来更优雅。

- 对于同一个对象不超过 3 个操作的，可以直接写成一行
  - `$("li").show().unbind("click");`

- 对于同一个对象的较多操作，建议每行写一个操作
- 对于多个对象的少量操作，可以每个对象写一行；如果涉及到子元素可以适当的缩进

### $ 符号

`$`是著名的 jQuery 符号。实际上，jQuery 把所有功能全部封装在一个全局变量 jQuery 中，而`$`也是一个合法的变量名，它是变量 jQuery 的别名。$() 其实和 jQuery() 是等价的，是一个函数。可以调用 `jQuery.noConflict()` 函数将变量 $ 的控制权释放。

- ```javascript
  (function(){
    window.jQuery = window.$ = jQuery;
  }());
  ```

`$()` 是一个函数，参数传递不同，效果也不一样。

- ```tex
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

网页中所有 DOM 结构绘制完毕后就可以执行。

```javascript
$(document).ready(function(){
  ... 
});
// 可以简写成:
$(function(){
  ...
});
```

### JavaScript 和 jQuery 对比

#### 定位 DOM 对象

| 类型       | ID 属性          | class 属性               | 标签名                 |
| :--------- | :--------------- | :----------------------- | :--------------------- |
| javascript | getElementById() | getElementsByClassName() | getElementsByTagName() |
| jQuery     | $("#id")         | $(".className")          | $("tagName")           |

#### DOM 对象和 jQuery 对象

DOM 文档对象模型（Document Object Model），是 W3C 组织推荐的处理可扩展标志语言的标准编程接口。 通过 DOM 对 HTML 页面的解析，可以将页面元素解析为元素节点、属性节点和文本节点，这些解析出的节点对象，即 DOM 对象。DOM 对象可以使用 JavaScript 中的方法。

jQuery 对象是通过 jQuery 包装 DOM 对象后产生的对象，是 jQuery 独有的，可以使用 jQuery 里的方法。jQuery 对象是一个数组对象，该数组中的第 0 个元素即为该 jQuery 对象对应的 DOM 对象。

在 jQuery 对象中无法使用 DOM 对象的任何方法，同样 DOM 对象页不能使用 jQuery 里的方法。

jQuery 对象和 DOM 对象的相互转换：

| 对象类型 | 变量定义  | 相互转换(jQuery ⇿ DOM)                                      |
| :------- | :-------- | :---------------------------------------------------------- |
| DOM      | variable  | var $vari = $(vari); 使用 $() 将 DOM 对象包装起来即可       |
| jQuery   | $variable | 1、var vari = $vari[index]; 2、var vari = $vari.get(index); |

