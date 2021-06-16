---
title: "页面布局方式汇总"
date: 2017-09-15
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - HTML

---

### 响应式开发

#### 响应式开发原理

使用`媒体查询`针对不同宽度的设备进行布局和样式的设置，从而适配不同设备的目的。

#### 媒体查询

媒体查询是指针对不同的设备、特定的设备特征或者参数进行定制化的修改网站的样式。

方式一、通过给 `<link>` 加上 media 属性来指定该样式文件只能对什么设备生效，不指定的话默认是 all，即对所有设备都生效：

```html
<link rel="stylesheet" src="styles.css" media="screen" />
<link rel="stylesheet" src="styles.css" media="print" />
```

支持设备类型：

- all：适用于所有设备；
- print：适用于在打印预览模式下在屏幕上查看的分页材料和文档；
- screen：主要用于屏幕；
- speech：主要用于语音合成器。

方式二、还可以通过 `@media` 让 CSS 规则在特定的条件下才能生效。

- ```css
  @media (min-width: 1000px) {}
  ```

媒体查询支持逻辑操作符：

- and：查询条件都满足的时候才生效；

- not：查询条件取反；

- only：整个查询匹配的时候才生效，常用语兼容旧浏览器，使用时候必须指定媒体类型；

- 逗号或者 or：查询条件满足一项即可匹配；

- ```css
  /* 用户设备的最小高度为680px或为纵向模式的屏幕设备 */
  @media (min-height: 680px), screen and (orientation: portrait) {}
  ```

#### 响应式布局容器

响应式需要一个父级做为布局容器，来配合子级元素来实现变化效果。

原理就是在不同屏幕下，通过`媒体查询`来改变布局容器的大小，再改变里面子元素的排列方式和大小，从而实现不同屏幕下，看到不同的页面和样式变化。

```css
<head>
  <style>
    .container {
      height: 150px;
      background-color: gray;
      margin: 0 auto;
      padding: 0;
    }
    /* 超小屏幕下，小于768px，布局容器宽度显示 100% */
    @media screen and (max-width: 767px) {
      .container {
        width: 100%;
      }
    }
    /* 小屏幕下，大于等于768px，布局容器宽度显示 750px */
    @media screen and (min-width: 768px) {
      .container {
         width: 750px;
      }
    }
    /* 中等屏幕下，大于等于 992px，显示 970px */
    @media screen and (min-width: 992px) {
      .container {
        width: 970px;
      }
    }
    /* 大屏幕下，大于等于 1200px，显示 1170px */
    @media screen and (min-width: 1200px) {
      .container {
        width: 1170px;
      }
    }
  </style>
</head>
<body>
  <!-- 响应式开发里面，首先需要一个布局容器 -->
  <div class="container"></div>
</body>
```

