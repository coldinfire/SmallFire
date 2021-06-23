---
title: "JQuery操作DOM"
date: 2017-12-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - JQuery

---

## JQuery 操作DOM

### 设置和获取 HTML、TEXT 和 val

#### html()

- `$("#id").html();`

类似于 javascript 中的 innerHTML 属性，可以用来读取或设置某个元素中的 HTML 内容。设置内容时：新的内容会将原有内容覆盖，HTML标签会被解析执行。

#### text()

- `$("#id").text();`

类似于 javascript 中的 innerText 属性，可以用来读取或设置某个元素中的纯文本内容。设置内容时：新的内容会将原有内容覆盖，HTML标签不会被解析执行。

#### val()

- `$("#id").val();`

类似于 javascript 中的 value 属性，可以用来设置和获取元素的值。文本框、下拉列表、单选框等都可以返回元素的值。如果元素为多选，则返回包含所有选择的值的数组。

### 属性操作

使用 `attr()` / `prop()` 方法来获取和设置元素属性，`removeAttr()` /`removeProp()` 方法来删除元素属性。

attr 和 prop 的区别：

- 如果操作的是元素的固有属性，建议使用 prop

- 如果操作的是元素自定义的属性，建议使用 attr

  | 功能           | 示例                                                         |
  | -------------- | ------------------------------------------------------------ |
  | 获取属性       | var txt = $("p").attr("attr_name");                          |
  | 设置单个属性   | $("p").attr("attr_name","new value");                        |
  | 设置多个属性值 | $("p").attr("attr1_name":"new value","attr2_name":"new value"); |
  | 删除属性       | $("p").removeAttr("attr_name");                              |

### 样式操作

`addClass("class_name")`：向被选中元素添加一个或多个 class

`removeClass("class_name")`：从被选元素删除一个或多个 class

`toggleClass("class_name")`：如果存在(不存在)就删除(添加) class 属性

`css("Attr","Value")`：设置或返回样式属性

### 节点操作

#### 插入节点

将动态创建的 HTML 元素插入到文档中。方法：将该元素成为文档某个节点的子节点。

| 方法           | 描述                                 | 示例                           |
| -------------- | ------------------------------------ | ------------------------------ |
| append()       | 向每个匹配的元素内部追加内容         | $("div").append("html");       |
| appendTo()     | 将所有匹配的元素追加到指定的元素中   | $("html").appendTo("div");     |
| prepend()      | 向每个匹配的元素内部前置内容         | $("div").prepend("html");      |
| prependTo()    | 将所有匹配的元素前置到指定的元素中   | $("html").appendTo("div");     |
| before()       | 在每个匹配的元素之前插入内容         | $("div").before("html");       |
| insertBefore() | 将所有匹配的元素插入到指定元素的前面 | $("html").insertBefore("div"); |
| after()        | 在每个匹配的元素之后插入内容         | $("div").after("html");        |
| insertAfter()  | 将所有匹配的元素插入到指定元素的后面 | $("html").insertAfter("div");  |

