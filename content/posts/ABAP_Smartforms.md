---
title: " Smart Forms 使用 "
date: 2018-07-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---

### Smart Forms 简介

在 SAP 的 ABAP 编程中，一般开发过程都是在 Report 程序中取出所有需要的数据，将数据进行相应的处理以后保存到输出内表中，再打印内表中的数据。作为输出介质，SAP Smart Forms 支持打印机、传真、电子邮件或 Internet（通过使用生成的 XML 输出）。

激活 Smart Forms 时，系统会在运行时自动生成一个独立的外部 Function Module，对于程序内部定义的内表数据不能直接传递，需要定义外部的数据结构 Structure 或者使用标准的表结构，如果程序变更，需要传递的数据发生变化，那么该 Sturcture 也需要修改，这是 Smart Forms 中不方便的地方。

我们也可以在 Smart Forms 内部写取数据的逻辑，但是在 Smart Forms 中编程不是很方便，而且有时我们的数据需要首先以 List 或者 ALV List 的方式显示，然后再打印，所以在 Smart Forms 中书写取数据逻辑只能对一些要求非常简单的场合适用。

Smart Forms 的执行顺序是根据左边菜单从上到下执行的。

### 查找 Smart Forms

#### 方法 1

查找表格名称和打印程序名称的最佳方法是使用 SE38，输入程序 RSNAST00。 在以下语句处保留断点。

```ABAP
PERFORM (TNAPR-RONAM) IN PROGRAM (TNAPR-PGNAM) 
                         USING RETURNCODE US_SCREEN
                         IF FOUND.
```

- TNAPR-FONAM 的值为表格名称
- TNAPR-PGNAM 的值为驱动程序名称

#### 方法 2

Run `SE03`, choose "Search For Object in the request/transport"：Here run the report for the hit：R3TR SSFO.

#### 方法 3

事物码 `NACE` 可以查找 (例如。采购订单，销售订单等)；如果不想找，可以直接在

SSF_FUNCTION_MODULE_NAME 这个函数里打断点，只要调用smartfrom打印，总要走这个的。前台去执行打印操作，自然会进入这个function，然后看下对应的smartform名称即可。

- NACE 是用于链接应用程序类型，输出类型及其处理例程（如驱动程序和附加的脚本表单或 Smartforms）的 Tcode。

`TNAPR` 表可以根据 smartform 名字查找对应的 smartform 程序。

#### 事物码 smartforms

![smartforms](/images/ABAP/smartform0.png)

![smartforms](/images/ABAP/smartform1.png)

- Navigation window：导航窗口由节点和子节点组成。包含表单的所有元素（文本、窗口等）
- Maintenance window：维护窗口显示元素的具体属性
- Form printer：表格打印机窗口显示页面布局

### Global Settings

#### Form Attributes

表格属性，设置表格纸张大小及表格样式

![smartforms](/images/ABAP/smartform2.png)

#### Form Interface

表格接口，定义表格的输入参数，输出参数，tables 和异常信息。类似 BAPI 的接口参数。

![smartforms](/images/ABAP/smartform3.png)

![smartforms](/images/ABAP/smartform4.png)

输入输出参数，异常处理参数，都会有默认值，是 Smartform 生成时自动产生的参数

- 输入参数：用于传递 Smartform 的打印控制参数，也可传入自定义的参数值
- 输出参数：用于记录 Smartform 执行的结果，和执行状态的一些参数汇总
- 异常参数：Smartform 执行时遇到异常情况的捕捉

- Tables 参数：主要用来传递调用 Smartform 时用来展示的内表数据


#### Global Definitions

Global definitions 包含可以在整个表单中使用的数据，可进行数据定义和初始化。

![smartforms](/images/ABAP/smartform5.png)

### Form 内容设置

Pages and Windows

- 一般 Form 里包含表头、表身、表尾；
- 可以单页，也可以有多页内容

Form 内容显示为树形结构，在树结构中，为每个节点定义了一个选项卡，每个节点都可以链接到一个条件。当表单中满足条件时，系统将处理该节点，如果不满足，则系统不处理该节点。

通过对树的节点布局，以及节点内部详细的信息处理可以实现复杂的 Form 内容设置。

在一般情况下，树结构中的节点从上到下处理。每页上的分页数取决于当前页面上剩余的空间。

### Style 样式制作

Style 定义文字类型、大小样式等属性，可在 Text 对象内使用不同的样式。

#### 创建 Style

![Smartforms Style](/images/ABAP/smartform6.png)

![Smartforms Style](/images/ABAP/smartform7.png)

Header data：表头数据，定义默认段落属性等参数。 
Paragraph formats：段落格式，字体属性，制表符以及轮廓和编号等，在设定对象时可以选择需要的段落。 
Character formats：字符格式，定义是否粗体、倾斜、上标、下标、大小等，在一段文字内设置不同的字体、颜色等。

#### 使用 Style

在 Smartforms 的 Form Attributes 中引入 Style。

![Smartforms Style](/images/ABAP/smartform8.png)

### Smartforms 程序调用

打印程序在运行时调用函数`SSF_FUNCTION_MODULE_NAME`可以获取 Smart Forms 生成的 FM，然后调用得到的 Function Module 来执行 smartform 。

- 记录总页数：sfsy-page(当前页数) / sfsy-formpages(总页数)

```ABAP
DATA: fm_name TYPE rs38l_fnam,
      control_param TYPE ssfctrlop,
      composer_param TYPE ssfcompop,	
CLEAR fm_name.
"根据Form name将Form生成一个函数"
CALL FUNCTION 'SSF_FUNCTION_MODULE_NAME'
  EXPORTING
    formname           = 'Z_FORMNAME'
  IMPORTING
    fm_name            = fm_name
  EXCEPTIONS
    no_form            = 1
    no_function_module = 2
    OTHERS             = 3.
IF sy-subrc <> 0.
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
    WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
ENDIF.
"控制参数设置"
control_param-langu = sy-langu.
control_param-no_open = 'X'.
control_param-no_close = 'X'.
control_param-no_dialog = 'X'. " Not show dialog "
options-tddest = 'LP01'. " Printer name "
composer_param-tdimmed = 'X'.   " Print Immediately (Print Parameters) "
composer_param-tddelete = 'X'.  " Delete After Printing (Print Parameters) "   
composer_param-tdcopies = 2.    " Repeat printing times "
composer_param-tdpageslct = '1-3'. " Control the printed page number，1,2/1-3 "
"调用打印服务"
CALL FUNCTION lc_fmnam
  EXPORTING
    control_parameters = control_param
    output_options     = composer_param
  EXCEPTIONS
    formatting_error   = 1
    internal_error     = 2
    send_error         = 3
    user_canceled      = 4
    OTHERS             = 5.
IF sy-subrc <> 0.
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
    WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
ENDIF.
```

### Smartforms  调用异常信息获取

```ABAP
IF sy-subrc <> 0.
  DATA lt_errortab TYPE tsferror.
  FIELD-SYMBOLS: <fs_errortab>  TYPE LINE OF tsferror.
  CALL FUNCTION 'SSF_READ_ERRORS'
    IMPORTING
      errortab = lt_errortab.
  LOOP AT lt_errortab ASSIGNING <fs_errortab>.
    CALL FUNCTION 'NAST_PROTOCOL_UPDATE'
      EXPORTING
        msg_arbgb = <fs_errortab>-msgid
        msg_nr    = <fs_errortab>-msgno
        msg_ty    = <fs_errortab>-msgty
        msg_v1    = <fs_errortab>-msgv1
        msg_v2    = <fs_errortab>-msgv2
        msg_v3    = <fs_errortab>-msgv3
        msg_v4    = <fs_errortab>-msgv4
      EXCEPTIONS
        OTHERS    = 1.
  ENDLOOP.
ENDIF.
```




