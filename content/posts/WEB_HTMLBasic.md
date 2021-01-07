---
title: "HTML 基础知识汇总"
date: 2017-09-01
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - HTML

---

## HTML基础汇总

### HTML书写规范

1. 文档类型声明及编码：统一为 HTML5 声明类型 `<!DOCTYPE html>`; 编码统一为 `<meta charset="utf-8" />`, 书写时实现层次分明的缩进；

2. 所有编码均遵循 XHTML 标准，标签、属性、属性命名由小写字母、下划线、数字组成，且所有标签必须闭合，包括`<br />`,` <hr />` 等；属性值必须用双引号包括；

3. 非特殊情况下样式文件外链至 `<head>…</head >` 之间；

4. 非特殊情况下 JavaScript 文件外链至页面底部（如果需要界面未加载前执行的代码可以放在head标签后）；这样即便网络出现问题，浏览器也能把 DOM  加载渲染完成，防止因网络问题，页面加载 JavaScript 时间过长，出现空白界面现象；

5. 引入样式文件或 JavaScript 文件时，须略去默认类型声明，写法如下:

   ```html
   <link rel="stylesheet" href="…" />
   <style>…</style>
   <script src="…"></script>
   ```

6. 引入 JS 库文件，文件名须包含库名称及版本号及是否为压缩版，比如 jquery-1.4.1.min.js; 引入插件，文件名格式为库名称 + 插件名称，比如 jQuery.cookie.js；

7. 充分利用无兼容性问题的 html 自身标签，比如 span, em, strong, label, 等等；需要为 html 元素添加自定义属性的时候，首先要考虑下有没有默认的已有的合适标签去设置，如果没有，可以使用须以 "data-" 为前缀来添加自定义属性，避免使用 "data:"  等其他命名方式；

8. 语义化 html ；如标题根据重要性用 h*(同一页面只能有一个 h1), 段落标记用 p, 列表用 ul, 内联元素中不可嵌套块级元素；

9. 尽可能减少 div 嵌套；div作为一个块级的元素，主要应该用来描述布局；

10. 书写链接地址时，必须避免重定向，例如：href="https://www.google.com/", 即须在 URL 地址后面加上 “/”；

11. 为含有描述性表单元素 (input, textarea) 添加 label, 如 

    ```html
    <p> 姓名: <input type="text" id="name" name="name" /></p > 
    须写成:
    <p><label for="name"> 姓名: </label><input id="name" type="text" /></p>
    ```

12. 图片总是添加 alt，width，height 属性；给重要的元素和截断的元素加上 title；

13. 能以背景形式呈现的图片，尽量写入 css 样式中

### 标签属性声明顺序

HTML属性应按照特定的顺序排列，以方便代码查阅。

- `class`
- `id`, `name`
- `data-*`
- `src`, `for`, `type`, `href`, `value`
- `title`, `alt`
- `role`, `aria-*`

class 能让我们更好地重用组件，所以它打头阵；

id 则更加特定和专属，应尽量控制其使用（例如：内页书签）。

#### 属性命名规则

class 遵循小驼峰命名法

- class="nHeadTitle"

id 遵循名称 &_

- id="n_head_title"

name 属性命名遵循首个字母大写 &_

- name="N_Head_Title"

### HTML标签

![HTML Elements](/images/WEB/HTML_Elements.png)

- `<pre>`预处理标签的主要作用：预格式化的文本。被包围在 pre 元素中的文本通常会保留空格和换行符。

#### 元素类型

*块状元素* ：每个块级元素都从新的一行开始，并且其后的元素也另起一行；元素的高度、宽度、行高以及顶和底边距都可设置；元素宽度在不设置的情况下和父元素的宽度一致。

- 通过代码 `display: block;` 将元素设置为块级元素

| Element       | Element        | Element     | Element          | Element      | Element    |
| ------------- | -------------- | ----------- | ---------------- | ------------ | ---------- |
| `<header>`    | `<nav>`        | `<aside>`   | `<section>`      | `<article>`  | `<footer>` |
| `<h1>...<h6>` | `<div>`        | `<p>,<pre>` | `<ul>,<ol>,<dl>` | `<table>`    | `<form>`   |
| `<address>`   | `<blockquote>` | `<canvas>`  | `<video>`        | `<noscript>` | `<figure>` |

*内联元素* ：和其他元素都在一行上；元素的高度、宽度、行高及顶部和底部边距不可设置；元素的宽度就是它包含的文字或图片的宽度，不可改变。

- 通过代码 `display: inline;` 将元素设置为行内元素

| Element   | Element    | Element    | Element      | Element    | Element   |
| --------- | ---------- | ---------- | ------------ | ---------- | --------- |
| `<a>`     | `<span>`   | `<br>`     | `<img>`      | `<button>` | `<code>`  |
| `<b>`     | `<strong>` | `<i>`      | `<em>`       | `<sub>`    | `<sup>`   |
| `<input>` | `<label>`  | `<select>` | `<textarea>` | `<script>` | `<small>` |

*内联块状元素* ：同时具备内联元素、块状元素的特点；和其他元素都在一行上；元素的高度、宽度、行高以及顶和底边距都可设置。

- `<img>` `<input>`
- 通过代码 `display: inline-block;` 将元素设置为内联块状元素

#### 自闭合标签

`<br/>` `<hr/>` `<meta/>` `<img/>` `<input../>` `<option../>` `<link/>`

#### 特殊字符

| LABEL    | DESC      | LABEL   | DESC      | LABEL     | DESC      |
| -------- | --------- | ------- | --------- | --------- | --------- |
| `&nbsp;` | 空格      | `&quot` | 引号(")   | `&yen;`   | 人民币(¥) |
| `&gt`    | 大于号(>) | `&copy` | 版权号"@" | `&radic;` | 对号(√)   |
| `&lt`    | 小于号(<) | `&amp;` | &字符     | `&ne;`    | 不等于(≠) |

#### 链接

```html
普通链接：<a href="http://www.example.com/" target="_blank"> 链接文本 </a>
图像链接：<a href="http://www.example.com/"><img src="URL" alt="image"></a>
邮件链接：<a href="mailto:xxxxx@example.com"> 发送 e-mail </a>
书签链接：<a id="tips"> 提示部分 </a> <a href="#tips"> 跳到提示部分 </a>
```

`target` 属性值

- `_self` :默认值，在原页面打开链接
- `_blank` :在新窗口打开链接
- `_parent` :在父窗口中打开
- `_top` :打开时忽略所有的框架

#### 列表

- 无序列表：`<ul>...<li></li>...</ul>`
- 有序列表：`<ol>...<li></li>...</ol>`
- 定义列表：`<dl><dt>...<dd></dd>...</dt></dl>`

#### 表格

创建表格的五个元素：table、tr、th、td、caption

- `<table>...</table>`：整个表格以 < table > 标记开始、</table > 标记结束。
- `<caption>...</caption>`：表格的标题行设置
- `<tr>...</tr>`：表格的一行，所以有几对 tr 表格就有几行。
- `<th>...</th>`：表格的头部的一个单元格，表格表头样式显示。
- `<td>...</td>`：表格的一个单元格，一行中包含几对 td 说明一行中就有几列。

框架使用：`<iframe src="demo_iframe.htm" title="Iframe Example"></iframe>` 在一个页面中组织多个 html 文件

#### 表单

*action* 定义在提交表单时执行的动作；向服务器提交表单的通常做法是使用`submit`按钮。通常，表单会被提交到 web 服务器上的网页。

*method* 定在提交表单时所用的 HTTP 方法(GET 或 POST)

- GET 提交时，表单数据在页面地址栏中是可见的

- POST 的安全性更加，因为在页面地址栏中被提交的数据是不可见的

*name* 如果要表单数据正确地被提交，每个输入字段必须设置一个 name 属性。

```html
<form action="#" method="post/get">
  <label for="fname">User name:</label>
  <input type="text" id="fname" name="name" size="40" maxlength="50" autofocus><br/>
  <input type="password" name="password" placeholder="Enter pwd" required><br/>
  <input type="checkbox" name="hoppy" checked="checked">
  <input type="checkbox" name="hoppy"><br/>
  <input type="radio" name="gender" value="male" checked="checked">Male
  <input type="radio" name="gender" value="female">Female<br/>
  <input type="submit" value="send"><br/>
  <input type="reset" value="reset"><br/>
  <input type="button" onclick="alert('Hello World!')" value="Click Me!">
  <input type="hidden">
  <select name="cars" size="3" multiple>
    <option value="volvo" selected="selected">Volvo</option>
    <option value="saab">Saab</option>
    <option value="fiat">Fiat</option>
    <option value="audi">Audi</option>
  </select><br/>
  <textarea name="comment" rows="60" cols="20"></textarea>
  <button type="button" onclick="alert('Hello World!')">Click Me</button>
</form>
```

*表单分组元素*

`<fieldset>`元素用于将表单中的相关数据分组。

`<legend>`元素为`<fieldset>`元素定义标题。

```html
<form action="#">
  <fieldset>
    <legend>Personalia:</legend>
    <label for="fname">First name:</label><br>
    <input type="text" id="fname" name="fname" value="John"><br>
    <label for="lname">Last name:</label><br>
    <input type="text" id="lname" name="lname" value="Doe"><br><br>
    <input type="submit" value="Submit">
  </fieldset>
</form>
```

*预定义元素*

`<datalist>`元素为`<input>`元素指定预定义选项的列表，在输入框输入数据时将显示预定义选项的下拉列表。

`<input>`元素的 list 属性必须引用`<datalist>`元素的 id 属性。

```html
<form action="#">
  <input list="browsers" name="browser">
  <datalist id="browsers">
    <option value="Micosoft Edge">
    <option value="Firefox">
    <option value="Chrome">
    <option value="Opera">
  </datalist> 
</form>
```

### Graphics

#### Canvas

`<canvas>`元素用于在网页上绘制图形。

`<canvas>`元素只是图形的容器，需要使用JavaScript实际绘制图形。

画布有几种绘制路径，框，圆，文本和添加图像的方法。

#### SVG：可缩放矢量图形

SVG以XML格式定义了基于矢量的图形。

#### SVG和Canvas之间的差异

- SVG是用于描述XML中的2D图形的语言；Canvas可以实时绘制2D图形(使用JavaScript)
- SVG基于XML，这意味着每个元素在 SVG DOM 中都可用。可以为元素附加JavaScript事件处理程序
- 在SVG中，每个绘制的形状都被记住为一个对象。如果更改了SVG对象的属性，则浏览器可以自动重新渲染形状
- Canvas逐像素渲染。在Canvas中，一旦绘制了图形，浏览器就会将其忘记。如果需要更改其位置，则需要重新绘制整个场景，包括图形可能覆盖的所有对象

Canvas 和 SVG 的比较

| Canvas                            | SVG                                                   |
| --------------------------------- | ----------------------------------------------------- |
| 取决于分辨率                      | 分辨率独立                                            |
| 不支持事件处理程序                | 支持事件处理程序                                      |
| 文字渲染能力差                    | 适合具有较大渲染区域的应用程序(Google地图)            |
| 可以将结果图像另存为 .png 或 .jpg | 如果很复杂，则渲染速度较慢(经常使用DOM的东西都会很慢) |
| 非常适合图形密集型游戏            | 不适合游戏应用                                        |

### 引用 CSS 和 JavaScript 文件

按照HTML5规范，一般来说，当CSS和JS文件被引用时，都会默认以 `text/css`和 `text/javascript` 的方式，没必要特意为其指定 `type` 类型。

```html
<!-- CSS -->
  <!-- 内联样式 -->
  <div style="display: none;background:red"></div>
  <!-- 嵌入样式 -->
  <head>
      <style> 
          /* ... */
      </style>
  </head>
  <!-- 外部链接样式 -->
  <head>
      <link rel="stylesheet" type="text/css" href="code-guide.css">
  </head>
  <!-- 导入样式 -->
  <style> @import url(style.css); </style>
<!-- JavaScript -->
  <script src="code-guide.js"></script>
  <noscript>Sorry, your browser does not support JavaScript!</noscript>
```

#### 调用优先级

*头部声明的内联样式和外联样式的优先级和加载顺序有关*

 - Level1：内联样式
 - Level2：外联样式和头部声明的内联样式
 - Level3：浏览器默认样式

### 资源文件路径表达

#### 路径分类

- 相对路径：(Relative Path) 相对于该文件的路径；
- 绝对路径：(Absolute Path) 从磁盘出发的路径；

#### 路径表达式

相对路径

- `/` 开头表示根目录
- `./` 表示当前目录
- `../` 上级目录
- 直接用文件名也表示当前目录

绝对路径：就是全路径。

```html
<img src="https://www.baidu.com/img/PCtm_d9c8750bed0b3c7d089fa7d55720d6cf.png"  alt="baidu"/>
```

