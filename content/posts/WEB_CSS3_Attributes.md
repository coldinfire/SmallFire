---
title: "CSS3 属性"
date: 2017-09-13
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - CSS
---



## CSS3 属性介绍

### 滤镜 Filter

filte：CSS属性将模糊或颜色偏移等图形效果应用于元素。

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

过渡动画：从一个状态渐渐的过渡到另外一个状态；经常和 :hover 一起搭配使用；*谁做过渡给谁添加该属性*。

transition: [要过渡的属性] [花费时间] [运动曲线] [何时开始], ... ;

- 属性：想要变化的 CSS 属性，宽度高度、背景颜色、内外边距等。如果想要所有属性都变化过渡使用 all
- 花费时间：单位是秒，单位必须写 (0.5s)
- 运动曲线：默认是 ease，可以省略。linear:匀速；ease-in:加速；ease-out:减速；ease-in-out:先加速后减速
- 何时开始：单位是秒，单位必须填写。可以设置延迟触发时间，默认是 0s，可以省略

### transform 转换

#### 2D 移动：translate

2D移动是2D转换里面的一种功能，可以改变元素在网页中的位置，类似定位。

`transform: translate(x,y);`：transform: translate(100px, 100px);

- x：是x轴上移动位置
- y：是y轴上移动位置

可以分开写：

- `transform: translateX(n);`
- `transform: translateY(n);`

特点：

- 定义 2D 转换中的移动，沿着 X 和 Y 轴移动元素

- translate 最大的优点：不会影响到其它元素的位置

- transle中的百分比单位是相对于自身元素的

- 对行内标签没有效果

#### 2D 旋转：rotate

2D 旋转指的是让元素在2维平面内顺时针旋转或则逆时针旋转。

`transform: rotate(45deg);`

- rotate里面单位是 deg (度数)
- 角度为正时，顺时针旋转；为负时，逆时针旋转
- 默认旋转的中心点是元素的中心点

#### 转换中心点：transform-origin

可以设置元素转换的中心点，默认是以图形中心点为旋转中心点。

`transform-origin: x y;`

- 参数 x 和 y 用空格隔开
- x y 默认转换的中心点是元素的中心点 (50% 50%)
- 还可以给 x y 设置"像素"或则"方位名词" (top bootm left right center)

#### 2D 转换之缩放：scale

放大和缩小，通过该属性控制放大还是缩小。

`transform:scale(x,y);`

- 其中 x 和 y 用逗号分隔
- transform:scale(1,1) ：宽和高都放大一倍，相对于没有放大
- transform:scale(2,2) ：宽和高都放大 2 倍
- transform:scale(2) ：只有一个参数，第二个参数默认和第一个参数一样，相当于 scale(2,2)
- transform:scale(0.5,0.5) ：宽和高都缩小一倍
- scale 缩放最大的优势：可以设置转换中心缩放，默认以中心点缩放的，而且不影响其它盒子

#### 2D 转换综合写法

可以同时使用多个转换：`transform: translate() rotate() scale() ...;`

- 书写顺序会影响转换效果
- 同时有位移和其它属性时，将位移属性放到最前面

### 动画

动画 (animation) CSS3 中具有颠覆性的特征之一，可以通过设置多个节点来精确控制一个或一组动画，常用来实现复杂的动画效果。

相比较过渡，动画可以实现更多变化，更多控制，连续自动播放等效果。

#### 动画使用

Step1：定义动画

用 keyframes 定义动画

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

- 0% 是动画的开始，100% 是动画的完成。该规则就是动画序列，百分比使用整数
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
| animation-duration        | 规定动画完成一个周期所花费的时间，s/ms。必须项               |
| animation-timing-function | 规定动画的速度曲线，默认是 ‘ease’                            |
| animation-delay           | 规定动画何时开始，默认是 0                                   |
| animation-iteration-count | 规定动画被播放的次数，默认是 1，还有 infinite                |
| animation-direction       | 规定动画是否在下一周期逆向播放，默认是 normal；alternate 逆播放：动画会在奇数次数（1、3、5 等）正常播放，而在偶数次数（2、4、6 等）逆向播放 |
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
- 暂停动画：animation-play-state: puased; 经常和鼠标经过等操作配合使用
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

#### 3D 位移和旋转

3D 位移：translate3d(x,y,z);

3D 旋转：rotate3d(x,y,z);

透视：perspective

3D 呈现：transfrom-style