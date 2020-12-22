---
title: "Smartforms"
date: 2018-07-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---

### 查找Smartforms

TCode： **NACE** 可以查找 (例如。采购订单，销售订单等)

- NACE 是用于链接应用程序类型，输出类型及其处理例程（如驱动程序和附加的脚本表单或 Smartforms）的 Tcode。

Table： **TNAPR** 根据smartform名字查找对应的smartform程序

### 创建

- TCode : smartforms

#### Smartforms组成

Smartforms 包括页格式+FORM 内容，一般Form里包含表头、表身、表尾；可以单页，也可以有多页。

调用方式是通过函数`SSF_FUNCTION_MODULE_NAME` 将 form 生成一个函数，然后调用函数打印Smart Form 。

```JS
DATA: lc_fmnam       TYPE rs38l_fnam,
      lw_control     TYPE ssfctrlop,
      lw_options     TYPE ssfcompop,	
CLEAR: lc_fmnam.
"根据Form name将Form生成一个函数"
CALL FUNCTION 'SSF_FUNCTION_MODULE_NAME'
  EXPORTING
    formname           = 'Z_FORMNAME'
  IMPORTING
    fm_name            = lc_fmnam
  EXCEPTIONS
    no_form            = 1
    no_function_module = 2
    OTHERS             = 3.
IF sy-subrc <> 0.
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
  WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
ENDIF.
"控制参数设置"
lw_control-no_dialog = 'X'.
lw_options-tddest = 'LOCL'.
lw_options-tdimmed = 'X'.
lw_options-tdpageslct = '1-3'. "1,2"
"调用打印服务"
CALL FUNCTION lc_fmnam
  EXPORTING
    control_parameters = lw_control
    output_options     = lw_options
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

#### Smartforms使用

在 SAP 的 ABAP 编程中，一般开发过程都是在 Report 程序中取出所有需要的数据，将数据进行相应的处理以后保存到输出内表中，再打印内表中的数据。

但是 SmartForms 是一个独立的外部 Function Module，对于程序内部定义的内表数据不能直接传递，需要定义外部的数据结构 Structure 或者使用标准的表结构，如果程序变更，需要传递的数据发生变化，那么该 Sturcture 也需要修改，这是 SmartForms 中不方便的地方。

我们也可以在 SmartForms 内部写取数据的逻辑，但是在 SmartForms 中编程不是很方便，而且有时我们的数据需要首先以 List 或者 ALV List 的方式显示，然后再打印，所以在 smartforms 中书写取数据逻辑只能对一些要求非常简单的场合适用。

Smartforms的执行顺序是根据右边菜单从上到下执行的。




