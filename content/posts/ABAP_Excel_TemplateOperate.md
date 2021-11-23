---
title: " SAP 操作Excel文件 "
date: 2018-11-13
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
  - abaputils

---

### 操作 EXCEL 文件方式

#### 使用类操作文件

*CL_GUI_FRONTEND_SERVICES*：该类提供了大量对操作系统文件的操作，如拷贝、列出文件名、打开文件、下载文件等。

- FILE_OPEN_DIALOG：显示文件打开对话框

- FILE_SAVE_DIALOG：显示文件保存对话框
- GUI_DOWNLOAD：下载文本文件到本地PC
-  GUI_UPLOAD：从本地文本文件读取数据

#### 使用 BAPI 操作文件

F4 获取文件路径

- KD_GET_FILENAME_ON_F4：打开选择框，并获取本地文件路径
- WS_FILENAME_GET：打开选择框，并获取本地文件路径

判断文件是否存在

- CALL FUNCTION 'WS_QUERY'：判断本地文件是否存在，并返回文件名
- CALL FUNCTION 'WWWDATA_IMPORT'：判断服务器是否存在该模板，并返回模板数据

内表数据下载到文件

- CALL FUNCTION 'DOWNLOAD'：提示保存
- CALL FUNCTION 'WS_DOWNLOAD'：不提示直接保存

文件数据读取到内表

- CALL FUNCTION 'UPLOAD'：提示读入内表
- CALL FUNCTION 'WS_UPLOAD'：不提示直接读入内表
- CALL FUNCTION 'GUI_UPLOAD'：读取 txt 模板文件

### 读取本地文件并转换为内表

#### GUI 输入框选择文件 (F4)

```ABAP
TABLES: sscrfields.
TYPE-POOLS: slis.
SELECTION-SCREEN BEGIN OF BLOCK blk1 WITH FRAME TITLE text-001.
SELECTION-SCREEN BEGIN OF LINE.
SELECTION-SCREEN COMMENT 1(31) text-001 FOR FIELD p_file.
PARAMETERS: p_file LIKE rlgrap-filename .
SELECTION-SCREEN END OF LINE.
SELECTION-SCREEN END OF BLOCK blk1.
AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_file.
  PERFORM frm_get_filename CHANGING p_file.
  IF cv_file IS INITIAL.
    MESSAGE 'Please input file!' TYPE 'I'.
    STOP.
    SET CURSOR FIELD p_file.
  ENDIF.
" Method 1 "
FORM frm_get_filename CHANGING cv_file.
  CALL FUNCTION 'KD_GET_FILENAME_ON_F4'
    CHANGING
      file_name = cv_file.
  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
    WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
    EXIT.
  ENDIF.
ENDFORM.
" METHOD 2 "
FORM frm_get_filename CHANGING cv_file.
  CALL FUNCTION 'WS_FILENAME_GET'
   EXPORTING
     mask                   = ',*.xlsx,*.XLSX,*.xls,*.XLS.'
     mode                   = 'O'
     title                  = 'Please check the input file local path'
   IMPORTING
     filename               = cv_file
*     RC                     =
   EXCEPTIONS
     inv_winsys             = 1
     no_batch               = 2
     selection_cancel       = 3
     selection_error        = 4
     OTHERS                 = 5.
  IF sy-subrc <> 0.
*    MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
*      WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
  ENDIF.
ENDFORM. 
" Method 3 "
FORM frm_get_filename  CHANGING cv_file.
  DATA: lt_file TYPE filetable,
        lv_rc   TYPE i.
  CALL METHOD CL_GUI_FRONTEND_SERVICES=>FILE_OPEN_DIALOG  
    EXPORTING
      window_title            = '选择上传文件'
      default_extension       = '*.XLS,*.XLSX'
      file_filter             = 'All Files (*.*)|*.*|NotePad Files(*.txt)|*.txt
                                |Excel Files(*.xls)|*.xls|Excel Files(*.xlsx)|*.xlsx
                                |Word files(*.doc)|*.doc'
      "INITIAL_DIRECTORY = 'C:/'  "初始化的目录"
      "MULTISELECTION = 'X'       "是否可以同时打开多个文件"
    CHANGING
      file_table              = lt_file  "打开文件名的列表"
      rc                      = lv_rc    "返回打开文件的数量"
    EXCEPTIONS
      file_open_dialog_failed = 1
      cntl_error              = 2
      error_no_gui            = 3
      not_supported_by_gui    = 4
      OTHERS                  = 5.
  IF sy-subrc EQ 0 AND lv_rc EQ 1.
    READ TABLE lt_file INDEX 1 INTO cv_file.
  ENDIF.
ENDFORM.                    " FRM_GET_FILENAME "
```

#### 判断本地文件是否存在

```ABAP
DATA l_file_exist TYPE c.
CLEAR l_file_exist.
CALL FUNCTION 'WS_QUERY'
  EXPORTING
    filename = p_file
    query    = 'FE'
  IMPORTING
    return   = l_file_exist.
IF sy-subrc <> 0.
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
  WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
ENDIF.
IF l_file_exist <> 1 OR l_file_exist IS INITIAL.
  MESSAGE 'File not found' TYPE 'E'.
ENDIF.
```

#### 调用 METHOD 读取文件内容到内表

```ABAP
CALL FUNCTION 'GUI_UPLOAD'
  EXPORTING
    FILENAME            = p_file  "要读取的文件"
    FILETYPE            = 'ASC'
    HAS_FIELD_SEPARATOR = CL_ABAP_CHAR_UTILITIES=>HORIZONTAL_TAB "字段间按TAB键分隔开來"
  TABLES
    DATA_TAB            = TXT_READ_DATA  "写入相应的內表中"
  EXCEPTIONS
    FILE_OPEN_ERROR     = 1
    FILE_READ_ERROR     = 2.
```

### 获取 Excel 数据并转换成内表

#### 获取EXCEL内容

```ABAP
DATA: g_it_excel TYPE TABLE OF alsmex_tabline.
CLEAR:g_it_excel,g_it_excel[].
CALL FUNCTION 'ALSM_EXCEL_TO_INTERNAL_TABLE'
  EXPORTING
    filename                = p_file
    i_begin_col             = '1'
    i_begin_row             = '2'
    i_end_col               = '100'
    i_end_row               = '65535'
  TABLES
    intern                  = g_it_excel.
  EXCEPTIONS
    inconsistent_parameters = 1
    upload_ole              = 2
    OTHERS                  = 3.
  IF sy-subrc <> 0.
* MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
*         WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
  ENDIF.
```

#### 获取EXCEL行、列，值进行数据处理

```ABAP
LOOP AT GT_EXCEL_T INTO GS_EXCEL_T.
  AT NEW ROW.
    CLEAR:GW_EXCEL.
  ENDAT.
  CASE GS_EXCEL_T-COL.
    WHEN 1.
      GW_EXCEL-LIFNR = GS_EXCEL_T-VALUE.
      CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
        EXPORTING
          INPUT  = GW_EXCEL-LIFNR
        IMPORTING
          OUTPUT = GW_EXCEL-LIFNR.
    WHEN 2.
      GW_EXCEL-MATNR = GS_EXCEL_T-VALUE.
      CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
        EXPORTING
          INPUT  = GW_EXCEL-MATNR
          IMPORTING
             OUTPUT = GW_EXCEL-MATNR.
    WHEN 3.
      GW_EXCEL-EKORG = GS_EXCEL_T-VALUE.
    WHEN 4.
      GW_EXCEL-WERKS = GS_EXCEL_T-VALUE.
    WHEN 5.
      GW_EXCEL-NETPR = GS_EXCEL_T-VALUE.
    WHEN 6.
      GW_EXCEL-KPEIN = GS_EXCEL_T-VALUE.
    WHEN 7.
      GW_EXCEL-LIFAB = GS_EXCEL_T-VALUE.
    WHEN 8.
   *  GW_EXCEL-NORBM = GS_EXCEL_T-VALUE.
      GW_EXCEL-LIFBI = GS_EXCEL_T-VALUE.
    WHEN 9.
      GW_EXCEL-MWSKZ = GS_EXCEL_T-VALUE.
    WHEN OTHERS.
  ENDCASE.
  AT END OF ROW.
    APPEND GW_EXCEL TO GT_EXCEL.
  ENDAT.
ENDLOOP.
```

#### 获取 Excel 数据函数

**TEXT_CONVERT_XLS_TO_SAP** ：这个函数直接可以把 execl 的内容原原本本的写入到内表，不用格式转化那么麻烦。如果该内表 ITAB 的数据最后要写入你的自建表里，那么还得迂回一下，因为透明表里有个 MANDT 客户端字段。所以得再建一个内表来迂回。

注意事项

- EXCEL 中第一行是标题，调用 FM 时，参数 I_LINE_HEADER=’X’；读第一条数据时，会把标题放入 “值是空的字段”

- 模板中前两行是标题，调用 FM 时，参数 I_LINE_HEADER=‘’，然后再把前两行数据删除；100*N+1 行，有把标题放入 “值是空的字段” 的情况


```ABAP
* 定义一个内表来存储数据，内表的列数和要传得数据的列数要相同，其按照列来匹配传值
DATA: BEGIN OF gt_data OCCURS 0,
     col1 TYPE char10,
     col2 TYPE char10,
  END OF gt_data.
CALL FUNCTION 'TEXT_CONVERT_XLS_TO_SAP'  
  EXPORTING  
*   I_FIELD_SEPERATOR          =  
    I_LINE_HEADER               = 'X'  
    i_tab_raw_data             = IT_RAW  
    i_filename                 = fname1  
  tables  
    i_tab_converted_data       = gt_data  
  EXCEPTIONS  
    CONVERSION_FAILED          = 1  
    OTHERS                     = 2  .  
IF sy-subrc <> 0.  
  MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO  
      WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.  
ENDIF.  
```

###  程序实例

- [通过调用BAPI实现](https://coldinfire.github.io/2018/ABAP_EXCEL_BAPI/)
- [通过CLASS实现](https://coldinfire.github.io/2018/ABAP_EXCEL_CLASS/)


