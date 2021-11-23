---
title: " ALV 单元格编辑设置 "
date: 2018-08-03
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

单元格级别的可编辑和不可编辑是个非常有用的功能，在很多时候都会使用到。

### 实现 ALV 单元格不可编辑

如果已经把 ALV 中的整列设为可编辑，而只想让这个列中的某些单元格不可编辑，可以使用以下这种方法。

具体单元格可编辑状态设置的主要思想：

- 首先通过 EIDT 参数设置列为可编辑状态；
- 其次对输出内表进行循环将不需要编辑的行设置为不可编辑状态，如此单元格的可编辑属性设置完毕。

#### 必要条件

1.在输出内表中增加字段参考表类型是 `LVC_T_STYL`： `FIELD_STYLE TYPE LVC_T_STYL`。输入下面的参数值

- CL_GUI_ALV_GRID=>MC_STYLE_ENABLED：使字段可以编辑
- CL_GUI_ALV_GRID=>MC_STYLE_DISABLED使字段不可以编辑

2.需要告诉 layout 哪个字段是 style 字段：`layout-stylefname = FIELD_STYLE.`

3.把 style table 添加到显示表，并在 layout structure 中说明 style field。在 field catalog 中把相应的 EDIT 设为‘X’。

#### 实例代码

```ABAP
DATA: BEGIN OF ITAB OCCURS 0,
    ZXMDM TYPE CHAR1,
    ZQRFH_ICON TYPE STRING,
    ZLDATE TYPE ZLDATE,
    ZLUSR TYPE ZLUSR,
    ZLUSD TYPE ZLUSD,
    K TYPE STRING,
    FIELD_STYLE TYPE LVC_T_STYL, " 为内表添加设置编辑状态所需的字段 "
  END OF ITAB.
DATA: GT_FIELDCAT TYPE lvc_t_fcat,
      GS_FIELDCAT TYPE lvc_s_fcat,
      LAYOUT TYPE lvc_s_layo.
GS_FIELDCAT-FIELDNAME = 'ZBQ'. " 设置列可编辑 "
GS_FIELDCAT-EDIT = 'X'.
APPEND GS_FIELDCAT TO GT_FIELDCAT.
DATA STYLELIN TYPE LVC_S_STYL.
LOOP AT ITAB.
    IF ITAB-ZXMDM = 'D' OR ITAB-ZXMDM = 'F' OR ITAB-ZXMDM = 'H'.
      STYLELIN-FIELDNAME = 'ZLUSR'. " 需要编辑的列名 "
      STYLELIN-STYLE = CL_GUI_ALV_GRID=>MC_STYLE_DISABLED. " 设置为不可编辑状态 "
      APPEND STYLELIN TO ITAB-FIELD_STYLE.
      CLEAR STYLELIN.
      MODIFY ITAB.
    ELSE.
	  STYLELIN-FIELDNAME = 'ZLUSD'. " 需要编辑的列名"
	  STYLELIN-STYLE = CL_GUI_ALV_GRID=>MC_STYLE_ENABLED. " 设置为可编辑状态 "
	  APPEND STYLELIN TO ITAB-FIELD_STYLE.
      CLEAR STYLELIN.
      MODIFY ITAB.
    ENDIF.
ENDLOOP.
" 指定内表中的格式字段 "
LAYOUT-STYLE_FNAME = 'FIELD_STYLE'. 
" 调用函数 "
CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY_LVC'  
    EXPORTING
      IT_FIELDCAT_LVC    = GT_FIELDCAT
      IS_LAYOUT_LVC      = LAYOUT
    TABLES
      T_OUTTAB           = ITAB[]
    EXCEPTIONS
      PROGRAM_ERROR      = 1
      OTHERS             = 2.
```

### 单元格编辑模式切换

一般情况下，单元格的设置会覆盖整列的设置。如果想在程序里动态切换各种模式，只需要修改内表里关于 STYLE 设置的值然后刷新。使用方法 set_ready_for_input 可以使 ALV 在编辑和不可编辑模式之间切换。

```ABAP
FORM SWITCH_EDIT_MODE.
  IF GO_GRID->IS_READY_FOR_INPUT( ) = 0.
    CALL METHOD GO_GRID->SET_READY_FOR_INPUT
      EXPORTING
        I_READY_FOR_INPUT = 1. "可编辑"
  ELSE.
    CALL METHOD GO_GRID->SET_READY_FOR_INPUT
      EXPORTING
        I_READY_FOR_INPUT = 0. "不可编辑"
  ENDIF.
ENDFORM.                " SWITCH_EDIT_MODE "
```