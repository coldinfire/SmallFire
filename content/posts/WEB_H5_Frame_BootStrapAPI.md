---
title: " Bootstrap API 学习总结 "
date: 2017-10-11
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - JS

---

- [Bootstrap 中文网](https://v3.bootcss.com/)
- [Bootstrap 官方网站](https://getbootstrap.com/)

### 全局 CSS 样式

页面主题默认设置：Bootstrap 将全局 `font-size` 设置为 **14px**，`line-height` 设置为 **1.428**。这些属性直接赋予 `<body>` 元素和所有段落元素。另外，`<p>`（段落）元素还被设置了等于 1/2 行高（即 10px）的底部外边距（margin）。

#### 排版

| Class                  | 描述               | Class               | 描述                                  |
| :--------------------- | :----------------- | :------------------ | :------------------------------------ |
| .h1 ~ .h6              | 标题标签           | .text-lowercase     | 文本全部小写显示                      |
| .small                 | 可以用来标记副标题 | .text-uppercase     | 文本全部大写显示                      |
| .text-left/.text-right | 文本左(右)对齐     | .text-capitalize    | 文本首字母大写                        |
| .text-center           | 文本居中           | .initialism         | 缩略语，可以让 font-size 变得稍微小些 |
| .text-justify          | 文本左右对齐       | .blockquote-reverse | 引用呈现内容右对齐的效果              |
| .text-nowrap           | 文本内容不折叠显示 | .list-unstyled      | 无样式列表显示                        |
|                        |                    | .list-inline        | 将所有列表元素放置于同一行            |

#### 按钮

`a` 标签需要设置 `role="button"` 属性。

| Class        | 描述                   | Class      | 描述                                         |
| :----------- | :--------------------- | :--------- | :------------------------------------------- |
| .btn         | 将标签或元素显示为按钮 | .btn-block | 将按钮拉伸至父元素100%的宽度，转换成块级元素 |
| .btn-default | 默认按钮样式           | .btn-sm    | 小按钮样式                                   |
| .btn-primary | 首选项样式             | .btn-xs    | 超小按钮样式                                 |
| .btn-success | 成功样式               | .btn-lg    | 大按钮样式                                   |
| .btn-info    | 一般信息样式           | .active    | 按钮处于激活状态，表现为被按压下去           |
| .btn-warning | 警告样式               | disabled   | 添加 disabled 属性，设置按钮为禁用状态       |
| .btn-danger  | 危险样式               | .btn-link  | 超链接样式                                   |

#### 图片

通过为图片添加 `.img-responsive` 类可以让图片支持响应式布局。其实质是为图片设置了 `max-width: 100%;`、 `height: auto;` 和 `display: block;` 属性，从而让图片在其父元素中更好的缩放。

如果需要让使用了 `.img-responsive` 类的图片水平居中，请使用 `.center-block` 类，不要用 `.text-center`。

- img-rounded：圆角边框正方形图片
- img-circle：圆形边框图片
- img-thumbnail：相框类型的边框图片

#### 表格

表格整体属性设置

| Class             | 描述                                                         |
| :---------------- | :----------------------------------------------------------- |
| .table            | 设置默认的table样式                                          |
| .table-striped    | 给 `<tbody>`之内的每一行增加斑马条纹样式                     |
| .table-bordered   | 为表格和其中的每个单元格增加边框                             |
| .table-hover      | 让 `<tbody>` 中的每一行对鼠标悬停状态作出响应                |
| .table-condensed  | 让表格更加紧凑，单元格中的padding 均会减半                   |
| .table-responsive | 将任何 `.table` 元素包裹在 `.table-responsive` 元素内，即可创建响应式表格。 |
|                   | 其会在小屏幕设备上（小于768px）水平滚动。当屏幕大于 768px 宽度时，水平滚动条消失。 |

通过这些状态类可以为行或单元格设置颜色。

| Class    | 描述                                 |
| :------- | :----------------------------------- |
| .active  | 鼠标悬停在行或单元格上时所设置的颜色 |
| .success | 标识成功或积极的动作                 |
| .info    | 标识普通的提示信息或动作             |
| .warning | 标识警告或需要用户注意               |
| .danger  | 标识危险或潜在的带来负面影响的动作   |

#### 表单

单独的表单控件会被自动赋予一些全局样式。所有设置了 `.form-control` 类的 `<input>`、`<textarea>` 和 `<select>` 元素都将被默认设置宽度属性为 `width: 100%;`。 

将 `label` 元素和前面提到的控件包裹在 `.form-group` 中可以获得最好的排列。

| Class                                | 描述                                                         |
| :----------------------------------- | :----------------------------------------------------------- |
| .form-group                          | 将 label元素和控件更好的排列                                 |
| .form-group-lg/.form-group-sm        | 为 .form-horizontal 包裹的 label 元素和表单控件快速设置尺寸  |
| .form-control                        | 默认设置宽度属性为  width: 100%;                             |
| .form-inline                         | 使 form 内容左对齐并且表现为 `inline-block` 级别的控件       |
| .sr-only                             | 将 label 标签隐藏不显示                                      |
| .form-horizontal                     | 将 from 的 label 标签和控件组水平并排布局，改变 .form-group 的行为 |
| .checkbox-inline / .radio-inline     | 使多选框 / 单选框控件排列在一行                              |
| .form-control-static                 | 将一行纯文本和 `label` 元素放置于同一行，为 `<p>` 元素添加该类 |
| readonly                             | 为输入框设置 `readonly` 属性可以禁止用户修改输入框中的内容   |
| .has-success/.has-warning/.has-error | 任何包含在此元素之内的元素都将接受这些校验状态的样式         |
| .input-lg/.input-sm                  | 为控件设置高度                                               |
| .col-xs-n                            | 用栅格系统中的列（column）包裹输入框或其任何父元素，都可设置宽度 |

### 组件

无数可复用的组件，包括字体图标、下拉菜单、导航、警告框、弹出框等。

### JavaScript 插件

jQuery 插件为 Bootstrap 的组件赋予了“生命”。可以简单地一次性引入所有插件，或者逐个引入到你的页面中。