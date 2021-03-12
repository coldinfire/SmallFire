---
title: "Bootstrap学习与总结"
date: 2017-09-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - JS

---



### Bootstrap 介绍

[Bootstrap](https://bootstrap.css88.com/)，来自 Twitter，前端开发框架。Bootstrap 是基于 HTML、CSS、JavaScript 的，它简洁灵活，使得 Web 开发更加快捷。

框架：有一套比较完整的网页功能解决方案，而且控制权在框架本身，框架包含有预制样式库、组件和插件。使用者要按照框架所规定的某种规范进行开发。

Bootstrap 优点

- 标准化的 html + css 编码规范
- 提供了一套简洁、直观、强悍的组件
- 有自己的生态圈，不断的更新迭代
- 让开发更简单，提高开发效率

Bootstrap 版本

- 2.x.x：停止维护，兼容性好，代码不够简洁，功能不够完善
- 3.x.x：目前使用最多，稳定，放弃了 IE6-IE7。偏向用于开发响应式布局。
- 4.x.x：最新版，使用相比 3.x.x 较少

### Bootstrap 使用

#### Step1:创建文件夹结构

![Bootstrap](/images/WEB/Bootstrap_01.png)

bootstrap 文件夹中的内容是官方提供的下载包内的文件内容。

#### Step2:创建 html 骨架，引入相关样式文件

```html
<!-- 要求当前网页使用IE浏览器最高版本内核来渲染 -->
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<!-- 视口的设置：视口的宽度和设备一直，默认的缩放比例和PC端一致，用户不能自行缩放 -->
<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=0">
<!-- 引入Bootstrap -->
<link href="bootstrap/css/bootstrap.min.css" rel="stylesheet">
<!--[if lt IE 9]>
  <!-- 解决IE9以下浏览器对HTML5新增标签的不识别，并导致CSS不起作用的问题 -->  
  <script src="https://oss.maxcdn.com/html5shiv/3.7.2/html5shiv.min.js"></script>
  <!-- 解决IE9以下浏览器对CSS3 Media Query的不识别 -->  
  <script src="https://oss.maxcdn.com/respond/1.4.2/respond.min.js"></script>
<![endif]-->
```

#### Step3:写入内容

```html
<body>
<!-- Split button -->
    <div class="btn-group">
        <button type="button" class="btn btn-danger">Action</button>
        <button type="button" class="btn btn-danger dropdown-toggle" data-toggle="dropdown" 
                aria-expanded="false">
            <span class="caret"></span>
            <span class="sr-only">Toggle Dropdown</span>
        </button>
        <ul class="dropdown-menu" role="menu">
            <li><a href="#">Action</a></li>
            <li><a href="#">Another action</a></li>
            <li><a href="#">Something else here</a></li>
            <li class="divider"></li>
            <li><a href="#">Separated link</a></li>
        </ul>
    </div>
</body>
```

### 布局容器

Bootstrap 需要为页面内容和栅格系统包裹一个 .container 容器，Bootstrap 预先定义好了该类。

| container 类                   | container-fluid 类           |
| :----------------------------- | :--------------------------- |
| 响应式布局的内容，`固定宽度`   | 流式布局容器，`全屏宽度`     |
| 大屏(>=1200px) 宽度定为 1170px | 占据全部视口(viewport)的容器 |
| 中屏(>=992px) 宽度定为 970px   | 适合于单独做移动端开发       |
| 小屏(>=768px) 宽度定为 750px   |                              |
| 超小屏(100%)                   |                              |

### 栅格系统

栅格系统：指将页面布局划分为等宽的列，然后通过列数的定义来模块化页面布局。

Bootstrap 提供了一套响应式、移动设备优先的流式栅格系统，随着屏幕或 viewpoint 尺寸的增加，系统会自动分为最多12列。

Bootstrap 里面定义的 container 宽度是固定的，但是不同屏幕下，container 的宽度不同，将 container 划分为12等份。

#### 栅格选项参数

栅格系统用于通过一系列的行（row）与列（column）创建页面布局，内容就可以放入创建好的布局中。

|                     | 超小屏幕 < 768px | 小平设备 >=768px | 中等屏幕>=992px | 宽屏设备>=1200px |
| ------------------- | ---------------- | ---------------- | --------------- | ---------------- |
| .container 最大宽度 | 自动(100%)       | 750px            | 970px           | 1170px           |
| 类前缀              | .col-xs-x        | .col-sm-x        | .col-md-x       | .col-lg-x        |
| 列数(column)        | 12               | 12               | 12              | 12               |

- 行（row）必须包含在 container 布局容器中，以便自身调整或填充。
- 用行（row）来创建一组水平的列（column）。
- 内容应放在列（column）中， 只有列（column）可以是行（row）的直接子元素。
- 预定义的栅格类，像 `.row` and `.col-xs-4`可用于快速创建栅格布局。
- 实现列的平均等分，需要给列添加对应的类前缀：`.col-xx-x`。
- 栅格系统中的列是通过指定1到12的值来表示其跨越的范围。
- 如果一行中超过12列， 各组额外列，将作为一个单元， 换到新的一行。
- 每一列默认有左右 15px 的 padding 值。
- 栅格类适用于与屏幕宽度大于或等于分界点大小的设备 ， 并且针对小型设备覆盖栅格类。 因此，在元素上应用任何 `.col-md-`类不仅会影响媒体设备，同时如果 `.col-lg-`不存在， 也影响大型设备。
- 列通过设置 `padding`来创建列之间的间隔。然后通过在 `.row`上设置负值的 `margin`来抵消为第一和最后一列的 `padding`。
- 负值的 `margin`就是下面的示例为什么是向外突出的原因。在栅格列中的内容排成一行。

#### 列嵌套

一个列内再分成若干列。可以通过添加一个新的 .row 元素和一系列 .col-md-x 元素到已存在的列中。

列嵌套加一个行(row)，这样可以取消父元素的 padding 值，而且高度自动和父级一样高。

#### 列偏移

使用 `.col-md-offset-x` 类可以将列向右侧偏移。这些实际类是通过 * 选择器为当前元素增加了左边的边距 (margin-left)。

```html
<div class="row">
    <div class="col-lg-3">left site</div>
    <div class="col-lg-3 col-lg-offset-6">right site</div>
</div>
```

#### 列排序

通过使用 `col-md-push-x` 和 `.col-md-pull-x` 类就可以很容易的改变列(column)的顺序。

```html
<div class="row">
    <div class="col-lg-3 col-lg-push-9">left site</div>
    <div class="col-lg-9 col-lg-pull-3">right site</div>
</div>
```

### 响应式工具

为了加快对移动设备友好的页面开发工作，利用媒体查询功能，并使用这些工具类可以方便的针对不同的设备展示或隐藏页面内容。

| 类名       | 超小屏 | 小屏 | 中屏 | 大屏 |
| :--------- | ------ | ---- | ---- | ---- |
| .hidden-xs | 隐藏   | 可见 | 可见 | 可见 |
| .hidden-sm | 可见   | 隐藏 | 可见 | 可见 |
| .hidden-md | 可见   | 可见 | 隐藏 | 可见 |
| .hidden-lg | 可见   | 可见 | 可见 | 隐藏 |

与隐藏相反的是 `visible-xs`，`visible-sm`，`visible-md`，`visible-lg`显示某个页面内容。