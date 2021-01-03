---
title: "CSS格式与标准总结"
date: 2017-09-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - CSS

---

## CSS基础知识

### 编码规则

- 编码统一为 utf-8；
- 使用双空格短缩进——只有这样才能保证代码能在所有渲染器中显示一致；
- 当成组使用选择器时，每个选择器单独一行；
- 一条声明的开大括号前留一空格；声明的关闭右括号新起一行；每条引用声明冒号后留一个空格；
- 每条声明有单独的一行，以便错误报告更精确；每条声明以分号结尾；
- 以逗号分隔属性值之间，要在逗号后面保留一个空格（ `box-shadow `）；
- 在`rgb()`、`rgba()`、`hsl()`、`hsla()`以及`rect()`属性值中逗号后不要加空格，这样能把多色彩值（逗号无空格）与多属性值的情况区分开来（逗号带空格）；
- 省略掉属性值以及颜色参数的首位0（例如：用 `.5` 代替 `0.5` ，用 `-.5px` 取代 `-0.5px`）；
- 十六进制值要用小写，简化十六进制值的写法，像 `#fff`这样，当一个文档中有很多的字母时，小写字母更容易分辨；
- 在选择器中引用属性值，例如：`input[type="text"]`，在某些情况下可选是否使用，这样做也能更好地统一代码；
- 零值不需要添加单位，例如：用 `margin: 0;` 代替 `margin: 0px;`；

### 属性书写顺序

有关联的属性声明之间应该按照以下顺序组在一起：

1. 布局定位
2. 盒模型，自身属性
3. 文本属性
4. 视觉设计(CSS)
5. 其他杂项

布局定位摆在第一位，因为它能把元素从文档正常流中脱离出来，覆盖盒模型样式。

盒模型表示着组件的度量尺寸和摆放位置，因此排第二。

文本排版和视觉都是属于元素内部的属性，不会影响到前两项，所以排为3、4。

```CSS
.header {
/* 布局定位属性 */
    display || visibility
    list-style
    position top || right || bottom || left
    z-index
    clear
    float
    overflow
/* 盒模型，自身属性 */
    width max-width || min-width
    height max-height || min-height
    overflow || clip
    margin
    padding
    outline
    border
    background
/* 文本属性 */
    color
    font
    text-decoration
    text-overflow
    text-align
    text-indent
    line-height
    white-space
    vertical-align
/* CSS 视觉设计 */
    cursor
    content
    box-shadow
    text-shadow
    border-radius
};
```

### 选择器

#### 选择器使用规则

- 为了更好的显示性能，请使用类名( class )而非一般的元素标签；
- 对于经常使用的组件，不要使用多个属性选择器，这样调用对浏览器性能有不小影响；
- 尽可能保持简短，如果可以请限制在3个元素以内；
- 只有在不得已的情况下再回溯最近的父级元素（例如：类名无前缀时）；

#### 选择器类型

- 元素(标签)选择器：`p { color: red; }`


- id选择器：`#id { color: red; }`


- class选择器：`.menuList { color: red; }`


- 组选择器：`h1,h2,p { color: red; }`
- 子选择器：用于选择指定标签元素的子元素，作用于元素的第一代后代
  -  `.menuList>li { border:1px solid red; }` 
- 后代选择器：用于选择指定标签元素下的后辈元素，作用于元素的所有后代
  - `.menuList span { color:red; }`
- 伪类选择器

  - `a:hover { text-decoration: none; }`

#### 优先级

浏览器根据优先级来决定给元素应用哪个样式，而优先级仅由选择器的匹配规则来决定，**不同的权重，权重值高则生效**。

- Level 0：!important 优先级是最高 (提升样式优先级) 
- Level 1：内联
- Level 2：ID 选择器
- Level 3：伪类 = 属性选择器 = 类选择器
- Level 4：元素选择器【p】
- Level 5：通用选择器 (*)
- Level 6：继承的样式

**权重记忆口诀**：*从 0 开始，一个行内样式 + 1000，一个 id 选择器 + 100，一个伪类、属性选择器、或者class + 10，一个元素选择器或者伪元素 + 1，通配符 + 0。*



