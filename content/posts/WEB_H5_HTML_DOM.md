---
title: " DOM 知识汇总 "
date: 2017-09-25
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - HTML

---

### DOM 标准分类

核心 DOM：针对任何结构化文档的标准模型

- Document：文档对象
- Element：元素对象
- Attribute：属性对象
- Text：文本对象
- Comment：注释对象
- Node：节点对象，上面五个的父对象

XML DOM ：针对 XML 文档的标准模型

HTML DOM：针对 HTML 文档的标准模型

### HTML DOM (文档对象模型)

当网页被加载时，浏览器会创建页面的文档对象模型（Document Object Model）。

通过可编程的对象模型，JavaScript 获得了足够的能力来创建动态的 HTML，可以控制HTML文档的内容。

- JavaScript 能够改变页面中的所有 HTML 元素
- JavaScript 能够改变页面中的所有 HTML 属性
- JavaScript 能够改变页面中的所有 CSS 样式
- JavaScript 能够对页面中的所有事件做出反应

### DOM 节点

根据 W3C 的 HTML DOM 标准，HTML 文档中的所有内容都是节点

- 整个文档是一个文档节点：document.documentElement
- HTML 是根节点
- 每个 HTML 元素是元素节点
- HTML 元素内的文本是文本节点
- 每个 HTML 属性是属性节点
- 注释是注释节点

![HTML DOM](/images/WEB/HTML_DOM.png)

#### nodeName

nodeName 属性规定节点的名称。

- nodeName 是只读的
- 元素节点的 nodeName 与标签名相同
- 属性节点的 nodeName 与属性名相同
- 文本节点的 nodeName 始终是 #text
- 文档节点的 nodeName 始终是 #document

**注意：** nodeName 始终包含 HTML 元素的大写字母标签名。

#### nodeValue 属性

nodeValue 属性规定节点的值。

- 元素节点的 nodeValue 是 undefined 或 null
- 文本节点的 nodeValue 是文本本身
- 属性节点的 nodeValue 是属性值

### 操作 HTML 元素和属性

HTML DOM 定义了所有 HTML 元素的对象和属性，以及访问它们的方法。

- **方法** 是能够执行的动作（比如添加或修改元素）。

- **属性** 是能够获取或设置的值（比如节点的名称或内容）。

#### 查找 HTML 元素

找到需要操作的 HTML 元素的方法有以下三种：

通过 id 找到 HTML 元素

- var x = document.getElementById("id_name");

通过标签名找到 HTML 元素 : 返回带有指定标签名的所有元素

- var y = document.getElementsByTagName("span");

通过类名找到 HTML 元素:返回包含指定 class 的所有元素的一个列表

- var x = document.getElementsByClassName("class_name");

#### 其它操作方法

| Method             | Desc                 | Method                 | Desc                     |
| :----------------- | :------------------- | :--------------------- | :----------------------- |
| appendChild()      | 添加新节点到指定节点 | insertBefore()         | 在指定子节点前插入新节点 |
| removeChild()      | 删除子节点           | createAtttibute()      | 创建属性节点             |
| replaceChild()     | 替换子节点           | setAttribute(attr,val) | 修改指定属性设置值       |
| createElement()    | 创建元素节点         | removeAttribute(attr)  | 删除属性                 |
| createTextNote()   | 创建本本节点         | getAttribute()         | 获取指定的属性值         |
| insertRow(line_no) | Table 插入一行       | insertCell(cell_no)    | 行中插入一个元素，0 开始 |

#### 创建元素

创建了一个新的元素：var para=document.createElement("p");

如需向元素添加文本，首先必须创建文本节点:var node=document.createTextNode("这是一个新段落。");

然后必须向元素追加文本节点：para.appendChild(node);

最后，必须向已有元素追加这个新元素：var element=document.getElementById("div1");

向这个已存在的元素追加新元素：element.appendChild(para);

#### HTML DOM 属性

属性是节点（HTML 元素）的值，能够获取或设置属性值。

| Attribute  | Desc                   | Attribute  | Desc               |
| :--------- | :--------------------- | :--------- | :----------------- |
| innerHTML  | 获取节点(元素)的文本值 | firstChild | 首个子元素         |
| attributes | 节点(元素)的属性节点   | lastChild  | 最后一个子元素     |
| childNodes | 节点(元素)的子节点     | parentNode | 节点(元素)的父节点 |

