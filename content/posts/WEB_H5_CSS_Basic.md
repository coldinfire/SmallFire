---
title: "CSS基础知识"
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

### @规则

[@namespace](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@namespace) ，告诉 CSS 引擎必须考虑XML命名空间。

[@media](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@media)， 如果满足媒体查询的条件则条件规则组里的规则生效。

[@page](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@page)， 描述打印文档时布局的变化.

[@font-face](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@font-face)， 描述将下载的外部的字体。

[@keyframes](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@keyframes)， 描述 CSS 动画的关键帧。

[@document](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@document)， 如果文档样式表满足给定条件则条件规则组里的规则生效。

[@supports](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@supports) 用于查询特定的 CSS 是否生效，可以结合 not、and 和 or 操作符进行后续的操作。

### CSS 初始化 (CSS reset)

CSS初始化是指重设浏览器的样式。不同浏览器对有些标签的默认值是不同的，为了消除其对HTML文本呈现的差异，实现浏览器的兼容，需要对 CSS 初始化。

#### 雅虎工程师提供的CSS初始化示例代码

```CSS
body,div,dl,dt,dd,ul,ol,li,h1,h2,h3,h4,h5,h6,pre,code,form,fieldset,legend,
input,button,textarea,p,blockquote,th,td { margin:0; padding:0; }
body { background:#fff; color:#555; font-size:14px; 
    font-family: Verdana, Arial, Helvetica, sans-serif; }
td,th,caption { font-size:14px; }
h1, h2, h3, h4, h5, h6 { font-weight:normal; font-size:100%; }
address, caption, cite, code, dfn, em, strong, th, var { font-style:normal; font-weight:normal;}
a { color:#555; text-decoration:none; }
a:hover { text-decoration:underline; }
img { border:none; }
ol,ul,li { list-style:none; }
input, textarea, select, button { font:14px Verdana,Helvetica,Arial,sans-serif; }
table { border-collapse:collapse; }
html {overflow-y: scroll;} 

.clearfix:after {content: "."; display: block; height:0; clear:both; visibility: hidden;}
.clearfix { *zoom:1; }
```

#### 腾讯官网 样式初始化

```CSS
body,ol,ul,h1,h2,h3,h4,h5,h6,p,th,td,dl,dd,form,fieldset,
legend,input,textarea,select{margin:0;padding:0} 
body{font:12px"宋体","Arial Narrow",HELVETICA;background:#fff;-webkit-text-size-adjust:100%;} 
a{color:#2d374b;text-decoration:none} 
a:hover{color:#cd0200;text-decoration:underline} 
em{font-style:normal} 
li{list-style:none} 
img{border:0;vertical-align:middle} 
table{border-collapse:collapse;border-spacing:0} 
p{word-wrap:break-word} 
```

#### 淘宝官网 样式初始化

```css
body, h1, h2, h3, h4, h5, h6, hr, p, blockquote, dl, dt, dd, ul, ol, li, pre,
form, fieldset, legend, button, input, textarea, th, td { margin:0; padding:0; } 
body, button, input, select, textarea { font:12px/1.5tahoma, arial, \5b8b\4f53; } 
h1, h2, h3, h4, h5, h6{ font-size:100%; } 
address, cite, dfn, em, var { font-style:normal; } 
code, kbd, pre, samp { font-family:couriernew, courier, monospace; } 
small{ font-size:12px; } 
ul, ol { list-style:none; } 
a { text-decoration:none; } 
a:hover { text-decoration:underline; } 
sup { vertical-align:text-top; } 
sub{ vertical-align:text-bottom; } 
legend { color:#000; } 
fieldset, img { border:0; } 
button, input, select, textarea { font-size:100%; } 
table { border-collapse:collapse; border-spacing:0; } 
```

#### 网易官网 样式初始化

```css
html {overflow-y:scroll;} 
body {margin:0; padding:29px00; font:12px"\5B8B\4F53",sans-serif;background:#ffffff;} 
div,dl,dt,dd,ul,ol,li,h1,h2,h3,h4,h5,h6,pre,form,fieldset,input,
textarea,blockquote,p{padding:0; margin:0;} 
table,td,tr,th{font-size:12px;} 
li{list-style-type:none;} 
img{vertical-align:top;border:0;} 
ol,ul {list-style:none;} 
h1,h2,h3,h4,h5,h6{font-size:12px; font-weight:normal;} 
address,cite,code,em,th {font-weight:normal; font-style:normal;} 
```

#### 京东官网 样式初始化

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

