---
title: " JQuery 操作 DOM "
date: 2017-12-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - JQuery

---

## JQuery 操作DOM

### 设置和获取 html、text 和 val

#### html()

- `$(selector).html();` ：获取元素 HTML 元素
- `$(selector).html(content);` ：设置元素 HTML 元素
- `$(selector).html(function(index,element))` ：使用函数设置元素内容；index — 返回集合中元素的索引值；element — 返回被选中元素的对象

类似于 javascript 中的 innerHTML 属性，可以用来读取或设置某个元素中的 HTML 内容。设置内容时：新的内容会将原有内容覆盖，HTML标签会被解析执行。

#### text()

- `$(selector).text();` ：获取文本内容
- `$(selector).text(content);` ：设置文本内容
- `$(selector).text(function(index,element))` ：使用函数设置文本内容

类似于 javascript 中的 innerText 属性，可以用来读取或设置某个元素中的纯文本内容。设置内容时：新的内容会将原有内容覆盖，HTML标签不会被解析执行。

#### val()

- `$("#id").val();` ：获取 value 属性
- `$("#id").val(value);` ：设置 value 属性

- `$("#id").val(function(index,element));` ：使用函数设置 value 属性

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

`addClass("class1")`：向被选中元素添加一个或多个 class

`removeClass("class1 class2")`：从被选元素删除一个或多个 class，不输如指定 class 时移除所有 class

`toggleClass("class1")`：如果存在(不存在)就删除(添加) class 属性

#### CSS DOM 操作

就是读取和设置 style 对象的个各种属性。

- `css()`：设置或返回样式属性，如果属性中带有"-"符号，设置属性时使用驼峰式写法

| 功能                   | 示例                                              |
| ---------------------- | ------------------------------------------------- |
| 获取元素的样式属性     | $("#test").css("color")                           |
| 设置某个元素的单个样式 | $("#test").css("color","red")                     |
| 设置某个元素的多个样式 | $("#test").css({"color":"red","fontSize":"30px"}) |

其它方法使用：

| 方法         | 描述                                                     | 示例                                         |
| ------------ | -------------------------------------------------------- | -------------------------------------------- |
| height()     | 获取和设置元素的高度，数字默认单位：px                   | $("p").height() / $("p").height(100)         |
| width()      | 获取和设置元素的宽度，数字默认单位：px                   | $("p").width() / $("p").width(100)           |
| poistion()   | 获取元素相对于最近的一个定位样式属性的祖父节点的相对偏移 | $("p").position().left / .top                |
| scrollTop()  | 获取和设置元素的滚动条距离顶端的距离                     | $("p").scrollTop() / $("p").scrollTop(300)   |
| scrollLeft() | 获取和设置元素的滚动条距离左侧的距离                     | $("p").scrollLeft() / $("p").scrollLeft(300) |

### 节点操作

#### 插入节点

将动态创建的 HTML 元素插入到文档中。方法：将该元素成为文档某个节点的子节点。

this：代表当前选中的节点对象。

| 方法             | 描述                                                 | 示例                                 |
| :--------------- | :--------------------------------------------------- | :----------------------------------- |
| append()         | 向每个匹配的元素(父)内部追加内容(子)                 | $("div").append("html");             |
| appendTo()       | 将所有匹配的元素(子)追加到指定的元素(父)中           | $("html").appendTo("div");           |
| prepend()        | 向每个匹配的元素(父)内部前置内容(子)                 | $("div").prepend("html");            |
| prependTo()      | 将所有匹配的元素(子)前置到指定的元素(父)中           | $("html").appendTo("div");           |
| before()         | 在每个匹配的元素之前插入内容，兄弟节点               | $("div").before("html");             |
| insertBefore()   | 将所有匹配的元素插入到指定元素的前面，兄弟节点       | $("html").insertBefore("div");       |
| after()          | 在每个匹配的元素之后插入内容，兄弟节点               | $("div").after("html");              |
| insertAfter()    | 将所有匹配的元素插入到指定元素的后面，兄弟节点       | $("html").insertAfter("div");        |
| remove([option]) | 从DOM中删除匹配的元素，当前和后代节点都被移除        | $("div").remove();                   |
| empty()          | 清空元素的所有后代元素，保留当前对象以及其属性节点   | $("div").empty();                    |
| clone([true])    | 克隆匹配的DOM元素并且选中这些克隆的副本,true复制事件 | $("span").clone(true).append("div"); |

#### 遍历节点

for 循环

- for(初始化值; 循环结束条件; 步长)

$(selector).each(callback)：回调函数返回 false 则结束循环，返回 true 则继续循环

- ```javascript
  $("img").each(function(i){this.src = "test" + i + ".jpg";});
  ```

$.each(object,callback)

- ```javascript
  $.each($("img"),function(i){this.src = "test" + i + ".jpg";});
  ```

