---
title: " Web Dynpro ABAP - View Details "
date: 2018-10-19
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro
---

### 视图的整体介绍

![View Layout](/images/webdynproABAP/14.png)

**Properties** ：VIEW的属性，一般引入一些系统控件如alv，select-option等

**Layout** ：视图布局，显示的样式，选择字段的排版

**Inbound Plugs** ：转入的连接(内向链接)，一般视图跳转需要带些参数什么的，需要在这里定义plugs和参数相关信息

**Outbound Plugs** ：转出的连接(外向链接)，与Inbound对应，传出的连接和参数

**Context** ：上下文节点。视图使用的表，结构，全部放在这里。一般0-1/1-1/0-n三种。前两种相当于工作区，结构，后一种是内表。1-1时需要勾选Initialization Lead Selection

**Attributes** ：视图属性，可在本视图的各方法中使用

**Action** ：一般是对应ELEMENT对应的EVENT产生的

**Methods** ：定义该View使用到的所有方法，如按钮的执行和退出功能

### Properties 常用对象

#### Select-option使用

Step1：Select options 组件引入WDA程序。

双击Web Dynpro Component，在Used Components中添加组件，维护组件名称和对应的Component。

- SELECT_OPTIONS  <----->  WDR_SELECT_OPTIONS

![Web Dynpro Used Components](/images/webdynproABAP/Portal25.png)

Step2：将组件加入使用的视图中。

双击视图，选择Properties页签，在使用组件表格中点击创建，将组件对应的两个列表加进来。

![Web Dynpro View Properties](/images/webdynproABAP/Portal26.png)

Step3：在视图的WDDOINIT方法中，初始化SELECT OPTIONS。

在视图的Attributes页签中，添加组件对象

- HANDLE  Ref To IF_WD_SELECT_OPTIONS.

使用初始化代码

- [WDDOINIT 初始化代码](https://coldinfire.github.io/2018/SAPWebDynproABAP_P3/)

Step4：在视图的Layout中添加ViewContainerUIElement容器，该容器用来盛放Select-options的内容；在Window中将SELECT-OPTION组件嵌套视图中。

选中视图的ViewContainerUIElement容器，右键 ----> Embed View，弹出下图：

![Web Dynpro View Properties](/images/webdynproABAP/Portal27.png)

选择SELECT_OPTIONS组件绑定：

![Web Dynpro Window](/images/webdynproABAP/Portal28.png)

![Web Dynpro Window](/images/webdynproABAP/Portal29.png)

#### ALV 使用

ALV是WDA的组件，封装好的，和SELECT OPTION一样。所以使用的方法一般就是：

- 引入组件、初始化组件、数据绑定、数据显示

Step1：程序组件引入ALV组件。

双击Web Dynpro Component，在Used Components中添加组件，维护组件名称和对应的Component。

- ALV  <----->  SALV_WD_TABLE 

Step2：将组件加入使用的视图中。

双击视图，选择Properties页签，在使用组件表格中点击创建，将组件对应的两个列表加进来。

Step3：初始化ALV。

- [ALV_VIEW: WDDOINIT](https://coldinfire.github.io/2018/SAPWebDynproABAP_P1/)


Step4：数据绑定。



Step5：ALV控制器创建，并在窗口中添加，在视图的Layout中添加ViewContainerUIElement容器，该容器用来盛放ALV的结果。

选中视图的ViewContainerUIElement容器，右键 ----> Embed View，选择ALV Table绑定。

### Layout 界面元素

下面列表是一些常用的UI ELEMENT,在普通的WDA开发中会经常用到。

| Element                  | Desc                                                     | Element            | Desc             |
| ------------------------ | -------------------------------------------------------- | ------------------ | ---------------- |
| BUTTON                   | 按钮                                                     | CAPTION            | 标题             |
| DROPDOWN_BY_IDX          | 带序号的下拉                                             | INPUT_FIELD        | 文本输入框       |
| DROPDOWN_BY_KEY          | 带键值的下拉                                             | LABEL              | 说明文本         |
| FILE_UPLOAD              | 上载文件工具，选择文件路径                               | LINK_TO_ACTION     | 突出显示，下划线 |
| TEXT_EDIT                | 文本编辑，大文本框                                       | TABLE              | 表控制           |
| TEXT_VIEW                | 文本显示，不可编辑                                       | TABSTRIP           | 页签             |
| TRAY                     | 可折叠块                                                 | PAGE_HEADER        | 页抬头标题       |
| TREE                     | 树                                                       | TOOLBAR            | 工具条           |
| VIEW_CONTAINER_UIELEMENT | 视图组建控制器（一般用来放Select-Option、ALV或其他组建） | HORIZONTAL _GUTTER | 水平分割线       |
| GROUP                    | 分组工具，无工具功能                                     | IMAGE              | 上传图片         |

### Layout属性

区域的Layout属性有：FlowLayout、GridLayout、MatrixLayout、RowLayout。

区域的Layout一般选择MatrixLayout 

- MatrixHeadData 行开头 
- MatrixData 紧接着 HEAD，没有新的MatrixHeadData，会一直往后排
- 新的MatrixHeadData，另起一行

| 属性值        | Desc                             | 属性值     | Desc                                                         |
| ------------- | -------------------------------- | ---------- | ------------------------------------------------------------ |
| enabled       | 功能性，控制字段等是否灰色显示   | width      | 占的宽度，或者比例 一般200，250，150，TRAY 一般95%之类       |
| readOnly      | 控制字段是否可编辑               | EVENTS     | 事件，每种ELEMENT对应事件不同，有field的输入，按钮的事件     |
| suggestValues | 值建议                           | cellDesign | 单元格格式                                                   |
| value         | 绑定的VALUE                      | colSpan    | 字段占列数，比如文本框，我们可以设置占5格等（前提是容器TRAY,CONTAINER的COL设置的够） |
| visible       | 可见性，控制字段，组件等是否可见 | hAlign     | 格式                                                         |

 