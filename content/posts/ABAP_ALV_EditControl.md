---
title: "ALV 控制单元格不可编辑"
date: 2018-08-03
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

### 实现 ALV 单元格不可编辑

如果已经把ALV中的整列设为可编辑，而只想让这个列中的某些单元格不可编辑，可以使用以下这种方法。

具体单元格可编辑状态设置的主要思想：

- 首先通过 EIDT 参数设置列为可编辑状态；
- 其次对输出内表进行循环将不需要编辑的行设置为不可编辑状态，如此单元格的可编辑属性设置完毕。

需要告诉 layout 哪个字段是 style 字段：`layout-stylefname = ‘CELLSTYLES’.`

#### 必要条件

1. 在输出内表中增加字段 `FIELD_STYLE TYPE LVC_T_STYL`
2. 设置 `STYLE_FNAME = 'FIELD_STYLE'`
3. 把 style table 添加到显示表，并在 layout structure 中说明 style field。在 field catalog 中把相应的 EDIT 设为‘X’。

#### 实例代码

```JS
DATA: BEGIN OF ITAB OCCURS 0,
      ZQRFH_ICON TYPE STRING,
      ZLDATE TYPE ZLDATE,
      ZLUSR TYPE ZLUSR,
      K TYPE STRING,
      FIELD_STYLE TYPE LVC_T_STYL, " 为内表添加设置编辑状态所需的字段 "
      END OF ITAB.
DATA: T_FIELDCAT TYPE lvc_t_fcat,
      S_FIELDCAT TYPE lvc_s_fcat,
      X_LAYOUT TYPE lvc_s_layo.
      
S_FIELDCAT-FIELDNAME = 'ZBQ'. " 设置列可编辑 "
S_FIELDCAT-EDIT = 'X'.
APPEND S_FIELDCAT TO T_FIELDCAT.
DATA STYLELIN TYPE LVC_S_STYL.
LOOP AT ITAB.
    IF ITAB-ZXMDM = 'D' OR ITAB-ZXMDM = 'F' OR ITAB-ZXMDM = 'H'.
      STYLELIN-FIELDNAME = 'ZBQFS'. " 需要编辑的列名 "
      STYLELIN-STYLE = CL_GUI_ALV_GRID=>MC_STYLE_DISABLED. " 设置为不可编辑状态 "
      APPEND STYLELIN TO ITAB-FIELD_STYLE.
      CLEAR STYLELIN.
      MODIFY ITAB.
    ELSE.
	  STYLELIN-FIELDNAME = 'ZBQFS'. " 需要编辑的列名"
	  STYLELIN-STYLE = CL_GUI_ALV_GRID=>MC_STYLE_ENABLED. " 设置为可编辑状态 "
	  APPEND STYLELIN TO ITAB-FIELD_STYLE.
      CLEAR STYLELIN.
      MODIFY ITAB.
    ENDIF.
endloop.
X_LAYOUT-STYLE_FNAME = 'FIELD_STYLE'. " 将内表中的字段名存入显示格式 "
CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY_LVC'  " 调用函数 "
    EXPORTING
      IT_FIELDCAT_LVC    = T_FIELDCAT
      IS_LAYOUT_LVC      = X_LAYOUT
    TABLES
      T_OUTTAB           = ITAB
    EXCEPTIONS
      PROGRAM_ERROR      = 1
      OTHERS             = 2.
```




