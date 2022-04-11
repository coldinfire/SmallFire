---
title: " XLSXWorkbench 使用 "
date: 2022-03-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

[XLSX Workbench](https://sites.google.com/site/sapxlwb/home/eng) 是一款开源的 SAP 插件工具，用于在 SAP 环境中设计基于 Excel 的表单。 由于开发表单的完全可视化方法（类似 SMARTFORMS），它非常易于学习和使用。

### Step1：创建 Context

在 XLSX Worbkench 中，上下文是一个变量可以是结构、内表、类实例。上下文为生成的 XLSX 文件提供所需的数据，在打印程序中作为参数传递给 FM：`ZXLWB_CALLFORM`。 因此，可以使用类实例的任何结构或内部表（在打印程序中声明）作为上下文。 

- 注意：上下文必须引用 ABAP 数据字典类型（事务 SE11）或对象类型（事务 SE24）。

嵌入在上下文中的每一行都可以包含提供值的简单字段，也可以包含嵌套结构/表/类实例，嵌套层数不受限制。

### Step2：设计 XLSX 模板

使用事务码 `ZXLWB_WORKBENCH` 来创建输出模板。

![ZXLWB_WORKBENCH](/images/ABAP/ABAP_XLSXWorkbench1.png)

执行结果：

![XLSX Workbench](/images/ABAP/ABAP_XLSXWorkbench2.png)

Workbench 分为三个大的功能区域：

1. 表格结构树

2. 属性选项卡（用于树中当前选择的组件）

3. Excel 模板

### 开发打印程序

打印程序也可以分为三个部分：

- 使用 ABAP 字典类型声明变量（上下文）

- 实现填充变量（上下文）的逻辑


- 调用 FM:ZXLWB_CALLFORM，将变量（上下文）和表单名称作为输入参数传递

#### ZXLWB_CALLFORM 参数解析

输入参数：

![ZXLWB_CALLFORM](/images/ABAP/ABAP_XLSXWorkbench3.png)

- IV_FORMNAME：表格名称（必填）。

- IV_CONTEXT_REF：上下文（必填）。
- IV_VIEWER_TITLE：查看器屏幕标题栏中显示的文本。

- IV_VIEWER_INPLACE：是一个标志；如果 ='X'，查看器将位于模态 SAP 屏幕上；如果 = 空格，它将是 Excel 应用程序的一个单独窗口。

- IV_VIEWER_CALLBACK_PROG 和 IV_VIEWER_CALLBACK_FORM：回调程序和表单。仅当 IV_VIEWER_SUPPRESS = SPACE 时适用。

- IV_VIEWER_SUPPRESS：如果不是空白则标记，则查看器不会出现。

  - 无论此选项如何，生成的 XLS 文件的原始数据将在导出参数 EV_DOCUMENT_RAWDATA 中可用。

  - 无论此选项如何，都可以将生成的 XLSX 文件保存在桌面上 IV_SAVE_AS 参数指定的路径中。

- IV_PROTECT：如果标志不为空，保护数据不被用户更改。

- IV_SAVE_AS：前端计算机上的完整路径（包括文件名和扩展名），您要将生成的 XLSX 文件保存到该路径。如果此参数为空，则不保存。

- IV_SAVE_AS_APPSERVER：应用程序服务器计算机上的完整路径（包括文件名和扩展名），您要将生成的 XLSX 文件保存到该路径。如果此参数为空，则不保存。

- IT_DOCPROPERTIES：包含文档属性的名称及其值。

输出参数：

![ZXLWB_CALLFORM](/images/ABAP/ABAP_XLSXWorkbench3_1.png)

- EV_DOCUMENT_RAWDATA：生成的 XLS 文件的原始数据，数据类型为字节序列。

#### DEMO

```ABAP
REPORT  z_shipping_label.
* declare a context
DATA gs_context   TYPE zcontext_shipping_label .
* fill the context
gs_context-to_name    = 'Dan Tedford' .
gs_context-to_street  = '811 Alworth Avenue' .
gs_context-to_town    = 'Middlefield' .
gs_context-to_state   = 'CA' .
gs_context-to_zip     = '98567' .
* call the form
CALL FUNCTION 'ZXLWB_CALLFORM'
  EXPORTING
    iv_formname    = 'SHIPPING_LABEL'
    iv_context_ref = gs_context
  EXCEPTIONS
    OTHERS         = 2.
IF sy-subrc NE 0 .
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
          WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4 .
ENDIF .
```

