---
title: " HTML 多媒体 "
date: 2017-09-03
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - HTML

---

### 图片语义容器

HTML5 的`<figure>` 和 `<figcaption>`元素，能够为图片提供一个语义容器，在标题和图片之间建立清晰的关联。 

`<figcaption>` 元素告诉浏览器和其他辅助的技术工具这段说明文字描述了 `<figure>` 元素的内容。

```html
<figure>
  <img src="../images/demo.jpg"
     alt="示例图片"
     width="400"
     height="341">
  <figcaption>该图片仅用来展示示例使用</figcaption>
</figure>
```

### 视频嵌入

`<video>` 标签可以在网页中嵌入视频。

```html
<video src="rabbit320.webm" controls>
  <p>你的浏览器不支持 HTML5 视频。可点击<a href="rabbit320.mp4">此链接</a>观看</p>
</video>
```

#### 属性

src：属性指向你想要嵌入网页当中的视频资源

controls：浏览器提供的控件界面来控制视频和音频的回放功能

width & height：可以用属性控制视频的尺寸

autoplay：这个属性会使音频和视频内容立即播放

loop：这个属性可以让音频或者视频文件循环播放

muted：这个属性会导致媒体播放时，默认关闭声音

poster：这个属性指向了一个图像的 URL，这个图像会在视频播放前显示

preload：这个属性被用来缓冲较大的文件，有 3 个值可选：

- `"none"` ：不缓冲
- `"auto"` ：页面加载后缓存媒体文件
- `"metadata"` ：仅缓冲文件的元数据

### 音频嵌入

`<audio>` 标签与 video 标签的使用方式几乎完全相同。音频播放器所占用的空间比视频播放器要小，由于它没有视觉部件，所以只需要显示出能控制音频播放的控件。