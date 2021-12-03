---
title: " JQuery 动画 "
date: 2017-12-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - JQuery

---

## JQuery 动画

### 默认方式

| 方法                          | 描述                                                     |
| :---------------------------- | :------------------------------------------------------- |
| hide([speed],[easing],[fn])   | 同时修改多个样式属性，将选择元素的 display 样式改为 none |
| show([speed],[easing],[fn])   | 将元素的 display 样式设置为之前的显示状态(其它)          |
| toggle([speed],[easing],[fn]) | 切换元素的可见状体                                       |

hide() 方法在隐藏的时候会先保存原先的 display 属性值("block"、"inline"或其它除了"none" 之外的值)。当调用 show() 方法时，会根据 hide() 方法保存的属性值来显示元素。

#### 参数解析

`speed`：设置动画的速度，有三种预定义字符串("slow" — 600ms、"normal" — 400ms、"fast" — 200ms)，也可以输入毫秒数值；输入关键字时需要添加引号，数字则不需要

`easing`：用来指定切换效果，默认是 "swing"，可用参数 "linear"

- swing：动画执行时效果是开始慢，中间快，最后慢
- linear：动画匀速执行

`fn`：在动画完成时执行的函数，每个元素执行一次

### 滑动方式

| 方法                               | 描述                                   |
| :--------------------------------- | :------------------------------------- |
| slideDown([speed],[easing],[fn])   | 改变元素的高度，将元素由上至下延伸显示 |
| slideUp([speed],[easing],[fn])     | 改变元素的高度，将元素由下到上缩短隐藏 |
| slideToggle([speed],[easing],[fn]) | 通过高度变化来切换匹配元素的可见性     |

### 淡入淡出方式

| 方法                              | 描述                                 |
| :-------------------------------- | :----------------------------------- |
| fadeIn([speed],[easing],[fn])     | 改变元素的不透明度，直到元素完整显示 |
| fadeOut([speed],[easing],[fn])    | 改变元素的不透明度，直到元素完全消失 |
| fadeToggle([speed],[easing],[fn]) | 切换以上两种状态                     |

### 自定义动画

在 jQuery 中，可以使用 animate() 方法来自定义动画。通过该方法，能够实现更加复杂的动画效果，更具有灵活性。

语法结构：`animate([params,]speed,callback);`

- params：包含样式属性及值的映射，eg：{property1:"value",property2:"value2"...}
- speed：动画执行的速度
- callback：在动画完成时执行的函数

复杂示例：

```javascript
$("#panel").click(function()
  $(this).animate({left:"400px",height:"200px",opacity:"1"},2000)
    .animate({top:"200px",width:"200px"},3000,function(){
      $(this).css("border","5px solid green")
  })
});
```

#### 动画队列

一组元素上的动画效果

- 当在一个 animate() 方法中应用多个属性时，动画时同时发生的
- 当以链式的写法应用动画方法时，动画时按照顺序发生的

多组元素上的动画效果

- 默认情况下，动画都是同时发生的
- 当以回调的形式应用动画方式时，动画是按照顺序发生的

在动画方法中，要注意其它非动画方法会插队，需要将这些方法写在动画方法的回调函数中。

### 停止动画

语法结构：`stop([clearQueue][,gotoEnd]);`

clearQueue：true 后续动画不执行；false(默认值) 后续动画会执行

gotoEnd：true 立即执行完成当前动画；false(默认值) 立即停止当前动画

当调用stop()方法后，队列里面的下一个动画将会立即开始。

但是，如果参数clearQueue被设置为true，那么队列面剩余的动画就被删除了，并且永远也不会执行。

如果参数jumpToEnd被设置为true，那么当前动画会停止，但是参与动画的每一个CSS属性将被立即设置为它们的目标值。