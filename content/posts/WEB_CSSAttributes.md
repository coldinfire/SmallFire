---
title: "CSS属性"
date: 2017-09-13
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - CSS

---

## 属性介绍
网页的背景设计：

### background-color ###
颜色设置方式

 - Color name：red，green...
 - HEX Value：#rrggbb
 - RGB Value：rgb(255,255,0)

div {  background-color: lightblue; }
### background-image ###
body {
  background-image: url("demo.gif");
}
### background-repeat ###
默认图片是水平和垂直重复铺满整个屏幕。

- repeat-x：水平重复
- repeat-y：垂直重复
- no-repeat：不重复

### background-attachment ###
设置背景图片跟随屏幕滚动，还是固定在特定的位置。

 - fixed
 - scroll

### background-position ###
图片定位：left, right, top,bottom

**可以将上述属性统一设置，不必单独设置，但是需要按照一定的顺序设置。**

 - background-color
 - background-image
 - background-repeat
 - background-attachment
 - background-position
