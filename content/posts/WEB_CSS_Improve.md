---
title: "CSS高级功能实现"
date: 2017-09-07
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - CSS

---

### 三角形实现

#### 普通三角形

通过 border 设置实现三角形：BOX 的宽和高设置为 0，border的边框设置为透明色，指定对应边的border大小和颜色即可实现对应的三角形显示。

```css
.box {
    width: 0;
    height: 0;
    border: 10px solid transparent;
    border-bottom-color: pink;
}
```

#### 高级三角形

可以通过设置边框的上边框或下边框为 0 实现不同的大三角，同时设置其他边框为透明色，可只保留需要的形状。

```css
.box {
    width: 0;
    height: 0;
    border-color: transparent pink transparent transparent;
    border-style: solid;
    border-width: 20px 10px 0 0;
}
```

### 实现行内块和文字垂直居中对齐

图片、表单都属于行内块元素，默认的 vertical-align 是基线对齐。

- { vertical-align: baseline; } ：基线对齐
- { vertical-align: bottom; } ：底部对齐
- { vertical-align: middle; } ：垂直居中对齐
- { vertical-align: top; } ：顶部对齐

### 单行文字溢出显示省略号

Step1：如果文字显示不完，也必须强制一行内显示，`white-space: nowrap; `

Step2：溢出部分隐藏，`overflow: hidden;`

Step3：文字溢出时，用省略号显示，`text-overflow: ellipsis;`

### 多行文本溢出显示省略号

有兼容性问题，适合于webKit浏览器或者移动端。

```css
overflow: hidden;
text-overflow: ellipsis;
/* 弹性伸缩盒子模型显示 */
display: -webkit-box;
/* 限制一个块元素显示的文本的行数 */
-webkit-line-clamp: 2;
/* 设置或检索伸缩盒子对象的子元素排列方式 */
-webkit-box-orient: vertical;
```

