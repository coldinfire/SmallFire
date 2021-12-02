---
title: " SAP 上传和下载 Excel "
date: 2018-11-10
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
  - abaputils

---

### 上传模板

EXCEL 文档是通过 **SMW0** 上传的。

- 选择模板类型

  ![SMW0](/images/ABAP/ABAP_SMW0_1.png)

- 根据Package和Name查找模板

  ![SMW0](/images/ABAP/ABAP_SMW0_2.png)

- 编辑模板

  ![SMW0](/images/ABAP/ABAP_SMW0_3.png)

- 创建模板，维护名称和描述后从本地选择文件上传

  ![SMW0](/images/ABAP/ABAP_SMW0_4.png)

### 下载模板

通过 METHOD 或则 BAPI 的方法下载服务器上的模板文件到本地。

- CALL FUNCTION 'DOWNLOAD_WEB_OBJECT'：下载 Web 模板到指定目录，提示保存

```ABAP
FORM frm_download_excel_template .
  DATA: lt_filetab TYPE filetable, 
        lv_rc TYPE sy-subrc,
        lv_temp,
        lv_data_new LIKE wwwdatatab,
        lv_download_path TYPE rlgrap-filename.
  CLEAR: lt_filetab,lv_rc.
  REFRESH: lt_filetab.
* Open dialog
  CALL METHOD cl_gui_frontend_services=>file_open_dialog
    EXPORTING
      default_filename     = '*.xlsx'
     " initial_directory    = 'C:\' "
     " multiselection       = ''"
    CHANGING
      file_table           = lt_filetab
      rc                   = lv_rc
    EXCEPTIONS
      cntl_error           = 1
      error_no_gui         = 2
      not_supported_by_gui = 3
      OTHERS               = 4.
* Get file path
  CHECK lv_rc EQ 1.
  CLEAR lv_download_path.
  READ TABLE lit_filetab INDEX 1 INTO lv_download_path.
* Download a Web Template
  CLEAR: lv_data_new,lv_rc,lv_temp.
  lv_data_new-relid = 'MI'.
  lv_data_new-objid = 'ZFIDUL'. "SAP 服务器模板" 
  CALL FUNCTION 'DOWNLOAD_WEB_OBJECT'
    EXPORTING
      key         = lv_data_new
      destination = lv_download_path
    IMPORTING
      rc          = lv_rc
    CHANGING
      temp        = lv_temp.
  IF lv_rc NE 0.
    MESSAGE 'Download template failed' TYPE 'E'.
  ELSE.
    MESSAGE 'Download template success' TYPE 'S'.
  ENDIF.
ENDFORM.                    "frm_download_excel_template"
```

