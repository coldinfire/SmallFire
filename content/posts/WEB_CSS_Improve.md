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

通过 border 设置实现三角形：BOX 的宽和高设置为 0，border的边框设置为透明色，指定对应边的border大小和颜色即可实现对应的三角形显示。

```css
.box {
    width: 0;
    height: 0;
    border: 10px solid transparent;
    border-bottom-color: pink;
}
```



### 实现行内块和文字垂直居中对齐

图片、表单都属于行内块元素，默认的 vertical-align 是基线对齐。

- { vertical-align: baseline; } ：基线对齐
- { vertical-align: bottom; } ：底部对齐
- { vertical-align: middle; } ：垂直居中对齐
- { vertical-align: top; } ：顶部对齐