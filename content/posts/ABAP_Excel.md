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
1. 判断文件是否存在
  CALL FUNCTION 'WS_QUERY'：本地文件是否存在
  CALL FUNCTION 'WWWDATA_IMPORT'：判断服务器是否存在该模板
2. 内表数据下载到文件:
  CALL FUNCTION 'DOWNLOAD'：提示保存
  CALL FUNCTION 'WS_DOWNLOAD'：不提示直接保存
  CALL FUNCTION 'DOWNLOAD_WEB_OBJECT'：提示保存
3. 文件数据读取到内表
  CALL FUNCTION 'UPLOAD'：提示读入内表
  CALL FUNCTION 'WS_UPLOAD'：不提示直接读入内表
```

### 下载模板

```html
FORM frm_download_file .
  DATA: ls_filekey  TYPE wwwdatatab,
        lt_mime     TYPE STANDARD TABLE OF w3mime,
        lt_param    TYPE STANDARD TABLE OF wwwparams,
        ls_param    TYPE wwwparams,
        lv_filesize TYPE i,
        lv_code     TYPE i.
  DATA: lv_filefilter       TYPE string,
        lv_default_filename TYPE string,
        lv_filename         TYPE string,
        lv_path             TYPE string,
        lv_fullpath         TYPE string,
        lv_user_action      TYPE i.

  ls_filekey-relid = 'MI'.
  ls_filekey-objid = 'ZTEST_TEMPLATE'.
  " 判断是否存在该模板 "
  CALL FUNCTION 'WWWDATA_IMPORT'   
    EXPORTING
      key               = ls_filekey
    TABLES
      mime              = lt_mime
    EXCEPTIONS
      wrong_object_type = 1
      import_error      = 2
      OTHERS            = 3.
  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
    WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
    LEAVE SCREEN.
  ENDIF.

  SELECT * FROM wwwparams
    INTO CORRESPONDING FIELDS OF TABLE lt_param
    WHERE relid = ls_filekey-relid
    AND objid = ls_filekey-objid.

  READ TABLE lt_param INTO ls_param WITH KEY name = 'filesize'.
  IF sy-subrc EQ 0.
    lv_filesize = ls_param-value.
  ENDIF.
  " 弹出下载框函数，确定下载路径 "
  CALL METHOD cl_gui_frontend_services=>file_save_dialog
    EXPORTING
      default_file_name    = 'Z_Template'
      default_extension    = 'XLSX'
    CHANGING
      filename             = lv_filename
      path                 = lv_path
      fullpath             = lv_fullpath
      user_action          = lv_user_action
    EXCEPTIONS
      cntl_error           = 1
      error_no_gui         = 2
      not_supported_by_gui = 3
	  invalid_default_file_name = 4
      OTHERS               = 5.     
  IF sy-subrc <> 0.
*   Implement suitable error handling here
  ENDIF.
  " 保存文件到选择的下载路径 "
  IF lv_user_action EQ cl_gui_frontend_services=>action_ok.
    CALL METHOD cl_gui_frontend_services=>gui_download
      EXPORTING
        bin_filesize = lv_filesize
        filename     = lv_fullpath
        filetype     = 'BIN'
      CHANGING
        data_tab     = lt_mime.
    IF sy-subrc <> 0.
*     Implement suitable error handling here
    ENDIF.
  ENDIF.
```

- 选择合适的文件夹后保存的BAPI方法

  ```html
  FORM frm_download_files .
    DATA: lw_key TYPE wwwdatatab,
          lv_rc  TYPE sy-subrc.
    DATA: lv_filefilter       TYPE string,
          lv_default_filename TYPE string,
          lv_filename         TYPE string,
          lv_path             TYPE string,
          lv_fullpath         TYPE string,
          lv_user_action      TYPE i.
  
    DATA: lv_destination LIKE  rlgrap-filename .
    DATA: lw_application TYPE ole2_object,
          lw_workbook    TYPE ole2_object,
          lw_sheet       TYPE ole2_object.
    " 判断服务器模板是否存在 "
    SELECT SINGLE relid objid INTO CORRESPONDING FIELDS OF lw_key
      FROM wwwdata
      WHERE srtf2 EQ 0
      AND relid EQ 'MI'
      AND objid EQ 'OBJID'.      "SY-CPROG.上传的文件对象名"
    IF sy-subrc NE 0.
      MESSAGE 'Template is not exist' TYPE 'E'.
      RETURN.
    ENDIF.
    " 弹出下载框函数，确定下载路径 "
    CALL METHOD cl_gui_frontend_services=>file_save_dialog
      EXPORTING
        default_file_name    = 'Z_Template'
        default_extension    = 'XLSX'
      CHANGING
        filename             = lv_filename
        path                 = lv_path
        fullpath             = lv_fullpath
        user_action          = lv_user_action
      EXCEPTIONS
        cntl_error           = 1
        error_no_gui         = 2
        not_supported_by_gui = 3
  	  invalid_default_file_name = 4
        OTHERS               = 5.     
    IF sy-subrc <> 0.
  *   Implement suitable error handling here
    ENDIF.
  
    lv_destination = lv_fullpath .
    
    CALL FUNCTION 'DOWNLOAD_WEB_OBJECT'
      EXPORTING
        key         = lw_key
        destination = lv_destination
      IMPORTING
        rc          = s.
    IF lv_rc <> 0.
      MESSAGE 'Error occurs when download' TYPE 'E'. 
      RETURN.
    ENDIF.
  ENDFORM.                    " FRM_DOWNLOAD_FILES"
  ```

### 上传文件并转换为内表

- #### GUI输入框选择文件

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

- #### 判断本地文件是否存在

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

- 调用METHOD 读取文件内容到内表

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
1. 选择屏幕上加个文件路径选择
  SELECTION-SCREEN:BEGIN OF BLOCK BLK01 WITH FRAME TITLE TEXT-001.
  PARAMETERS:P_FILE LIKE RLGRAP-FILENAME.
  SELECTION-SCREEN END OF BLOCK BLK01.
2. 给文件搜索帮助
  AT SELECTION-SCREEN ON VALUE-REQUEST FOR P_FILE.
  PERFORM FRM_GET_FILEPATH.
  FORM FRM_GET_FILEPATH .
    CALL FUNCTION 'WS_FILENAME_GET'
      EXPORTING
        MASK             = ',Excel(*.xls),*.XLS,*.XLSX,'
        TITLE            = '选择文件'(100)
      IMPORTING
        FILENAME         = P_FILE
      EXCEPTIONS
        INV_WINSYS       = 1
        NO_BATCH         = 2
        SELECTION_CANCEL = 3
        SELECTION_ERROR  = 4
        OTHERS           = 5.
    IF SY-SUBRC <> 0.
      MESSAGE E100(ZDEV) WITH '选择文件出错！'(007).
    ENDIF.
  ENDFORM.
3. 获取EXCEL内容
  CALL FUNCTION 'ALSM_EXCEL_TO_INTERNAL_TABLE'
    EXPORTING
      FILENAME    = P_FILE
      I_BEGIN_COL = '1'
      I_BEGIN_ROW = '2'
      I_END_COL   = '300'
      I_END_ROW   = '65535'
    TABLES
      INTERN      = GT_EXCEL_T.
4. 获取EXCEL行，列，值进行数据处理
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

```JS
SELECTION-SCREEN:PUSHBUTTON /2(30) button1 USER-COMMAND but1."30是按钮长度"
SELECTION-SCREEN BEGIN OF BLOCK blk1 WITH FRAME TITLE text-001.
SELECTION-SCREEN BEGIN OF LINE.
PARAMETERS p_file TYPE rlgrap-filename.
SELECTION-SCREEN POSITION 50.
SELECTION-SCREEN COMMENT (20) p_comm FOR FIELD p_file.
SELECTION-SCREEN END OF LINE.
SELECTION-SCREEN END OF BLOCK blk1.

AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_file.
  "发出上传文件时，弹出选择文件框，选择对应的文件"
  PERFORM frm_get_filepath.

AT SELECTION-SCREEN. "检查界面操作并响应"
  IF sscrfields EQ 'BUT1'."下载按钮时，下载文档模板"
    PERFORM download_file.
  ENDIF.
  IF p_file IS NOT INITIAL.
    PERFORM frm_check_file."当文档上传框有请求时，检验路径是否存在"
  ENDIF.

INITIALIZATION.
  p_comm = ''.
  button1 = 'Download template file'.

START-OF-SELECTION.
  PERFORM frm_inter_table.
*  PERFORM frm_check_xzael.

FORM download_file .
  DATA: lwa_wwwdata_tab LIKE wwwdatatab,
        l_filename      TYPE rlgrap-filename,
        mes             TYPE string.
  l_filename = 'C:/temp/BatchInput.xlsx'.
  SELECT SINGLE *
    FROM wwwdata
   INNER JOIN tadir
      ON wwwdata~objid = tadir~obj_name
    INTO CORRESPONDING FIELDS OF lwa_wwwdata_tab
   WHERE wwwdata~srtf2  = 0
     AND wwwdata~relid  = 'MI'             "标识二进制的对象"
     AND tadir~pgmid    = 'R3TR'
     AND tadir~object   = 'W3MI'
     AND tadir~obj_name = 'ZMI05'.         "模板名字"
  IF sy-subrc = 0.
    CALL FUNCTION 'DOWNLOAD_WEB_OBJECT'
      EXPORTING
        key         = lwa_wwwdata_tab
        destination = l_filename.
    IF sy-subrc = 0.
      CONCATENATE 'The File Download Directory:' l_filename INTO mes.
      MESSAGE mes TYPE 'S'.
    ENDIF.
  ENDIF.
ENDFORM.                    " DOWNLOAD_FILE"

FORM frm_get_filepath .
  DATA lv_filepath TYPE ibipparms-path.
  CALL FUNCTION 'WS_FILENAME_GET'
    EXPORTING
      mask             = ',EXCEL FILE,*.XLS;*.XLSX;'
      mode             = 'O' "S为保存，O为打开"
    IMPORTING
      filename         = p_file
    EXCEPTIONS
      inv_winsys       = 1
      no_batch         = 2
      selection_cancel = 3
      selection_error  = 4
      OTHERS           = 5.
ENDFORM.                    " FRM_GET_FILEPATH "

FORM frm_check_file .
  DATA:lv_filename TYPE string,
       lv_result TYPE c.
  lv_filename = p_file.
  CALL METHOD cl_gui_frontend_services=>file_exist
    EXPORTING
      file                 = lv_filename
    RECEIVING
      result               = lv_result
    EXCEPTIONS
      cntl_error           = 1
      error_no_gui         = 2
      wrong_parameter      = 3
      not_supported_by_gui = 4
      OTHERS               = 5.
  IF sy-subrc NE 0.
  ENDIF.
  IF lv_result = ''.
    MESSAGE 'The File Not Found In The Direct!' TYPE 'E'.
  ENDIF.
ENDFORM.                    " FRM_CHECK_FILE "

FORM frm_inter_table.
    CALL FUNCTION 'ALSM_EXCEL_TO_INTERNAL_TABLE'
    EXPORTING
      filename                = p_file   "本地文件全路径名"
      i_begin_col             = '1'      "开始列"
      i_begin_row             = '2'      "开始行"
      i_end_col               = '7'      "结束列"
      i_end_row               = '6666'   "结束行"
    TABLES
      intern                  = it_file   "输出文件内容到it_file"
    EXCEPTIONS
      inconsistent_parameters = 1
      upload_ole              = 2
      OTHERS                  = 3.

  IF it_file[] IS INITIAL.
    MESSAGE 'There is no data in the excel!' TYPE 'E'.
  ENDIF.
  SORT it_file BY row.
  LOOP AT it_file ASSIGNING <wa_file>.     "将上传文件的内容写到内表中"
    CASE <wa_file>-col.
      WHEN '0001'.
        wa_iseg-gjahr  =  <wa_file>-value.
      WHEN '0002'.
        wa_iseg-iblnr  =  <wa_file>-value.
      WHEN '0003'.
        wa_iseg-zeili  =  <wa_file>-value.
      WHEN '0004'.
        wa_iseg-buchm  =  <wa_file>-value.
      WHEN '0005'.
        wa_iseg-meins  =  <wa_file>-value.
      WHEN '0006'.
        wa_iseg-lgort  =  <wa_file>-value.
      WHEN '0007'.
        wa_iseg-usnam  =  <wa_file>-value.
    ENDCASE.
    AT END OF row.
      APPEND wa_iseg TO it_iseg.
      CLEAR wa_iseg.
    ENDAT.
  ENDLOOP.
ENDFORM.                    " FRM_UPLOAD_FILE "
```


