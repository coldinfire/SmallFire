---
title: "HTML 基础"
date: 2017-09-01
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - HTML
---



## HTML书写规范

1. 文档类型声明及编码：统一为 html5 声明类型 `<!DOCTYPE html>`; 编码统一为 `<meta charset=”utf-8″ />`, 书写时实现层次分明的缩进；

2. 所有编码均遵循 XHTML 标准，标签、属性、属性命名由小写字母、下划线、数字组成，且所有标签必须闭合，包括br, hr 等；属性值必须用双引号包括；

3. 非特殊情况下样式文件外链至 `<head>…</head >` 之间；非特殊情况下 JavaScript 文件外链至页面底部；

4. 引入样式文件或 JavaScript 文件时，须略去默认类型声明，写法如下:

   ```html
   <link rel="stylesheet" href="…" />
   <style>…</style>
   <script src="…"></script>
   ```

5. 引入 JS 库文件，文件名须包含库名称及版本号及是否为压缩版，比如 jquery-1.4.1.min.js; 引入插件，文件名格式为库名称 + 插件名称，比如 jQuery.cookie.js;

6. 充分利用无兼容性问题的 html 自身标签，比如 span, em, strong, optgroup, label, 等等；需要为 html 元素添加自定义属性的时候，首先要考虑下有没有默认的已有的合适标签去设置，如果没有，可以使用须以”data-” 为前缀来添加自定义属性，避免使用”data:” 等其他命名方式；

7. 语义化 html, 如标题根据重要性用 h*(同一页面只能有一个 h1), 段落标记用 p, 列表用 ul, 内联元素中不可嵌套块级元素；

8. 尽可能减少 div 嵌套;

9. 书写链接地址时，必须避免重定向，例如：href=”http://itaolun.com/”, 即须在 URL 地址后面加上 “/”；

10. 必须为含有描述性表单元素 (input, textarea) 添加 label, 如 

    ```html
    <p> 姓名: <input type="text" id="name" name="name" /></p > 
    须写成:
    <p><label for="name"> 姓名: </label><input id="name" type="text" /></p>
    ```

11. 重要图片必须加上 alt 属性；给重要的元素和截断的元素加上 title;

## HTML标签

![HTML Elements](/images/WEB/HTML_Elements.png)

- `<pre>`预处理标签的主要作用：预格式化的文本。被包围在 pre 元素中的文本通常会保留空格和换行符。

### 自闭合标签

`<br>` `<hr>` `<meta>` `<img>` `<input..>` `<option..>` `<link>`

#### 特殊字符

- 空格：`&nbsp;`


- 大于号：`&gt`
- 小于号：`&lt`
- 引号：`&quot`
- 版权号：`&copy`

## 标签属性声明顺序

HTML属性应按照特定的顺序排列，以方便代码查阅。

- `class`
- `id`, `name`
- `data-*`
- `src`, `for`, `type`, `href`, `value`
- `title`, `alt`
- `role`, `aria-*`

Class能让我们更好地重用组件，所以它打头阵；id则更加特定和专属，应尽量控制其使用（例如：内页书签）。

### 属性命名规则

```js
CLASS : nHeadTitle --> CLASS遵循小驼峰命名法
ID : n_head_title --> ID遵循名称&_
NAME : N_Head_Title --> NAME属性命名遵循首个字母大写&_
<div class="nHeadTitle" id="n_head_title" name="N_Head_Title"></div>
```

## 引用CSS和JavaScript文件

​	按照HTML5规范，一般来说，当CSS和JS文件被引用时，都会默认以 `text/css`和 `text/javascript` 的方式，没必要特意为其指定 `type` 类型。

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
  <!-- 链接样式 -->
  <head>
      <link rel="stylesheet" type="text/css" href="code-guide.css">
  </head>
  <!-- 导入样式 -->
  <style> @import url(style.css); </style>
<!-- JavaScript -->
  <script src="code-guide.js"></script>
```

### 调用优先级

***内联样式和外联样式的优先级和加载顺序有关***

 - Level1：Inline style (Inside an HTML element)
 - Level2：External and internal style sheets (In the head section)
 - Level3：Browser default

## 资源文件路径表达

### 路径分类

- 相对路径：(Relative Path) 相对于该文件的路径；
- 绝对路径：(Absolute Path) 从磁盘出发的路径；

### 路径表达式

#### 相对路径

- `/` 开头表示根目录
- `./` 表示当前目录
- `../` 上级目录
- 直接用文件名也表示当前目录

#### 绝对路径

绝对路径就是全路径。

```html
<img src="https://www.google.com/images/branding/googlelogo/1x/googlelogo_color_272x92dp.png" alt="Google" />
```

