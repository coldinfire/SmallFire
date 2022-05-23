---
title: " ABAP 弹出框设置 "
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

在对话框中列出以选择一个或多个条目（或仅显示）弹出的 ALV 。

#### 输入参数

| Importing               | Description                                 |
| :---------------------- | :------------------------------------------ |
| I_TITLE                 | Dialog box title                            |
| I_SELECTION             | X : Selection possible；space : Display     |
| I_ALLOW_NO_SELECTION    | Allow copy although nothing is selected     |
| I_ZEBRA                 | Line output with alternating color          |
| I_SCREEN_START_COLUMN   | Coordinates for list in dialog box          |
| I_SCREEN_START_LINE     | Coordinates for list in dialog box          |
| I_SCREEN_END_COLUMN     | Coordinates for list in dialog box          |
| I_SCREEN_END_LINE       | Coordinates for list in dialog box          |
| I_CHECKBOX_FIELDNAME    | Output table checkbox field name            |
| I_LINEMARK_FIELDNAME    | Line selection color information field name |
| I_SCROLL_TO_SEL_LINE    | Scroll to default selection if necessary    |
| I_TABNAME               | Table name with chosen values               |
| I_STRUCTURE_NAME        | Internal output table structure name        |
| IT_FIELDCAT             | Field catalog with field descriptions       |
| IT_EXCLUDING            | Table of inactive function codes            |
| I_CALLBACK_PROGRAM      | Name of the calling program                 |
| I_CALLBACK_USER_COMMAND | USER_COMMAND handling form routine name     |

输出参数 & TABLE

| Parameter   | Description                           |
| ----------- | ------------------------------------- |
| ES_SELFIELD | 包含弹出 ALV 中简单选择的信息         |
| E_EXIT      | 当用户取消操作时，此字段设置为 “X”    |
| T_OUTTAB    | 包含要在弹出窗口中的 ALV 中显示的数据 |

#### Sample

```ABAP
REPORT  zpopup_sample.
TYPE-POOLS: slis.
DATA: gt_outtab TYPE sflight OCCURS 0,
      gs_private TYPE slis_data_caller_exit,
      gs_selfield TYPE slis_selfield,
      gt_fieldcat TYPE slis_t_fieldcat_alv WITH HEADER LINE,
      g_exit(1) TYPE c.
PARAMETERS: p_title TYPE sy-title.
START-OF-SELECTION.
  SELECT * FROM sflight INTO TABLE gt_outtab UP TO 5 ROWS.
  CALL FUNCTION 'REUSE_ALV_FIELDCATALOG_MERGE'
    EXPORTING
      i_structure_name = 'SFLIGHT'
    CHANGING
      ct_fieldcat      = gt_fieldcat[].
  READ TABLE gt_fieldcat WITH KEY fieldname = 'PLANETYPE'.
  IF sy-subrc = 0.
    gt_fieldcat-no_out = 'X'.
    MODIFY gt_fieldcat INDEX sy-tabix.
  ENDIF.
  CALL FUNCTION 'REUSE_ALV_POPUP_TO_SELECT'
    EXPORTING
      i_title                 = p_title
      i_selection             = 'X'
      i_zebra                 = 'X'
      i_screen_start_column   = 10
      i_screen_start_line     = 3
      i_screen_end_column     = 100
      i_screen_end_line       = 10
*     I_CHECKBOX_FIELDNAME    =
*     I_LINEMARK_FIELDNAME    =
      i_scroll_to_sel_line    = 'X'
      i_tabname               = '1'
      it_fieldcat             = gt_fieldcat[]
*     IT_EXCLUDING            =
      i_callback_program      = sy-repid
*     I_CALLBACK_USER_COMMAND =
      is_private              = gs_private
    IMPORTING
      es_selfield             = gs_selfield
      e_exit                  = g_exit
    TABLES
      t_outtab                = gt_outtab
    EXCEPTIONS
      program_error           = 1
      OTHERS                  = 2.
  IF sy-subrc <> 0.
    MESSAGE i000(0k) WITH sy-subrc.
  ENDIF.

  WRITE: / g_exit,
  gs_selfield-tabname,
  gs_selfield-tabindex.
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

