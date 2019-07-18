---
title: "CSS格式与标准总结"
date: 2017-09-12T17:20:58+08:00
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - CSS

---

## CSS语法



- 使用双空格短缩进——只有这样才能保证代码能在所有渲染器中显示一致；
- 当成组使用选择器时，每个选择器单独一行；
- 一条声明的开大括号前留一空格；
- 声明的关闭右括号请新起一行；
- 每条引用声明冒号后留一个空格；
- 每条声明有单独的一行，以便错误报告更精确；
- 每条声明以分号结尾，最后一个可以没有，但这样容易出错；
- 以逗号分隔属性值之间，要在逗号后面保留一个空格（例如： `box-shadow`）；
- 在`rgb()`、`rgba()`、`hsl()`、`hsla()`以及`rect()`属性值*中*逗号后不要加空格，这样能把多色彩值（逗号无空格）与多属性值的情况区分开来（逗号带空格）；
- 省略掉属性值以及颜色参数的首位0（例如：用 `.5` 代替 `0.5` ，用 `-.5px` 取代 `-0.5px`）；
- 十六进制值要用小写，像 `#fff`这样，当一个文档中有很多的字母时，小写字母更容易分辨；
- 如果可以的话，简化十六进制值的写法，如用 `#fff` 代替 `#ffffff` ；
- 在选择器中引用属性值，例如：`input[type="text"]`，在某些情况下可选是否使用，这样做也能更好地统一代码；
- 零值不需要添加单位，例如：用 `margin: 0;` 代替 `margin: 0px;`；

### 声明顺序

有关联的属性声明之间应该按照以下顺序组在一起：

1. 定位

2. 盒模型

3. 排字

4. 视觉设计

5.  其他杂项

   ​	定位摆在第一位，因为它能把元素从文档正常流中脱离出来，覆盖盒模型样式。而盒模型表示着组件的度量尺寸和摆放位置，因此排第二。排版和视觉都是属于元素内部的属性，不会影响到前两项，所以排为3、4。

### 选择器

- 为了更好的显示性能，请使用类名而非一般的元素标签；
- 对于经常使用的组件，不要使用多个属性选择器，这样调用对浏览器性能有不小影响；
- 尽可能保持简短，如果可以请限制在3个元素以内；
- **只有**在不得已的情况下再回溯最近的父级元素（例如：类名无前缀时）；

元素选择器：`p{ color: red; }`

id选择器：`#id{ color: red; }`

class选择器：`.center{ color: red; }`

组选择器：`h1,h2,p{ color: red;}`
## 标签

## 引用CSS和JavaScript文件

​	按照HTML5规范，一般来说，当CSS和JS文件被引用时，都会默认以 `text/css`和 `text/javascript` 的方式，没必要特意为其指定 `type` 类型。

```CSS
<!-- 外部 CSS -->
<link rel="stylesheet" type="text/css" href="code-guide.css">

<!-- 文档内CSS -->
<style>
/* ... */
</style>

<!-- JavaScript -->
<script src="code-guide.js"></script>
```
### 调用优先级

 - Inline style (inside an HTML element)
 - External and internal style sheets (in the head section)
 - Browser default

## 属性顺序

HTML属性应按照特定的顺序排列，以方便代码查阅。

- `class`

- `id`, `name`

- `data-*`

- `src`, `for`, `type`, `href`, `value`

- `title`, `alt`

- `role`, `aria-*`

  Class能让我们更好地重用组件，所以它打头阵；id则更加特定和专属，应尽量控制其使用（例如：内页书签）。