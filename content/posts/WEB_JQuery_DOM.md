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

类似于 javascript 中的 innerHTML 属性，可以用来读取或设置某个元素中的 HTML 内容。

#### text()

- `$("#id").text();`

类似于 javascript 中的 innerText 属性，可以用来读取或设置某个元素中的文本内容。

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

对 class 属性操作。

addClass()：添加 class 属性值

removeClass()：删除 class 属性值

toggleClass()：如果存在(不存在)就删除(添加) class 属性



### 节点操作

#### 创建节点

