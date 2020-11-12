---
title: " Web Dynpro ABAP的View Details "
date: 2018-10-24
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro
---

### 界面元素

下面列表是一些常用的UI ELEMENT,在普通的WDA开发中会经常用到。

| Element                  | Desc                                      | Element        | Desc                                         |
| ------------------------ | ----------------------------------------- | -------------- | -------------------------------------------- |
| BUTTON                   | 按钮                                      | GROUP          | 分组工具，无工具功能                         |
| CAPTION                  | 标题                                      | IMAGE          | 图片（需要将图片文件上传到该程序中才能使用） |
| DROPDOWN_BY_IDX          | 带序号的下拉                              | INPUT_FIELD    | 文本输入框                                   |
| DROPDOWN_BY_KEY          | 带键值的下拉                              | LABEL          | 说明文本                                     |
| FILE_UPLOAD              | 上载文件工具，选择文件路径                | LINK_TO_ACTION | 突出显示，下划线                             |
| TEXT_EDIT                | 文本编辑，大文本框                        | TABLE          | 表控制                                       |
| TEXT_VIEW                | 文本显示，不可编辑                        | TABSTRIP       | 页签                                         |
| TRAY                     | 可折叠块                                  | PAGE_HEADER    | 页抬头标题                                   |
| TREE                     | 树                                        | TOOLBAR        | 工具条                                       |
| VIEW_CONTAINER_UIELEMENT | 视图组建控制器（一般用来放ALV或其他组建） |                |                                              |

### Layout属性

区域的Layout一般选择MatrixLayout 

- MatrixHeadData 行开头 MatrixData 紧接着 HEAD，没有新的HEAD，会一直往后排。新的HEAD，另起一行。

常用属性：

| 属性值        | Desc        | 属性值     | Desc                                                         |
| ------------- | ----------- | ---------- | ------------------------------------------------------------ |
| enabled       | 是否灰色显  | width      | 占的宽度，或者比例 一般200，250，150，TRAY 一般95%之类       |
| readOnly      | 只显示      | EVENTS     | 事件，每种ELEMENT对应事件不同，有field的输入，按钮的事件     |
| suggestValues | 值建议      | cellDesign | 单元格格式                                                   |
| value         | 绑定的VALUE | colSpan    | 字段占列数，比如文本框，我们可以设置占5格等（前提是容器TRAY,CONTAINER的COL设置的够） |
| visible       | 可见        | hAlign     | 格式                                                         |

 