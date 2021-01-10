---
title: "CSS格式与标准总结"
date: 2017-09-03
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

1. 编码统一为 utf-8；
2. CSS 初始化 (CSS reset) 引入，不能随便更改；
3. 使用双空格短缩进，只有这样才能保证代码能在所有渲染器中显示一致；
4. 当成组使用选择器时，每个选择器单独一行；
5. 一条声明的开大括号前留一空格；声明的关闭右括号新起一行；每条引用声明冒号后留一个空格；
6. 每条声明有单独的一行，以便错误报告更精确；每条声明以分号结尾；
7. 在`rgb()`、`rgba()`、`hsl()`、`hsla()`以及`rect()`属性值中逗号后不要加空格，这样能把多色彩值（逗号无空格）与多属性值的情况区分开来（逗号带空格）；
8. 省略掉属性值以及颜色参数的首位0（例如：用 `.5` 代替 `0.5` ，用 `-.5px` 取代 `-0.5px`）；
9. 十六进制值要用小写，简化十六进制值的写法，像 `#fff`这样，当一个文档中有很多的字母时，小写字母更容易分辨；
10. 在选择器中引用属性值，例如：`input[type="text"]`，在某些情况下可选是否使用，这样做也能更好地统一代码；
11. 零值不需要添加单位，例如：用 `margin: 0;` 代替 `margin: 0px;`；
12. class 与 id 的使用： id是唯一的并是父级的，class是可以重复且是子级的；id仅使用在大的模块上，class可用在重复使用率高及子级中；
13. 充分利用 html 自身属性及样式继承原理减少代码量；
14. 背景图片请尽可能使用精灵图技术，减小http请求，精灵图按模块制作；

### 属性书写顺序

有关联的属性声明之间应该按照以下顺序组在一起：

1. 布局定位属性
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
    margin
    padding
    border
    background
    outline
/* 文本属性 */
    color
    font
    text-decoration
    text-align
    text-indent
    line-height
    vertical-align
    white-space
    text-overflow
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

#### CSS 基本选择器

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

#### CSS3 新增选择器

***属性选择器*** ：可以根据元素特定属性选择元素。这样就可以不借助于类或则id 选择器。

| 选择符        | 简介                                      |
| ------------- | ----------------------------------------- |
| E[att]        | 选择具有 att 属性的 E 元素                |
| E[att="val"]  | 选择具有 att 属性且 att=val 的 E 元素     |
| E[att^="val"] | 匹配具有 att 属性且值以 val 开通的 E 元素 |
| E[att$="val"] | 匹配具有 att 属性且值以 val 结尾的 E 元素 |
| E[att*="val"] | 匹配具有 att 属性且值中含有 val 的 E 元素 |

***结构伪类选择器*** ：主要根据文档结构来选择元素，常用于根据父级选择父级里面的子元素。

| 选择符           | 简介                                         |
| ---------------- | -------------------------------------------- |
| E:first-child    | 匹配父元素中的第一个子元素 E                 |
| E:last-child     | 匹配父元素中最后一个元素 E                   |
| E:nth-child(n)   | 选择某个父元素的一个或多个特定的元素的子元素 |
| E:first-of-type  | 指定类型 E 的第一个                          |
| E:last-of-type   | 指定类型 E 的最后一个                        |
| E:nth-of-type(n) | 指定类型 E 的第 n 个                         |

`E:nth-child(n)` ：将所有子元素排序选择，序号是固定的；先找到第 n 个孩子，然后看是否和 E 匹配

- n 可以是数字，关键字和公式

- n 如果是数字，既选择第 n 个子元素，里面的数字从 1 开始

- n 可以是关键字：even 偶数，odd 奇数

- n 可以是公式：常见公式如下

  - | 公式 | 取值                                     |
    | ---- | ---------------------------------------- |
    | n    | 从0开始计算，第0个或者超出的元素会被忽略 |
    | 2n   | 选择父元素下所有的偶数孩子               |
    | 2n+1 | 选择父元素下所有的奇数孩子               |
    | n+5  | 从第5个开始(包含第5个)到最后             |
    | -n+5 | 前5个(包含第5个) ....                    |

`E:nth-of-type(n)` ：将指定元素的盒子排列序号

- 执行的时候先看 E 指定的元素，之后看 :nth-of-type(n) 第n个孩子 

***伪元素选择器*** ：可以利用 CSS 创建新标签元素，而不需要 HTML 标签

| 选择符            | 简介                     |
| ----------------- | ------------------------ |
| element::before() | 在元素内部的前面插入内容 |
| element::afrer()  | 在元素内部的后面插入内容 |

- before 和 after 创建一个元素，但是属于行内元素
- 新创建的这个元素在文档树中是找不到的，所以称为伪元素
- before 和 after 必须有 content 属性
- before 在父元素内容的前面创建元素，after 在父元素的后面插入元素
- 伪元素选择器和标签选择器一样，权重为 1

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

### CSS 初始化 (CSS reset)

不同浏览器对有些标签的默认值是不同的，为了消除其对HTML文本呈现的差异，实现浏览器的兼容，需要对 CSS 初始化：重置浏览器的样式。

```css
/* 把我们所有标签的内外边距清零 */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}
/* em 和 i 斜体的文字不倾斜 */
em,
i {
    font-style: normal;
}
/* 去掉li 的小圆点 */
li {
    list-style: none;
}
img {
  /* 照顾低版本浏览器，如果图片外面包含了链接会有边框的问题 */
    border: 0;
  /* 取消图片底侧有空白缝隙的问题 */
    vertical-align: middle;
}
button {
  /* 当我们鼠标经过button 按钮的时候，鼠标变成小手 */
    cursor: pointer;
}
a {
    color: #666;
    text-decoration: none;
}
a:hover {
    color: #c81623;
}
button,
input {
  /* "\5B8B\4F53" 就是宋体的意思，这样浏览器兼容性比较好 */
    font-family: Microsoft YaHei, Heiti SC, tahoma, arial, Hiragino Sans GB, "\5B8B\4F53", sans-serif;
}
body {
  /* CSS3 抗锯齿形 让文字显示的更加清晰 */
    -webkit-font-smoothing: antialiased;
    background-color: #fff;
    font: 12px/1.5 Microsoft YaHei, Heiti SC, tahoma, arial, Hiragino Sans GB, "\5B8B\4F53", sans-serif;
    color: #666;
}
.hide,
.none {
    display: none;
}
/* 清除浮动 */
.clearfix:after {
    visibility: hidden;
    clear: both;
    display: block;
    content: ".";
    height: 0;
}
.clearfix {
    *zoom: 1;
}
```

