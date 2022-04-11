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

![Context](/images/ABAP/ABAP_XLSXWorkbench0.gif)

### Step2：设计 XLSX 模板

使用事务码 `ZXLWB_WORKBENCH` 来创建输出模板。

![ZXLWB_WORKBENCH](/images/ABAP/ABAP_XLSXWorkbench2.png)

执行结果：

![XLSX Workbench](/images/ABAP/ABAP_XLSXWorkbench2_1.png)

Workbench 分为三个大的功能区域：

1. 表格结构树

2. 属性选项卡（用于树中当前选择的组件）

3. Excel 模板

#### 表格结构树

![Form Structure](/images/ABAP/ABAP_XLSXWorkbench2_2.png)

*Sheet*：在运行时与生成的 XLSX 文件的工作表有直接关系，应该在表单中创建与打印表单中所需的工作表（一个或多个）一样多的工作表。如果事先不知道张数，可以将其动态插入到生成的 XLSX 文件中。 可以通过组件 Loop 在上下文的嵌套表的行中循环，然后在其中包含 Sheet。

![Sheet](/images/ABAP/ABAP_XLSXWorkbench2_3.png)

*Folder*：是一个具有多种用途的表单组件。首先，它必须用于通过将其他组件（具有相同类别或用途等）包含到文件夹中来组织和排列表单结构；其次，文件夹传播包含在其中的所有后续组件的公共属性。

![Folder](/images/ABAP/ABAP_XLSXWorkbench2_4.png)

*Loop*：该表单组件可以处理嵌套在上下文中的内部表行（循环表的行）。它可以用于运行时组件的嵌套子树的多次复制。Loop 是表单中动态结构的基础。

![Loop](/images/ABAP/ABAP_XLSXWorkbench2_5.png)

*Pattern*：该表单组件是在生成的 XLSX 文件的工作表上（按用户定义的顺序）布置的片段。 必须在运行时（在生成的 XLSX 文件创建时）复制（在工作表上）。注意：在运行时将模式插入到打印表单的工作表中的序列，仅由表单结构树中的组件序列定义（而不由 Excel 模板中的当前位置定义）。

![Pattern](/images/ABAP/ABAP_XLSXWorkbench2_6.png)

*Pattern (resizable)*：是 Pattern 的一种，继承了 Pattern 的大部分属性和原理，但有自己的特点。它的主要特点是能够通过合并附近的单元格来调整宽度或高度。Resizable pattern 的运行时大小取决于子区域。 子区域是下层所有 Pattern 占用的区域。 子区域在表单结构的树中表示为 Resizable pattern 下的子组件。Resizable pattern 也可以有另一个 Resizable patterns 作为子组件来提供多级格式。

![Pattern Resizable](/images/ABAP/ABAP_XLSXWorkbench2_7.png)

*Grid*：是一个用于简化从打印程序到表单的导出表格的组件。该组件支持将“平面”表处理为“多级”表（即分配给上下文表的 Grid，其行中包含嵌套的子表，描述子级）。 如果处理了多级表，则会对分配给高级别的列执行自动单元格合并（rowspan）。

![Grid](/images/ABAP/ABAP_XLSXWorkbench2_8.png)

*Tree*：是用于将 ALV 树从打印程序导出到表单的组件。

![Tree](/images/ABAP/ABAP_XLSXWorkbench2_9.png)

*Value*：该组件始终作为子节点直接位于 Form 结构树中的 Patterm 节点下。 Value 组件将来自 Context 的源字段的数据提供给 Form 的目标单元格。注意：Value 的目标单元格应仅位于 Excel 模板中父 Pattern 的单元格范围内。

![Value](/images/ABAP/ABAP_XLSXWorkbench2_10.png)

*Drawing*：该组件可以在生成的 XLSX 文件的工作表上放置图像（像素或矢量）。 在表格结构树中，Drawing 位于 Pattern 的正下方。

![Drawing](/images/ABAP/ABAP_XLSXWorkbench2_11.png)

*Chart*：该组件基于两个特殊组件，可以将图表插入到打印表单中。

- Chart model：它是 Excel 模板工作表上的任何图表，将用作示例格式：即图表类型（饼图、列、组合等）、系列数、样式、颜色、阴影、透明度、边框、 线宽、图表元素的相对定位和其他选项。 创建时使用 Excel 应用程序菜单：插入 --> 图表。
- Dataset(Grid)：它是 Form 结构中的任何 Grid 组件，它为 Chart 提供数据。 反过来，Grid 的列与图表 Seres 相关联。

![Drawing](/images/ABAP/ABAP_XLSXWorkbench2_12.png)

#### Excel 模板

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
REPORT z_shipping_labels.
* declare the context
DATA:
  gs_line         TYPE zcontext_shipping_label  ,
  gt_context      TYPE zcontext_shipping_labels .
* fill the context
gs_line-to_name    = 'Shane Hamby' .
gs_line-to_street  = '852 Ocean View Rd.' .
gs_line-to_town    = 'Bayshore' .
gs_line-to_state   = 'CA' .
gs_line-to_zip     = '94123' .
APPEND gs_line TO gt_context .
gs_line-to_name    = 'Dr.Henry Albrecht' .
gs_line-to_street  = '522 Ravenswood' .
gs_line-to_town    = 'East Bayshore' .
gs_line-to_state   = 'CA' .
gs_line-to_zip     = '93327' .
APPEND gs_line TO gt_context .
gs_line-to_name    = 'Hugh Molotsi' .
gs_line-to_street  = '1980 N.Stonecrest Rd.' .
gs_line-to_town    = 'West Middlefield' .
gs_line-to_state   = 'CA' .
gs_line-to_zip     = '12384' .
APPEND gs_line TO gt_context .
* call the form
CALL FUNCTION 'ZXLWB_CALLFORM'
  EXPORTING
    iv_formname    = 'SHIPPING_LABELS'
    iv_context_ref = gt_context[]
  EXCEPTIONS
    OTHERS         = 2 .
IF sy-subrc NE 0 .
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
          WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4 .
ENDIF .
```

