---
title: " CSS3 知识总结 "
date: 2017-09-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - CSS

---

### CSS3 新增选择器

#### 属性选择器

可以根据元素特定属性选择元素。这样就可以不借助于类或则 id 选择器。

| 选择符        | 简介                                      |
| ------------- | ----------------------------------------- |
| E[att]        | 选择具有 att 属性的 E 元素                |
| E[att="val"]  | 选择具有 att 属性且 att=val 的 E 元素     |
| E[att^="val"] | 匹配具有 att 属性且值以 val 开通的 E 元素 |
| E[att$="val"] | 匹配具有 att 属性且值以 val 结尾的 E 元素 |
| E[att*="val"] | 匹配具有 att 属性且值中含有 val 的 E 元素 |

#### 结构伪类选择器

主要根据文档结构来选择元素，常用于根据父级选择父级里面的子元素。

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

#### 伪元素选择器

可以利用 CSS 创建新标签元素，而不需要 HTML 标签；允许我们添加额外元素而不扰乱文档本身。

| 选择符            | 简介                     |
| ----------------- | ------------------------ |
| element::before() | 在元素内部的前面插入内容 |
| element::after()  | 在元素内部的后面插入内容 |

- before 和 after 创建一个元素，但是属于行内元素
- 新创建的这个元素在文档树中是找不到的，所以称为伪元素
- before 和 after 必须有 content 属性
- before 在父元素内容的前面创建元素，after 在父元素的后面插入元素
- 伪元素选择器和标签选择器一样，权重为 1

```css
/*伪元素清楚浮动*/
.record li::after {
    content: "";
    display: block;
    visibility: hidden;
    clear: both;
    overflow: hidden;
}
```

### 滤镜 Filter

Filter ：CSS属性将模糊或颜色偏移等图形效果应用于元素。

| 属性                       | 描述                                                         |
| -------------------------- | ------------------------------------------------------------ |
| filter: blur(5px);         | 图片使用高斯模糊效果，小括号里面的数值越大，越模糊。默认是0。 |
| filter: contrast(200%);    | 调整图像的对比度。值是0%的话图像会全黑。值是100%图像不变。值超过100%图像会比原来更亮。默认是1。 |
| filter: grayscale(80%);    | 将图像转换为灰度图像。值定义转换的比例。值为100%则完全转为灰度图像，值为0%图像无变化。值在0%到100%之间，则是效果的线性乘子。值默认是0。 |
| filter: hue-rotate(90deg); | 给图像应用色相旋转。"angle"一值设定图像会被调整的色环角度值。默认值是0deg，则图像无变化。超过360deg的值相当于又绕一圈。 |

### calc 函数

calc() 函数用于动态计算长度值，可以在声明 CSS 属性值时执行一些计算。

- 需要注意的是，运算符前后都需要保留一个空格，例如：`width: calc(100% - 10px)`；
- 任何长度值都可以使用 calc() 函数进行计算；
- calc() 函数支持 "+", "-", "*", "/" 运算；
- calc() 函数使用标准的数学运算优先级规则；

### transition 过渡

过渡是 CSS3 中具有颠覆性的特征之一，可以在不使用 Flash 动画或 JavaScript 的情况下，当元素从一种样式变换为另一种样式时为元素添加效果。

过渡动画：从一个状态渐渐的过渡到另外一个状态；经常和 :hover 一起搭配使用。

***哪个元素做过渡效果给该元素添加该属性***

transition: [要过渡的属性] [花费时间] [运动曲线] [何时开始], ... ;

- 属性：想要变化的 CSS 属性，宽度高度、背景颜色、内外边距等。如果想要所有属性都变化过渡使用 all
- 花费时间：单位是秒(s)，单位必须写
- 运动曲线：默认是 ease，可以省略。linear:匀速；ease-in:加速；ease-out:减速；ease-in-out:先加速后减速
- 何时开始：单位是秒，单位必须填写。可以设置延迟触发时间，默认是 0s

### transform 转换

#### 2D 移动：translate

2D 移动是 2D 转换里面的一种功能，可以改变元素在网页中的位置，类似定位。

`transform: translate(x,y);`：transform: translate(100px, 100px);

- x：是x轴上移动位置
- y：是y轴上移动位置

可以分开写：

- `transform: translateX(n);`
- `transform: translateY(n);`

特点：

- 定义 2D 转换中的移动，沿着 X 和 Y 轴移动元素

- translate 最大的优点：不会影响到其它元素的位置

- translate 中的百分比单位是相对于自身元素的

- 对行内标签没有效果

#### 2D 旋转：rotate

2D 旋转指的是让元素在二维平面内顺时针或逆时针旋转。

`transform: rotate(45deg);`

- rotate里面单位是度数 (deg)
- 角度为正时，顺时针旋转；为负时，逆时针旋转
- 默认旋转的中心点是元素的中心点

#### 转换中心点：transform-origin

可以设置元素转换的中心点，默认是以图形中心点为旋转中心点。

`transform-origin: x y;`

- 参数 x 和 y 用空格隔开
- x y 默认转换的中心点是元素的中心点 (50% 50%)
- 还可以给 x y 设置"像素"或则"方位名词" (top bottom left right center)

#### 2D 转换之缩放：scale

放大和缩小，通过该属性控制放大还是缩小。

`transform:scale(x,y);`

- 其中 x 和 y 用逗号分隔
- transform: scale(1,1) ：宽和高都放大一倍，相对于没有放大
- transform: scale(2,2) ：宽和高都放大 2 倍
- transform: scale(2) ：只有一个参数，第二个参数默认和第一个参数一样，相当于 scale(2,2)
- transform: scale(0.5,0.5) ：宽和高都缩小一倍
- scale 缩放最大的优势：可以设置转换中心缩放，默认以中心点缩放的，而且不影响其它盒子

#### 2D 转换综合写法

可以同时使用多个转换：`transform: translate() rotate() scale() ...;`

- 书写顺序会影响转换效果
- 同时有位移和其它属性时，将位移属性放到最前面

### 动画

动画(animation)是 CSS3 中具有颠覆性的特征之一，可以通过设置多个节点来精确控制一个或一组动画，常用来实现复杂的动画效果。

相比较过渡，动画可以实现更多变化，更多控制，连续自动播放等效果。

#### 动画使用

Step1：定义动画

用 @keyframes 定义动画

```css
@keyframes animation_name {
	0%{
	    transform: translate(0,0);
	}
    ...
	100%{
	    transform: translate(0,0);
	}
}
```

*动画序列*

- 0% 是动画的开始，100% 是动画的完成。该规则就是动画序列，百分比使用整数值
- 在 @keyframes 中规定某项 CSS 样式，就能创建由当前样式逐渐改为新样式的动画效果
- 动画是使元素从一种样式逐渐变化为另一种样式的效果，可以改变任意多的样式；任意多的次数
- 使用百分比来规定变化发生的时间，或用关键词 “from”，“to”，等同于 0%，100%

Step2：调用动画

指定元素使用动画效果

```css
div{
    /* 调用动画 */
    animation-name: animation_name;
    /* 持续时间 */
    animation-duration: keep_time;
}
```

#### 动画常用属性

| 属性                      | 描述                                                         |
| ------------------------- | ------------------------------------------------------------ |
| @keyframes                | 规定动画                                                     |
| animation                 | 所有动画属性的简写属性，除了 animation-play-state 属性       |
| animation-name            | 规定动画的名称，必须项                                       |
| animation-duration        | 规定动画完成一个周期所花费的时间，秒/毫秒。必须项            |
| animation-timing-function | 规定动画的速度曲线，默认是 ‘ease’                            |
| animation-delay           | 规定动画何时开始，默认是 0                                   |
| animation-iteration-count | 规定动画被播放的次数，默认是 1，还有 infinite                |
| animation-direction       | 规定动画是否在下一周期逆向播放，默认是 normal；alternate 逆播放； |
| animation-play-state      | 规定动画是否正在运行或暂停，默认是 running 还有 pause        |
| animation-fill-mode       | 规定动画结束后状态，保持 forwards，回到起始 backwards        |

动画速度曲线

| 属性值                | 描述                                                       |
| --------------------- | ---------------------------------------------------------- |
| linear                | 动画从头到尾的速度是相同的                                 |
| ease                  | 默认值。动画以低速开始，然后加快，在结束前变慢             |
| ease-in               | 动画以低速开始                                             |
| ease-out              | 动画以低速结束                                             |
| ease-in-out           | 动画以低速开始和结束                                       |
| steps(n)              | 动画分 n 步完成                                            |
| cubic-bezier(n,n,n,n) | 在 cubic-bezier 函数中共输入自定义值。可以是 0 到 1 的数值 |

动画属性简写

animation：动画名称 持续时间 运动曲线 何时开始 播放次数 是否反方向 动画起始或则结束时状态;

`animation: animation_name 5s linear 2s infinite alternate;`

- 简写属性不包括 animation-play-state
- 暂停动画：animation-play-state: paused; 经常和鼠标经过等操作配合使用
- 多个动画效果，中间使用逗号分隔

### 3D 转换

特点

- 近大远小
- 物体后面遮挡不可见

#### 三维坐标系

三维坐标系其实就是指立体空间，立体空间由三个轴共同组成。

- x 轴：水平向右 - x 右边是正值，左边是负值
- y 轴：垂直向下 - y下面是正值，下面是负值
- z 轴：垂直屏幕 - z 往外面是正值，往里面是负值

#### 3D 移动

3D 移动在 2D 移动的基础上添加了一个可以移动的方向，Z 轴移动。

`transform: translate3d(x,y,z);`

- transform: translateZ(100px)：仅仅在 Z 轴上移动，（一般使用 px 单位）

#### 透视：perspective

在 2D 平面产生近大远小的视觉立体，但是只是效果二维的

- 如果想要在网页产生 3D 效果需要透视，将 3D 物体投影在 2D 平面内
- 模拟人类的视觉位置
- 透视既是视距：人眼睛可以看到屏幕的距离
- 距离视觉点越近的在平面成像越大，越远成像越小
- 透视的单位是像素 px

透视写在被观察元素的父盒子上面

- d：就是视距，视距就是一个人的眼睛到屏幕的距离
- z：就是 z 轴，物体距离屏幕的距离，z 轴越大，我们看到的物体就越大

#### 3D 旋转

3D 旋转可以让元素在三维平面内沿着 x 轴，y 轴，z 轴或者自定义轴进行旋转。

transform: rotateX(45deg);

transform: rotateY(45deg);

transform: rotateZ(45deg);

transform: rotate3d(x,y,z,deg); 沿着自定义轴旋转， deg 定义角度值 (了解即可) 

- transform: rotate3d(1,0,0,45deg);  沿着 X 轴旋转 45deg
- transform: rotate3d(1,1,0,45deg); 沿着对角线旋转 45deg 

#### 3D 呈现：transform-style

- 控制子元素是否开启三维立体空间
- transform-style: flat;  默认值，子元素不开启 3D 立体空间
- transform-style: preserve-3d;  子元素开启立体空间
- 代码写给父级，但是影响的是子盒子