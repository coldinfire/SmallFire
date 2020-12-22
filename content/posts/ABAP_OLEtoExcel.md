---
title: "OLE下载数据到EXCEL模板及速度优化"
date: 2020-03-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils
  - ALV

---

引用链接：[OLE 下载数据到EXCEL模板及速度优化](https://mp.weixin.qq.com/s/9_1gyAgRwgGa09dXID-_LA)



**问题描述:** 当项目中对导出数据EXCEL模板有要求时，ALV标准导出功能不能满足需要开发人员自定义下载数据程序。

步骤：

- 上传模板 tcode：SMW0
- 获取并展示数据
- 自定义按钮
- 将ALV数据下载到EXCEL模板中
- 下载速度的优化
- 程序源码（ZOLE_EXCEL）

#### 下载速度优化：

```JS
* 将内表数据先放到剪切板上，全量粘贴
DESCRIBE FIELD LW_ALV TYPE LV_TYPE COMPONENTS LV_RC.
  LOOP AT T_ALV INTO W_ALV.
    LW_ALV-LINE1 = W_ALV-MANDT.      
    LW_ALV-LINE2 = W_ALV-PERSNUMBER.    
    LW_ALV-LINE3 = W_ALV-NAME_LAST.    
    LW_ALV-LINE4 = W_ALV-NAME_TEXT.    
    LW_ALV-LINE5 = W_ALV-COMPANY.    
    CLEAR LW_CHAR-LINE.
    DO LV_RC TIMES.
     ASSIGN COMPONENT SY-INDEX OF STRUCTURE LW_ALV TO <LV_FIELD>.
     CONCATENATE LW_CHAR-LINE <LV_FIELD> CL_ABAP_CHAR_UTILITIES=>HORIZONTAL_TAB INTO LW_CHAR-LINE.
    ENDDO.
    APPEND LW_CHAR TO LT_CHAR.
  ENDLOOP.
* 将内表数据放到excel剪切板上
  CALL METHOD CL_GUI_FRONTEND_SERVICES=>CLIPBOARD_EXPORT
    IMPORTING
      DATA                 = LT_CHAR  "Data"
    CHANGING
      RC                   = LV_RC    "Return Code"
    EXCEPTIONS
      CNTL_ERROR           = 1
      ERROR_NO_GUI         = 2
      NOT_SUPPORTED_BY_GUI = 3
*     no_authority         = 4
      OTHERS               = 5.

  GET PROPERTY OF APPLICATION 'ACTIVECELL' = SHEET.
  CALL METHOD OF APPLICATION 'Worksheets' = SHEET
    EXPORTING
      #1 = 'Sheet1'.
  CALL METHOD OF SHEET 'Activate'.
* Select the cell A1
  CALL METHOD OF APPLICATION 'CELLS' = CELL
    EXPORTING
      #1 = 2  " i_row "
      #2 = 1. " i_col "

* Paste clipboard from cell
  CALL METHOD OF CELL 'SELECT'. " 选择单元格 "
  CALL METHOD OF SHEET 'Paste'. " 粘贴 "
  CALL METHOD OF APPLICATION 'CELLS' = CELL
    EXPORTING
     #1 = 2
     #2 = 1 .
  CALL METHOD OF CELL 'SELECT'. " 取消全选"
```



#### 程序源码

```JS
TABLES:ADRP.
" Declare Internal Table TYPE "
TYPES:BEGIN OF TP_ALV ,
       MANDT      TYPE ADRP-CLIENT,      "客户端"
       PERSNUMBER TYPE ADRP-PERSNUMBER,  "人员编号"
       NAME_LAST  TYPE ADRP-NAME_LAST,   "姓"
       NAME_TEXT  TYPE ADRP-NAME_TEXT,   "全名"
       COMPANY    TYPE CHAR50,           "公司"
      END OF TP_ALV.
" Declare Internal Table "
DATA T_ALV TYPE STANDARD TABLE OF TP_ALV.
DATA W_ALV TYPE TP_ALV.
DATA: W_LAYOUT TYPE LVC_S_LAYO,
      W_FCAT   TYPE LVC_S_FCAT,
      T_FCAT   TYPE LVC_T_FCAT.
DATA: APPLICATION TYPE OLE2_OBJECT,       "excel object"
      WORKBOOK    TYPE OLE2_OBJECT,       "excel workbook objcet"
      SHEET       TYPE OLE2_OBJECT,       "workbook sheet object"
      COLUMNS     TYPE OLE2_OBJECT,       "sheet col objcet"
      ROWS        TYPE OLE2_OBJECT,       "sheet row objcet"
      RANGE       TYPE OLE2_OBJECT,       "range"
      RANGE1      TYPE OLE2_OBJECT,       "range1"
      FONT        TYPE OLE2_OBJECT,       "font"
      CELL        TYPE OLE2_OBJECT,       "cell"
      CELL1       TYPE OLE2_OBJECT,       "cell1"
      SHEET1      TYPE OLE2_OBJECT,       "workbook sheet object"
      BORDERS     TYPE OLE2_OBJECT.       "borders"
      
" Constants: "
CONSTANTS LC_PF_STATUS TYPE SLIS_FORMNAME VALUE 'ALV_PF_STATUS' .       "alv 自定义按钮"
CONSTANTS LC_USER_COMMAND TYPE SLIS_FORMNAME VALUE 'ALV_USER_COMMAND' . "alv 自定义按钮响应事件"
CONSTANTS LC_COMPANY TYPE CHAR50 VALUE 'XXXXX科技有限公司'.

**---------Selection-Screen----------**
SELECTION-SCREEN BEGIN OF BLOCK B01 WITH FRAME TITLE TEXT-P01.
PARAMETERS: R_1 TYPE C RADIOBUTTON GROUP R1 DEFAULT 'X',    
            R_2 TYPE C RADIOBUTTON GROUP R1 .  
SELECTION-SCREEN END OF BLOCK B01.
**======================================================================**
**  Report Events*
INITIALIZATION.
AT SELECTION-SCREEN OUTPUT.             
AT SELECTION-SCREEN.
START-OF-SELECTION.
  SELECT * INTO CORRESPONDING FIELDS OF TABLE T_ALV FROM ADRP.
  LOOP AT T_ALV INTO W_ALV.
    W_ALV-MANDT = SY-MANDT.
    W_ALV-COMPANY = LC_COMPANY.
    MODIFY T_ALV FROM W_ALV.
    CLEAR W_ALV.
  ENDLOOP.
END-OF-SELECTION.

  DEFINE _APPEND_FCAT.
    CLEAR w_fcat.
    w_fcat-fieldname = &1.
    w_fcat-scrtext_l = &2.
**  ls_fcat-hotspot   = &3.*
    append  w_fcat to t_fcat.
  END-OF-DEFINITION.

  W_LAYOUT-ZEBRA = ABAP_TRUE.
  W_LAYOUT-CWIDTH_OPT  = ABAP_TRUE.
  _APPEND_FCAT: 'MANDT' '客户端',
                'PERSNUMBER' '人员编号' ,
                'NAME_LAST' '姓' ,
                'NAME_TEXT' '全名' ,
                'COMPANY' '公司'.

  CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY_LVC'
    EXPORTING
      I_CALLBACK_PROGRAM       = SY-REPID
      I_CALLBACK_PF_STATUS_SET = LC_PF_STATUS    " 自定义按钮GUI "
      I_CALLBACK_USER_COMMAND  = LC_USER_COMMAND " 自定义按钮响应事件 "
      IS_LAYOUT_LVC            = W_LAYOUT
      IT_FIELDCAT_LVC          = T_FCAT
    TABLES
      T_OUTTAB                 = T_ALV.

FORM ALV_PF_STATUS USING RT_EXTAB TYPE SLIS_T_EXTAB .
  SET PF-STATUS 'STANDARD_001'.
ENDFORM.

FORM ALV_USER_COMMAND USING R_UCOMM LIKE SY-UCOMM     "user_command"
                       RS_SELFIELD  TYPE SLIS_SELFIELD.
  DATA: LR_GRID TYPE REF TO CL_GUI_ALV_GRID.
  "将变更的数据刷新"
  CALL FUNCTION 'GET_GLOBALS_FROM_SLVC_FULLSCR'
    IMPORTING
      E_GRID = LR_GRID.
  CALL METHOD LR_GRID->CHECK_CHANGED_DATA.
  RS_SELFIELD-REFRESH = 'X'.
  
  CASE R_UCOMM.
    WHEN '&DOWNLOAD'.
     PERFORM FRM_DOWNLOAD_ALV.  "附加关税成本追加"
  ENDCASE.
ENDFORM.
**&---------------------------------------------------------------------**
**&      Form  FRM_DOWNLOAD_ALV*
**&---------------------------------------------------------------------**
FORM FRM_DOWNLOAD_ALV .
  DATA: LV_FILE    TYPE RLGRAP-FILENAME,
        LV_PATH    TYPE RLGRAP-FILENAME ,
        LV_ERROR   TYPE CHAR1,
        LV_PERCENT(5) TYPE P DECIMALS 2.
  "获取文件路径"
  PERFORM FRM_GET_FILE CHANGING LV_PATH LV_ERROR.
  CHECK LV_ERROR IS INITIAL.
  PERFORM FRM_PROCESS_INDCATOR USING '程序正在下载模板' 0 .
  LV_FILE = LV_PATH.
  CONCATENATE LV_FILE '员工信息表' SY-UZEIT '.xlsx' INTO LV_FILE.
  "下载模板"
  PERFORM FRM_DOWN_TEMPLATE USING LV_FILE CHANGING LV_ERROR.
  CHECK LV_ERROR IS INITIAL.
  "打开excel"
  PERFORM FRM_OPEN_EXCEL_HIDE USING LV_FILE 'X'.
  PERFORM FRM_PROCESS_INDCATOR USING '程序正在把数据写入到Excel' LV_PERCENT.
  IF R_1 = 'X'.
    PERFORM FRM_WRITE_DATA.
  ELSEIF R_2 = 'X'.
    PERFORM FRM_WRITE_DATA_YH .
  ENDIF.
  "关闭excel"
  PERFORM FRM_CLOSE_EXCEL USING LV_FILE.
  PERFORM FRM_FREE_OBJECT.
ENDFORM.                    " FRM_DOWNLOAD_ALV"
**&---------------------------------------------------------------------**
**&      Form  FRM_GET_FILE*
**&---------------------------------------------------------------------**
FORM FRM_GET_FILE  CHANGING OV_FILE OV_ERROR.
  DATA: LV_FOLDER  TYPE STRING.
  CALL METHOD CL_GUI_FRONTEND_SERVICES=>DIRECTORY_BROWSE
    EXPORTING
      WINDOW_TITLE         = '文件路径选择'
*     initial_folder       =
    CHANGING
      SELECTED_FOLDER      = LV_FOLDER
    EXCEPTIONS
      CNTL_ERROR           = 1
      ERROR_NO_GUI         = 2
      NOT_SUPPORTED_BY_GUI = 3
      OTHERS               = 4.
  IF SY-SUBRC <> 0.
    MESSAGE ID SY-MSGID TYPE 'S' NUMBER SY-MSGNO
      WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4 DISPLAY LIKE 'E'.
    OV_ERROR = 'X'.
  ELSEIF LV_FOLDER IS INITIAL.
    MESSAGE '文件下载取消！' TYPE 'S'.
    OV_ERROR = 'X'.
  ELSE.
    OV_FILE = LV_FOLDER.
  ENDIF.
ENDFORM.                    " FRM_GET_FILE "
**&---------------------------------------------------------------------**
**&      Form  FRM_PROCESS_INDCATOR*
**&---------------------------------------------------------------------**
FORM FRM_PROCESS_INDCATOR  USING TEXT PERCENTAGE.
  CALL FUNCTION 'SAPGUI_PROGRESS_INDICATOR'
    EXPORTING
      PERCENTAGE = PERCENTAGE
      TEXT       = TEXT.
ENDFORM.                    " FRM_PROCESS_INDCATOR "
**&---------------------------------------------------------------------**
**&      Form  FRM_DOWN_TEMPLATE*
**&---------------------------------------------------------------------**
FORM FRM_DOWN_TEMPLATE  USING    I_FILE TYPE RLGRAP-FILENAME
                        CHANGING OV_ERROR.
  DATA: LO_OBJDATA     LIKE WWWDATATAB,
        LO_MIME        LIKE W3MIME,
        LS_OBJNAM      TYPE STRING,
        LI_RC          LIKE SY-SUBRC,
        LS_ERRTXT      TYPE STRING,
        LV_OBJID       TYPE WWWDATA-OBJID.
  LV_OBJID  = 'ZOLE_EXCEL'.
  CONCATENATE LV_OBJID '.XLSX' INTO LS_OBJNAM.
  CONDENSE LS_OBJNAM NO-GAPS.
  SELECT SINGLE RELID OBJID FROM WWWDATA
    INTO CORRESPONDING FIELDS OF LO_OBJDATA
    WHERE SRTF2    = 0
      AND RELID    = 'MI'
      AND OBJID    = LV_OBJID.
  IF SY-SUBRC NE 0 OR LO_OBJDATA-OBJID EQ SPACE.
    MESSAGE '模板文件不存在，请用TCODE：SMW0进行加载!' TYPE 'E'.
    OV_ERROR = 'X'.
  ENDIF.
  CALL FUNCTION 'DOWNLOAD_WEB_OBJECT'
    EXPORTING
      KEY         = LO_OBJDATA
      DESTINATION = I_FILE
    IMPORTING
      RC          = LI_RC.
  IF LI_RC NE 0.
    MESSAGE '模板文件下载失败!' TYPE 'E'.
    OV_ERROR = 'X'.
  ENDIF.
ENDFORM.                    " FRM_DOWN_TEMPLATE "
**&---------------------------------------------------------------------**
**&      Form  FRM_OPEN_EXCEL_HIDE*
**&---------------------------------------------------------------------**
FORM FRM_OPEN_EXCEL_HIDE  USING    P_FILE
                                   P_HIDE.
  CREATE OBJECT APPLICATION 'EXCEL.APPLICATION'.
  IF P_HIDE IS NOT INITIAL.
    SET PROPERTY OF APPLICATION 'VISIBLE' = 0.
  ELSE.
    SET PROPERTY OF APPLICATION 'Visible' = 1.
  ENDIF.
  CALL METHOD OF APPLICATION 'Workbooks' = WORKBOOK.
  CALL METHOD OF WORKBOOK 'Open' = WORKBOOK
    EXPORTING
     \#1 = P_FILE.
ENDFORM.                    " FRM_OPEN_EXCEL_HIDE "
**&---------------------------------------------------------------------**
**&      Form  FRM_WRITE_DATA*
**&---------------------------------------------------------------------**
FORM FRM_WRITE_DATA .
  DATA L_COUNT TYPE I VALUE '1'.
  LOOP AT T_ALV INTO W_ALV .
    L_COUNT = L_COUNT + 1.
    PERFORM FRM_FILL_CELL USING L_COUNT 1 W_ALV-MANDT SPACE.
    PERFORM FRM_FILL_CELL USING L_COUNT 2 W_ALV-PERSNUMBER SPACE.
    PERFORM FRM_FILL_CELL USING L_COUNT 3 W_ALV-NAME_LAST SPACE.
    PERFORM FRM_FILL_CELL USING L_COUNT 4 W_ALV-NAME_TEXT SPACE.
    PERFORM FRM_FILL_CELL USING L_COUNT 5 W_ALV-COMPANY SPACE.
  ENDLOOP.
  CLEAR L_COUNT.
ENDFORM.                    " FRM_WRITE_DATA "
**&---------------------------------------------------------------------**
**&      Form  FRM_FILL_CELL*
**&---------------------------------------------------------------------**
**       text*
**----------------------------------------------------------------------**
**      -->P_2      text*
**      -->P_1      text*
**      -->P_L_HEADDATA  text*
**      -->P_SPACE  text*
**----------------------------------------------------------------------**
FORM FRM_FILL_CELL  USING    I_ROW I_COL I_VALUE P_FLAG.
  CALL METHOD OF APPLICATION 'CELLS' = CELL
    EXPORTING
     \#1 = I_ROW
     \#2 = I_COL.
  SET PROPERTY OF CELL 'VALUE' = I_VALUE.
  IF P_FLAG = ''.
*    SET PROPERTY OF cell 'HORIZONTALALIGNMENT' = 2.*
* ELSEIF P_FLAG = cns_C.*
*    SET PROPERTY OF cell 'HORIZONTALALIGNMENT' = 3.*
  ELSEIF P_FLAG = 'Z'.
    SET PROPERTY OF CELL 'HORIZONTALALIGNMENT' = -4108.
  ENDIF.
ENDFORM.                    " FRM_FILL_CELL "
**&---------------------------------------------------------------------**
**&      Form  FRM_CLOSE_EXCEL*
**&---------------------------------------------------------------------**
FORM FRM_CLOSE_EXCEL USING P_FILE.
  GET PROPERTY OF APPLICATION 'ActiveWorkbook' = WORKBOOK.
  CALL METHOD OF WORKBOOK 'SAVE'.
  CALL METHOD OF WORKBOOK 'ClOSE'.
*    EXPORTING*
*    #1 = 1.*
  CALL METHOD OF WORKBOOK 'QUIT'.
ENDFORM.                      " FRM_CLOSE_EXCEL "
**&---------------------------------------------------------------------**
**&      Form  FRM_FREE_OBJECT*
**&---------------------------------------------------------------------**
FORM FRM_FREE_OBJECT .
  FREE OBJECT FONT.
  FREE OBJECT RANGE.
  FREE OBJECT RANGE1.
  FREE OBJECT COLUMNS.
  FREE OBJECT ROWS.
  FREE OBJECT CELL.
  FREE OBJECT CELL1.
  FREE OBJECT SHEET1.
  FREE OBJECT SHEET.
  FREE OBJECT WORKBOOK.
  FREE OBJECT APPLICATION.
ENDFORM.                    " FRM_FREE_OBJECT "
**&---------------------------------------------------------------------**
**&      Form  FRM_WRITE_DATA_YH*
**&---------------------------------------------------------------------**
FORM FRM_WRITE_DATA_YH .
  DATA: BEGIN OF LW_CHAR,
    LINE(2200) TYPE C,
  END OF LW_CHAR.
  DATA LT_CHAR LIKE TABLE OF LW_CHAR. " 将数据拷贝到剪切板上 "
  DATA: BEGIN OF LW_ALV ,
           LINE1(200) TYPE C,    
           LINE2(200) TYPE C,    
           LINE3(200) TYPE C,    
           LINE4(200) TYPE C,    
           LINE5(200) TYPE C,    
        END OF LW_ALV.
  DATA LV_RC TYPE I.
  DATA LV_TYPE TYPE C.
  FIELD-SYMBOLS <LV_FIELD>.
  DESCRIBE FIELD LW_ALV TYPE LV_TYPE COMPONENTS LV_RC.
  LOOP AT T_ALV INTO W_ALV.
    LW_ALV-LINE1 = W_ALV-MANDT.      
    LW_ALV-LINE2 = W_ALV-PERSNUMBER. 
    LW_ALV-LINE3 = W_ALV-NAME_LAST.  
    LW_ALV-LINE4 = W_ALV-NAME_TEXT.  
    LW_ALV-LINE5 = W_ALV-COMPANY.    
    CLEAR LW_CHAR-LINE.
    DO LV_RC TIMES.
     ASSIGN COMPONENT SY-INDEX OF STRUCTURE LW_ALV TO <LV_FIELD>.
     CONCATENATE LW_CHAR-LINE <LV_FIELD> CL_ABAP_CHAR_UTILITIES=>HORIZONTAL_TAB INTO LW_CHAR-LINE.
    ENDDO.
    APPEND LW_CHAR TO LT_CHAR.
  ENDLOOP.
* 将内表数据放到Excel剪切板上 *
  CALL METHOD CL_GUI_FRONTEND_SERVICES=>CLIPBOARD_EXPORT
    IMPORTING
      DATA                 = LT_CHAR   " Data "
    CHANGING
      RC                   = LV_RC     " Return Code "
    EXCEPTIONS
      CNTL_ERROR           = 1
      ERROR_NO_GUI         = 2
      NOT_SUPPORTED_BY_GUI = 3
**     no_authority         = 4*
      OTHERS               = 5.
  GET PROPERTY OF APPLICATION 'ACTIVECELL' = SHEET.
  CALL METHOD OF APPLICATION 'Worksheets' = SHEET
    EXPORTING
     \#1 = 'Sheet1'.
  CALL METHOD OF SHEET 'Activate'.
* Select the cell A1 *
  CALL METHOD OF APPLICATION 'CELLS' = CELL
    EXPORTING
      \#1 = 2   " i_row "
      \#2 = 1 . " i_col "
* Paste clipboard from cell *
  CALL METHOD OF CELL 'SELECT'. "选择单元格"
  CALL METHOD OF SHEET 'Paste'. "粘贴"
  CALL METHOD OF APPLICATION 'CELLS' = CELL
    EXPORTING
    \#1 = 2
    \#2 = 1 .
  CALL METHOD OF CELL 'SELECT'. "取消全选"
ENDFORM.                    " FRM_WRITE_DATA_YH" 
```

