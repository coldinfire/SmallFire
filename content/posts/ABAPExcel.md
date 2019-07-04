---
title: "Excle操作"
date: 2018-11-13
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: ["Excel","abap"]


---

## 使用 BAPI ##
```JS
1. 内表数据下载到文件:
  CALL FUNCTION 'DOWNLOAD'：提示保存
  CALL FUNCTION 'WS_DOWNLOAD'：不提示直接保存
  CALL FUNCTION 'DOWNLOAD_WEB_OBJECT'：提示保存
2. 文件数据读取到内表
  CALL FUNCTION 'UPLOAD'：提示读入内表
  CALL FUNCTION 'WS_UPLOAD'：不提示直接读入内表
```
## 上传和下载模板
上传模板：文档是通过SMW0上传的。SMW0:Binary data => Package => create object name and description

下载模板：调用METHOD 下载通过SMWO上传的服务器模板文件到本地

- 弹出下载框函数

```JS
DATA: lv_filename   TYPE string,
      lv_path       TYPE string,
      lv_fullpath   TYPE string,

FORM frm_file_save_dialog  USING    pv_value
   						   CHANGING pv_filename
									pv_path
									pv_fullpath.
  CALL METHOD cl_gui_frontend_services=>file_save_dialog
	EXPORTING
  	 default_file_name= pv_value
 	 default_extension= 'XLSX'
  CHANGING
  	filename = pv_filename
  	path = pv_path
  	fullpath = pv_fullpath
  EXCEPTIONS
  	cntl_error   = 1
  	error_no_gui = 2
  	not_supported_by_gui = 3
  	OTHERS   = 4.
ENDFORM." FRM_FILE_SAVE_DIALOG
```
- 选择合适的文件夹后保存

```JS
FORM frm_download_files  USING pv_fullpath .
  DATA:lw_key TYPE wwwdatatab,
       lv_rc  TYPE sy-subrc.
  DATA: lv_destination LIKE  rlgrap-filename .
  DATA: lw_application TYPE ole2_object,
        lw_workbook    TYPE ole2_object,
        lw_sheet       TYPE ole2_object.
  lv_destination = pv_fullpath .

  SELECT SINGLE relid objid INTO CORRESPONDING FIELDS OF lw_key
         FROM wwwdata
         WHERE srtf2 EQ 0
           AND relid EQ 'MI'
           AND objid EQ 'OBJID' ."SY-CPROG.上传的文件对象名
  IF sy-subrc NE 0.
    MESSAGE text-m03 TYPE 'E'. "Template is not exist
    RETURN.
  ENDIF.

  CALL FUNCTION 'DOWNLOAD_WEB_OBJECT'
    EXPORTING
      key         = lw_key
      destination = lv_destination
    IMPORTING
      rc          = lv_rc.
  IF lv_rc <> 0.
    MESSAGE text-m05 TYPE 'E'. "Error occurs when download
    RETURN.
  ENDIF.
ENDFORM.                    " FRM_DOWNLOAD_FILES
```
### 操作 ###
  CL_GUI_FRONTEND_SERVICES:该类提供了大量对操作系统文件的操作，如拷贝、列出文件名、打开文件等。
  
  打开文件：调用静态方法 FILE_OPEN_DIALOG

   将文本文件读取到内表：GUI_UPLOAD
  
   下载：GUI_DOWNLOAD

- 打开选择上传文件对话框

```JS
CALL METHOD CL_GUI_FRONTEND_SERVICES=>FILE_OPEN_DIALOG  
  EXPORTING
    WINDOW_TITLE = '选择上传文件'
    FILE_FILTER = 'All Files (*.*)|*.*|NotePad Files(*.txt)|*.txt|Excel Files(*.xls)|*.xls|Word files(*.doc)|*.doc' 
    DEFAULT_EXTENSION = '*.txt'
    DEFAULT_FilENAME = '1.txt'  "默认打开的文件
    "INITIAL_DIRECTORY = 'C:/'  "初始化的目录
    "MULTISELECTION = 'X' "是否可以同时打开多个文件
    CHANGING
    FILE_TABLE = LV_FILETABLE "你打开文件名的列表
    RC = LV_RC . "返回打开文件的数量
```
- 调用METHOD 读取文件内容到内表

```JS    　　
CALL FUNCTION 'GUI_UPLOAD'
   EXPORTING
    FILENAME                      = LV_FILENAME  "要讀取的文件
    FILETYPE                      = 'ASC'
    HAS_FIELD_SEPARATOR           =  CL_ABAP_CHAR_UTILITIES=>HORIZONTAL_TAB "字段間按TAB鍵分隔開來
  TABLES
    DATA_TAB                      = TXT_READ_DATA  "寫入相應的內表中
    EXCEPTIONS
    FILE_OPEN_ERROR               = 1
    FILE_READ_ERROR               = 2
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
        I_END_ROW   = '50000'
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

### [实例](https://github.com/coldinfire/ERP/wiki/%E6%96%87%E6%A1%A3%E4%B8%8A%E4%BC%A0%E5%92%8C%E4%B8%8B%E8%BD%BD#%E4%B8%89%E5%AE%9E%E4%BE%8B)

```JS
SELECTION-SCREEN:
  PUSHBUTTON /2(30) button1 USER-COMMAND but1."30是按钮长度

SELECTION-SCREEN BEGIN OF BLOCK blk1 WITH FRAME TITLE text-001.
SELECTION-SCREEN BEGIN OF LINE.
PARAMETERS p_file TYPE rlgrap-filename.
SELECTION-SCREEN POSITION 50.
SELECTION-SCREEN COMMENT (20) p_comm FOR FIELD p_file.
SELECTION-SCREEN END OF LINE.
SELECTION-SCREEN END OF BLOCK blk1.

AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_file.
  "发出上传文件时，弹出选择文件框，选择对应的文件
  PERFORM frm_get_filepath.

AT SELECTION-SCREEN. "检查界面操作并响应
  IF sscrfields EQ 'BUT1'."下载按钮时，下载文档模板
    PERFORM download_file.
  ENDIF.
  IF p_file IS NOT INITIAL.
    PERFORM frm_check_file."当文档上传框有请求时，检验路径是否存在
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
     AND wwwdata~relid  = 'MI'             "标识二进制的对象
     AND tadir~pgmid    = 'R3TR'
     AND tadir~object   = 'W3MI'
     AND tadir~obj_name = 'ZMI05'.         "模板名字

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
ENDFORM.                    " DOWNLOAD_FILE

FORM frm_get_filepath .
  DATA lv_filepath TYPE ibipparms-path.

  CALL FUNCTION 'WS_FILENAME_GET'
    EXPORTING
      mask             = ',EXCEL FILE,*.XLS;*.XLSX;'
      mode             = 'O' "S为保存，O为打开
    IMPORTING
      filename         = p_file
    EXCEPTIONS
      inv_winsys       = 1
      no_batch         = 2
      selection_cancel = 3
      selection_error  = 4
      OTHERS           = 5.
ENDFORM.                    " FRM_GET_FILEPATH

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
ENDFORM.                    " FRM_CHECK_FILE

FORM frm_inter_table.
    CALL FUNCTION 'ALSM_EXCEL_TO_INTERNAL_TABLE'
    EXPORTING
      filename                = p_file   "本地文件全路径名
      i_begin_col             = '1'      "开始列
      i_begin_row             = '2'      "开始行
      i_end_col               = '7'      "结束列
      i_end_row               = '6666'   "结束行
    TABLES
      intern                  = it_file   "输出文件内容到it_file
    EXCEPTIONS
      inconsistent_parameters = 1
      upload_ole              = 2
      OTHERS                  = 3.

  IF it_file[] IS INITIAL.
    MESSAGE 'There is no data in the excel!' TYPE 'E'.
  ENDIF.
  SORT it_file BY row.
  LOOP AT it_file ASSIGNING <wa_file>.     "将上传文件的内容写到内表中
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
ENDFORM.                    " FRM_UPLOAD_FILE
```


