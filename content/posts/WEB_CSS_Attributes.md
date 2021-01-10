---
title: "CSS属性"
date: 2017-09-05
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
  - 标签所包含的行内元素，行内块元素都会受控制
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

### 背景属性

#### background-color

背景颜色的添加

- background: red;
- background-color: red;

设置透明色：第四个参数为透明度(0~1)

- background-color: rgba(0,0,0,0.3);

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

`body {background:#fff url('img.png') no-repeat fixed right top;}`

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

#### display 属性

-  display: none  隐藏对象，元素不再占用原来的空间。
-  display: block 除了转换为块级元素外，还有显示元素的意思

#### visiblity 属性

对于 CSS 里的 visibility 属性，通常其值被设置成 `visible` 或 `hidden`。

visibility: hidden 相当于 display: none，能把元素隐藏起来，但两者的区别在于：

-  visibility: hidden 使元素在网页上不可见，但仍占用原来的空间。

visibility 还可能取值为 collapse 。当设置元素 visibility: collapse 后，一般的元素的表现与 visibility: hidden 一样，也即其会占用空间。

但如果该元素是与 table 相关的元素，例如 table row、table column、table column group 等，其表现却跟 display: none 一样，也即其占用的空间会释放。

#### overflow 溢出

有定位的盒子，慎用 overflow: hidden 因为它会隐藏多余的部分。

- overflow: visible 不剪切内容也不添加滚动条

- overflow: hidden 隐藏溢出部分的内容 

- overflow: scroll 一直显示滚动条
- overflow: auto 内容溢出时自动显示滚动条，不溢出不显示滚动条

### 盒模型

盒子宽度 = width + padding左右 + border左右

盒子高度 = height + padding上下 + border上下

![BOX](/images/WEB/HTML_BOX.png)

#### 内容区：content

- 内容区指的是盒子中放置内容的区域，也就是元素中的文本内容，子元素都是存在于内容区中的
- 如果没有为元素设置内边距和边框，则内容区大小默认和盒子大小是一致的
- 通过 width 和 height 两个属性可以设置内容区的大小；width 和 height 属性只适用于块元素

#### 边框：border

在元素周围创建边框，边框是元素可见框的最外部。

- border: 2px solid #fff;
- border-width: 2px;
- border-style: dotted;    (solid:实线，dashed:虚线，dotted:点状虚线，double:双实线)
- border-color: red;
- border-collapse: collapse; (合并相邻边框)
- border-top/left/right/bottom 分别指定上、右、下、左四个方向的边框

#### 圆角边框

可定义四个参数，代表:左上->右上->右下->左下

border-radius: 10px 10px 10px 10px; 

也可单独定义每一个圆角：

- border-top-left-radius: 10px; 
- border-top-right-radius: 10px; 
- border-bottom-right-radius: 10px;
- border-bottom-left-radius: 10px;

#### 内边距：padding

内边距指的就是元素内容区与边框以内的空间。依据上、右、下、左的参数顺序声明。

- padding: 5px;
- padding: 5px 5px 5px 5px; 
- padding-top: 5px;
- padding-right: 5px;
- padding-bottom: 5px;
- padding-left: 5px;

#### 外边距：margin

外边距是元素边框与周围元素相距的空间；属性设置和外边距设置遵循同样的规则，属性值也相同。

- 块级盒子，居中显示：设置宽度，指定居中`{ margin: 0 auto }`

外边距合并：两个嵌套的盒子同时设置了外边距，会进行一个外边距合并

- 为父元素定义上边框
- 为父元素定义上内边距
- 为父元素添加 overflow: hidden;

#### 其它属性

**阴影属性**：box-shadow

- box-shadow: 1px 1px 5px #000;

**轮廓**：outline

- outline 是不占空间的，既不会增加额外的 width 或者 height
- outline的声明内容和border一致

#### CSS3新增特性

CSS3 中可以通过 box-sizing 来指定盒模型，有2个值：即可指定为 content-box、border-box，这样就可以计算盒子大小的方式就发生了改变。

- `{ box-sizing: content-box } `盒子大小为 width + padding + border (默认值)
- `{ box-sizing: border-box }` 盒子大小为 width

### 浮动：float

CSS 的 float（浮动），会使元素向左或向右移动，其周围的元素也会重新排列。可以让多个块级元素一行内排列显示。

- img { float: none; }
- img { float: right; }
- img { float: left; }

#### 清除浮动

元素浮动之后，周围的元素会重新排列，为了避免这种情况，使用 clear 属性。

clear 属性指定元素两侧不能出现浮动元素。

方法一：额外标签法，在浮动元素末尾添加一个空标签(块级元素)，并对空标签清除浮动

- text { clear: both; }

方法二：父级元素添加 overflow，无法处理溢出部分内容

- .box { overflow: hidden、auto、scroll }

方法三：父元素添加 :after 伪元素

```css
.clearfix:after{
  content: "";
  display: block;
  height: 0;
  clear: both;
  visibility hidden;
}
.clearfix { 
    *zoom: 1;
}
```

方法四：父元素添加双伪元素

```css
.clearfix:before,
.clearfix:after {
    content: "";
    display: tabel;
}
.clearfix:after {
    clear: both;
}
.clearfix {
    *zoom: 1;
}
```

### 定位：Position 

"子绝父相"：子级是绝对定位的话，父级要使用相对定位。

- 父级需要占有位置，以此是相对定位；子盒子不需要占有位置，则是绝对定位

#### position 属性值

**static** ：默认值，没有定位，遵循正常的文档流对象

**relative** 

- 相对定位元素的定位是相对其正常位置；
- 移动相对定位元素，但它原本所占的空间不会改变

**absolute** 

- 绝对定位的元素的位置相对于最近的已定位父元素，如果没有已定位的父元素，其位置相对于HTML
- 绝对定位的元素，不会占有位置，可以放到父盒子的任何一个地方，不会影响其他兄弟盒子

**fixed** 

- 元素的位置相对于浏览器可视窗口是固定位置；即使窗口是滚动的它也不会移动
- Fixed 定位的元素和其他元素重叠，不占有位置，脱离标准流

**sticky** 

- 以浏览器的可视窗口为参照点移动
- 占有原先的位置
- 必须添加 top、left、right、bottom 其中一个才有效

#### 偏移量

top、right、bottom、left

如果一个盒子既有 left 属性也有 right 属性，则默认会执行 left 属性，同理 top bottom 则默认 top。

#### z-index：定位的叠放次序

div { z-index: 1; }

- 数值可以是正整数、负整数或0，默认是auto，数值越大，盒子越靠上
- 如果属性值相同，则按照书写顺序，后来居上
- 数字后面不能加单位
- 只有定位的盒子才有 z-index 属性

### 定位拓展

#### 绝对定位的盒子居中

加了绝对定位的盒子不能通过 `margin: 0 auto` 水平居中，但是可以通过以下方法实现水平和垂直居中。

- `left: 50% `让盒子的左侧移动到父级元素的水平中心位置。
- `margin-left: -100px` 让盒子向左移动自身宽度的一半。

#### 定位特殊特性

- 行内元素添加绝对或者固定定位，可以直接设置高度和宽度
- 块级元素添加绝对或者固定定位，如果不给宽度或者高度，默认大小是内容的大小

#### 脱标的盒子不会触发外边距塌陷

浮动元素，绝对定位(固定定位)的元素都不会触发外边距合并的问题。

#### 绝对定位(固定定位)会完全压住盒子

浮动元素不同，只会压住它下面标准流的盒子，但是不会压住下面标准流盒子里面的文字；但是绝对定位(固定定位)会压住下面标准流所有的内容。

浮动之所以不会压住文字，因为浮动产生的目的最初是为了做文字环绕效果的，文字会围绕浮动元素。

### CSS3 新特性

#### 滤镜 Filter

filte：CSS属性将模糊或颜色偏移等图形效果应用于元素。

| 属性                       | 描述                                                         |
| -------------------------- | ------------------------------------------------------------ |
| filter: blur(5px);         | 图片使用高斯模糊效果，小括号里面的数值越大，越模糊。默认是0。 |
| filter: contrast(200%);    | 调整图像的对比度。值是0%的话图像会全黑。值是100%图像不变。值超过100%图像会比原来更亮。默认是1。 |
| filter: grayscale(80%);    | 将图像转换为灰度图像。值定义转换的比例。值为100%则完全转为灰度图像，值为0%图像无变化。值在0%到100%之间，则是效果的线性乘子。值默认是0。 |
| filter: hue-rotate(90deg); | 给图像应用色相旋转。"angle"一值设定图像会被调整的色环角度值。默认值是0deg，则图像无变化。超过360deg的值相当于又绕一圈。 |

#### calc 函数

calc() 函数用于动态计算长度值，可以在声明 CSS 属性值时执行一些计算。

- 需要注意的是，运算符前后都需要保留一个空格，例如：`width: calc(100% - 10px)`；
- 任何长度值都可以使用 calc() 函数进行计算；
- calc() 函数支持 "+", "-", "*", "/" 运算；
- calc() 函数使用标准的数学运算优先级规则；

#### transition 过渡

过渡是 CSS3 中具有颠覆性的特征之一，可以在不使用 Flash 动画或 JavaScript 的情况下，当元素从一种样式变换为另一种样式时为元素添加效果。

过渡动画：从一个状态渐渐的过渡到另外一个状态；经常和 :hover 一起搭配使用；*谁做过渡给谁添加该属性*。

transition: [要过渡的属性] [花费时间] [运动曲线] [何时开始], ... ;

- 属性：想要变化的 CSS 属性，宽度高度、背景颜色、内外边距等。如果想要所有属性都变化过渡使用 all
- 花费时间：单位是秒，单位必须写 (0.5s)
- 运动曲线：默认是 ease，可以省略。linear:匀速；ease-in:加速；ease-out:减速；ease-in-out:先加速后减速
- 何时开始：单位是秒，单位必须填写。可以设置延迟触发时间，默认是 0s，可以省略