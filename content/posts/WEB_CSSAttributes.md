---
title: "CSS属性"
date: 2017-09-09
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - CSS

---

## CSS 属性介绍
### 文字文本属性

#### 文字属性

- 文字颜色：color: red;
  - 十六进制：#FF0000 | #F00
  - RGB值：rgb(255,0,0)
  - 颜色名字：red
- 文字字体：font-family: "微软雅黑",Times;
- 文字大小：font-size: 16px;   (16px = 1em)
- 文字粗细：font-weight: [bold、normal];
- 字体样式：font-style: [normal、italic、oblique];
- 小写字母以大写字母显示：font-variant: [normal、small-caps];

#### 文本属性

- 文本对齐
  - text-align: [center、right、left];
- 行间距
  - line-height: [5px、1.5em];
- 缩进
  - text-indent: [5px、2em];
- 字、字母间距：letter-spaceing: 5px;
- 单词间距：word-spaceing: 5px;
- 文本线
  - text-decoration: [none、overline、line-through、underline];
- 文本转换
  - text-transform: [uppercase、lowercase、capitalize];

### 盒模型

盒模型的组成

- 自身内容：width、height
- 边框：border
  - border: 2px solid #ff0000;
  - border-width: 2px;
  - border-style: dotted;    (solid:实线，dashed:虚线，dotted:点状虚线，double:双实线)
  - border-color: red;
  - border-radius: 10px;  圆角
- 内边距(上、右、下、左)：padding
  - padding: 5px 5px 5px 5px;
  - padding-top: 5px;
  - padding-right: 5px;
  - padding-bottom: 5px;
  - padding-left: 5px;
- 外边距：margin
  - 属性设置和外边距设置遵循同样的规则，属性值也相同
- 阴影属性：box-shadow
  - box-shadow: 1px 1px 5px #000;
- 轮廓：outline
  - outline 是不占空间的，既不会增加额外的 width 或者 height
  - outline的声明内容和border一致

外边距合并：两个盒子同时设置了外边距，会进行一个外边距合并

### 背景属性

#### background-color

背景颜色的添加

- background: red;
- background-color: red;

#### background-image ####

背景图片的添加

- background: url("demo.gif");
- background-image: url("demo.gif");

#### background-repeat ####
默认图片是水平和垂直重复铺满整个屏幕。

- 水平重复：background-repeat: repeat-x;
- 垂直重复：background-repeat: repeat-y;
- 不重复：background-repeat: no-repeat;

#### background-attachment ####
设置背景图片跟随屏幕滚动，还是固定在特定的位置。

 - 固定，不随内容的滚动而滚动：background-attachment: fixed;
 - 滚动，随内容的滚动而滚动：background-attachment: scroll;

#### background-position ####

景图片的定位就是可以设置显示背景图片的位置

- 英文单词：left、right、top、bottom
- 数值：background-position: x y;
- 百分比：background-position: x% y%;

#### background-size

背景图片的大小设置

- background-size: x y;
- background-size: x% y%;

#### 背景属性简写

`body {background:#ffffff url('img.png') no-repeat fixed right top;}`

可以将上述属性统一设置，不必单独设置，属性值的顺序为：

 - background-color
 - background-image
 - background-repeat
 - background-attachment
 - background-position

### 链接样式

#### 链接状态

- `a:link` ： 正常，未访问过的链接
- `a:visited` ： 用户已访问过的链接
- `a:hover` ： 当用户鼠标放在链接上时
- `a:active` ： 链接被点击的那一刻

设置为若干链路状态的样式时的顺序规则：

- a:hover 必须跟在 a:link 和 a:visited 后面
- a:active 必须跟在 a:hover 后面

### 显示与可见性

对于 CSS 里的 visibility 属性，通常其值被设置成 `visible` 或 `hidden`。

**visibility: hidden** 相当于 **display:none**，能把元素隐藏起来，但两者的区别在于：

-  1、**display:none** 元素不再占用空间。
-  2、**visibility: hidden** 使元素在网页上不可见，但仍占用空间。

visibility 还可能取值为 collapse 。当设置元素 **visibility: collapse** 后，一般的元素的表现与 **visibility: hidden** 一样，也即其会占用空间。但如果该元素是与 table 相关的元素，例如 table row、table column、table column group 等，其表现却跟 **display: none** 一样，也即其占用的空间会释放。

### 定位：Position

position 属性的五个值：

- static ：默认值，没有定位，遵循正常的文档流对象
- relative ：相对定位元素的定位是相对其正常位置；移动相对定位元素，但它原本所占的空间不会改变
- fixed 
  - 元素的位置相对于浏览器窗口是固定位置；即使窗口是滚动的它也不会移动
  - Fixed 定位的元素和其他元素重叠
- absolute ：绝对定位的元素的位置相对于最近的已定位父元素，如果元素没有已定位的父元素，那么它的位置相对于 html
- sticky ：

### 浮动：Float

CSS 的 Float（浮动），会使元素向左或向右移动，其周围的元素也会重新排列。

- img { float: right; }
- img { float: left; }

清除浮动

元素浮动之后，周围的元素会重新排列，为了避免这种情况，使用 clear 属性。

clear 属性指定元素两侧不能出现浮动元素。

- text { clear: both; }