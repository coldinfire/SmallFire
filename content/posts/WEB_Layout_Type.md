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

