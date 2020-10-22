---
title: "SAP Excel 上传和下载"
date: 2018-11-13
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - Excel
  - abaputils

---

## SAP Excel模板操作
**上传模板**：文档是通过**SMW0**上传的。

- 选择模板类型

  ![SMW0](/images/ABAP/ABAP_SMW0_1.png)

- 根据Package和Name查找模板

  ![SMW0](/images/ABAP/ABAP_SMW0_2.png)

- 编辑模板

  ![SMW0](/images/ABAP/ABAP_SMW0_3.png)

- 创建模板，维护名称和描述后从本地选择文件上传

  ![SMW0](/images/ABAP/ABAP_SMW0_4.png)

**下载模板**：调用METHOD / BAPI下载服务器上的模板文件到本地

### 使用类操作文件

**CL_GUI_FRONTEND_SERVICES**：该类提供了大量对操作系统文件的操作，如拷贝、列出文件名、打开文件、下载文件等。

- FILE_OPEN_DIALOG：显示文件打开对话框

- FILE_SAVE_DIALOG：显示文件保存对话框
- GUI_DOWNLOAD：下载文本文件到本地PC
-  GUI_UPLOAD：从本地文本文件读取数据

### 使用BAPI操作文件

```html
1. F4获取文件路径
  KD_GET_FILENAME_ON_F4：打开选择框，并获取本地文件路径
  WS_FILENAME_GET：打开选择框，并获取本地文件路径
2. 判断文件是否存在
  CALL FUNCTION 'WS_QUERY'：本地文件是否存在
  CALL FUNCTION 'WWWDATA_IMPORT'：判断服务器是否存在该模板,并返回模板数据
3. 内表数据下载到文件:
  CALL FUNCTION 'DOWNLOAD'：提示保存
  CALL FUNCTION 'WS_DOWNLOAD'：不提示直接保存
  CALL FUNCTION 'DOWNLOAD_WEB_OBJECT'：提示保存
4. 文件数据读取到内表
  CALL FUNCTION 'UPLOAD'：提示读入内表
  CALL FUNCTION 'WS_UPLOAD'：不提示直接读入内表
  CALL FUNCTION 'GUI_UPLOAD'：读取Txt模板文件
```

### 上传文件并转换为内表

#### GUI输入框选择文件(F4)

```HTML
TABLES: sscrfields.
TYPE-POOLS: slis.
SELECTION-SCREEN BEGIN OF BLOCK file_name WITH FRAME TITLE text-000.
SELECTION-SCREEN BEGIN OF LINE.
SELECTION-SCREEN COMMENT 1(31) text-001 FOR FIELD p_file.
PARAMETERS: p_file LIKE rlgrap-filename .
SELECTION-SCREEN END OF LINE.
SELECTION-SCREEN END OF BLOCK file_name.

AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_file.
    PERFORM frm_get_filename CHANGING p_file.

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

" Method 2 "
FORM frm_get_filename  CHANGING cv_file.
  DATA: lt_file TYPE filetable,
        lv_rc   TYPE i.
  CALL METHOD CL_GUI_FRONTEND_SERVICES=>FILE_OPEN_DIALOG  
    EXPORTING
      window_title            = '选择上传文件'
      default_extension       = '*.XLS'
      file_filter             = 'All Files (*.*)|*.*|NotePad Files(*.txt)|*.txt
                                |Excel Files(*.xls)|*.xls|Word files(*.doc)|*.doc'
      "INITIAL_DIRECTORY = 'C:/'  "初始化的目录"
      "MULTISELECTION = 'X'       "是否可以同时打开多个文件"
    CHANGING
      file_table              = lt_file  "你打开文件名的列表"
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

" METHOD 3 "
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
```

#### 判断本地文件是否存在

```html
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

#### 调用METHOD 读取文件内容到内表

```html
CALL FUNCTION 'GUI_UPLOAD'
   EXPORTING
    FILENAME            = LV_FILENAME  "要读取的文件"
    FILETYPE            = 'ASC'
    HAS_FIELD_SEPARATOR = CL_ABAP_CHAR_UTILITIES=>HORIZONTAL_TAB "字段间按TAB键分隔开來"
  TABLES
    DATA_TAB            = TXT_READ_DATA  "写入相应的內表中"
    EXCEPTIONS
    FILE_OPEN_ERROR     = 1
    FILE_READ_ERROR     = 2
```

### 获取Excel数据

```JS
1. 获取EXCEL内容 
  CALL FUNCTION 'ALSM_EXCEL_TO_INTERNAL_TABLE'
    EXPORTING
      FILENAME    = P_FILE
      I_BEGIN_COL = '1'
      I_BEGIN_ROW = '2'
      I_END_COL   = '300'
      I_END_ROW   = '65535'
    TABLES
      INTERN      = GT_EXCEL_T.
2. 获取EXCEL行，列，值进行数据处理
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
   *    GW_EXCEL-NORBM = GS_EXCEL_T-VALUE.
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

#### 获取Excel数据函数：

**TEXT_CONVERT_XLS_TO_SAP** ：这个函数直接可以把 execl 的内容原原本本的写入到内表，不用格式转化那么麻烦。如果该内表 ITAB 的数据最后要写入你的自建表里，那么还得迂回一下，因为透明表里有个 MANDT 客户端字段。所以得再建一个内表来迂回。

**1.EXCEL 中第一行是标题，调用 FM 时，参数 I_LINE_HEADER=’X’**

读第一条数据时，会把标题放入 “值是空的字段”

**2. 模板中前两行是标题，调用 FM 时，参数 I_LINE_HEADER=”，然后再把前两行数据删除**

100*N+1 行，有把标题放入 “值是空的字段” 的情况

```JS
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

- [通过调用BAPI实现]()
- [通过CLASS实现]()


