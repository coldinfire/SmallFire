---
title: "ABAP弹出框设置"
date: 2018-08-10
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils
  - ALV

---

### 可输入弹出框：POPUP_GET_VALUES_USER_HELP

```ABAP
"输入表格，SVAL相应的字段信息决定显示的效果："
  tabname  = 'AFKO'.
  fieldname = 'AUFNR'.
  fieldtext = '生产订单号'.
  field_attr = '02'.    "是否可输入和显示"
  value = 'val'.
CALL FUNCTION 'POPUP_GET_VALUES_USER_HELP'
  EXPORTING
  *   F1_FORMNAME     = ' '
  *   F1_PROGRAMNAME  = ' '
  *   F4_FORMNAME     = ' '
  *   F4_PROGRAMNAME  = ' '
  *   FORMNAME        = ' '
  popup_title     = 'BAIDUSAP.COM'
  *   PROGRAMNAME     = ' '
  *   START_COLUMN    = '5'
  *   START_ROW       = '5'
  *   NO_CHECK_FOR_FIXED_VALUES = ' '
  IMPORTING
    returncode      = l_ret
  TABLES
    fields          = git_tab
  EXCEPTIONS
    error_in_fields = 1
    OTHERS          = 2.
```

### 模仿系统标准弹出框：FB_MESSAGES_DISPLAY_POPUP

![弹出框](/images/ABAP/ABAP_Popup01.png)

```ABAP
DATA: 
  i_smesg TYPE tsmesg WITH HEADER LINE.
  i_smesg-msgty = 'E'.
  i_smesg-arbgb = '00'.
  i_smesg-txtnr = '001'.
  i_smesg-msgv1 = 'test1'.
  i_smesg-msgv2 = '箱码未扫描装车'.
  i_smesg-msgv3 = ''.
  APPEND i_smesg.
IF i_smesg[] IS NOT INITIAL.
  CLEAR i_smesg.
  i_smesg-msgty = 'E'.
  i_smesg-arbgb = '00'.
  i_smesg-txtnr = '001'.
  i_smesg-msgv1 = '下列箱码未扫描装车'.
  INSERT i_smesg INDEX 1.
  CALL FUNCTION 'FB_MESSAGES_DISPLAY_POPUP'
    EXPORTING
      it_smesg        = i_smesg[]
    EXCEPTIONS
      no_messages     = 1
      popup_cancelled = 2
      OTHERS          = 3.
  IF sy-subrc <> 0.
    RETURN.
  ENDIF.
ENDIF.
```

### 展示单列信息弹出框：POPUP_TO_DECIDE_LIST

![弹出框](/images/ABAP/ABAP_Popup02.png)

```ABAP
TABLES: spopli.
DATA: t_spop LIKE spopli OCCURS 0 WITH HEADER LINE. "定义供用户选择列表"
data: answer type string.              "用于存储用户选择"
REFRESH t_spop.
CLEAR t_spop. 
t_spop-selflag = 'X'.                  "设置选中"
t_spop-varoption = 'MBEW'.             "设置显示的文本" 
t_spop-inactive  = ''.                 "设置不可编辑"
APPEND t_spop.
CLEAR t_spop. 
t_spop-selflag = ''. 
t_spop-varoption = 'EKPO'. 
t_spop-inactive  = ''. 
APPEND t_spop.
CLEAR t_spop. 
t_spop-selflag = ''. 
t_spop-varoption = 'MSEG'. 
t_spop-inactive  = ''. 
APPEND t_spop.

CALL FUNCTION 'POPUP_TO_DECIDE_LIST' 
  EXPORTING 
    cursorline               = 1 
    mark_flag                = ' ' 
    mark_max                 = 1 
    start_col                = 20             "设置开始的列"
    start_row                = 7              "设置开始的行"
    textline1                = 'The order not release'        "设置文本行内容1"
*   TEXTLINE2                = ' ' 
*   TEXTLINE3                = ' ' 
    titel                    = 'Not realease order list'   "设置标题"
    DISPLAY_ONLY             = 'X' 
  IMPORTING 
    answer                   = answer         
    "获得用户选择,这里返回的值对应是当前列表NO，比如第一个就返回1，第二个返回2" 
  TABLES 
    t_spopli                 = t_spop         "设置选择列表" 
  EXCEPTIONS 
    NOT_ENOUGH_ANSWERS       = 1 
    TOO_MUCH_ANSWERS         = 2 
    TOO_MUCH_MARKS           = 3 
    OTHERS                   = 4 
    . 
IF sy-subrc <> 0. 
  MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
     WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
ENDIF. 
```

