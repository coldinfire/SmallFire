---
title: "Smartforms"
date: 2018-07-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---

### Smartforms 使用

在 SAP 的 ABAP 编程中，一般开发过程都是在 Report 程序中取出所有需要的数据，将数据进行相应的处理以后保存到输出内表中，再打印内表中的数据。

但是 SmartForms 是一个独立的外部 Function Module，对于程序内部定义的内表数据不能直接传递，需要定义外部的数据结构 Structure 或者使用标准的表结构，如果程序变更，需要传递的数据发生变化，那么该 Sturcture 也需要修改，这是 SmartForms 中不方便的地方。

我们也可以在 SmartForms 内部写取数据的逻辑，但是在 SmartForms 中编程不是很方便，而且有时我们的数据需要首先以 List 或者 ALV List 的方式显示，然后再打印，所以在 smartforms 中书写取数据逻辑只能对一些要求非常简单的场合适用。

Smartforms的执行顺序是根据左边菜单从上到下执行的。

### 查找Smartforms

**TCode : NACE** 可以查找 (例如。采购订单，销售订单等)

- NACE 是用于链接应用程序类型，输出类型及其处理例程（如驱动程序和附加的脚本表单或 Smartforms）的 Tcode。

**Table : TNAPR** 根据smartform名字查找对应的smartform程序

#### TCode : smartforms

![smartforms](/images/ABAP/smartform0.png)

### Smartforms组成

Smartforms 包括全局设置和 FORM 内容两大部分。

全局设置

- 设置form的页面格式
- 设置form将要展示的数据接口参数类型
- 设置form中进行数据处理的内容转换时需要的变量

From 内容

- 一般 Form 里包含表头、表身、表尾；
- 可以单页，也可以有多页内容

![smartforms](/images/ABAP/smartform1.png)

#### Form Attributes

表格属性，设置表格纸张大小及表格样式

![smartforms](/images/ABAP/smartform2.png)

#### Form Interface

表格接口，定义表格的输入参数，输出参数，tables和异常信息。类似BAPI的接口参数。

![smartforms](/images/ABAP/smartform3.png)

![smartforms](/images/ABAP/smartform4.png)

输入输出参数，异常处理参数，都会有默认值，是 Smartform 生成时自动产生的参数

- 输入参数：用于传递 Smartform 的打印控制参数，也可传入自定义的参数值
- 输出参数：用于记录 Smartform 执行的结果，和执行状态的一些参数汇总
- 异常参数：Smartform 执行时遇到异常情况的捕捉

Tables主要用来传递调用 Smartform 时用来展示的内表数据。

#### Global Definitions

Global definitions包含可以在整个表单中使用的数据，可进行数据定义和初始化。

![smartforms](/images/ABAP/smartform5.png)

### Smartforms 程序调用

调用方式是通过函数`SSF_FUNCTION_MODULE_NAME` 将 form 生成一个函数，然后调用函数打印Smart Form 。

```JS
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

- 记录总页数：sfsy-page(当前页数) / sfsy-formpages(总页数)






