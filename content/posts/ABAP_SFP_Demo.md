---
title: " SAP Adobe Form Demo "
date: 2020-04-13
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  ABAP

tags: 
  - abaputils

---

## Adobe Form调用

画好了对应的 Form 格式与数据绑定，就可以在 Report 程序中通过具体代码进行调用了。

### SFPOUTPUTPARAMS 参数介绍

**nodialog:** 是否弹出打印对话框

**noprint:** 不打印，能预览

**nopdf:** 不会产生PDF文档

**getpdf:** 得到PDF文档

**dest:** 打印终端指定

**copies:** 打印多少份

**reqnew:** 新开启一个SPOOL请求

**reqfinal:** SPOOL请求结束

**connection:** ADSRFC的

**xfp:** 外部程序可以调用，得到xml文件，该文件只包含内容，没有layout

### 程序内容

- Data define

  ```Js
  * Define Adobe Form Name
  DATA: lv_form_name TYPE FPWBFORMNAME.
  * Define Function Name
  DATA: lv_fm_name TYPE RS381_FNAM.
  * Define Print parameter
  DATA: lwa_fp_params TYPE SFPOUTPUTPARAMS.
  * Define Form Parameters for Form Processing
  DATA: lwa_fp_docparams TYPE SFPDOCPARAMS.
  * Define Result
  DATA: lv_result TYPE SFPJOBOUTPUT.
  DATA: lv_mblnr TYPE mblnr.
  
  START-OF-SELECTION.
  * Fetch the Data and store it in the Internal Table
  lv_mblnr = '30122345234'.
  ```

- Open spool job

  ```JS
  * Sets the output parameters and opens the spool job
  lwa_fp_params-connection = 'ADS'. 
  CALL FUNCTION 'FP_JOB_OPEN'    "& Form Processing: Call Form"
    CHANGING
      ie_outputparams = lwa_fp_params
    EXCEPTIONS
      cancel          = 1
      usage_error     = 2
      system_error    = 3
      internal_error  = 4
      OTHERS          = 5.
  IF sy-subrc <> 0.
    MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
      WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
  ENDIF.
  ```

- Get name  

  ```JS
  * Get the name of the generated function module
  lv_form_name = 'ZSFP_FORM'.
  CALL FUNCTION 'FP_FUNCTION_MODULE_NAME'           "& Form Processing Generation"
    EXPORTING
      i_name     = lv_form_name
    IMPORTING
      e_funcname = lv_fm_name.
  IF sy-subrc <> 0.
    MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
      WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
  ENDIF.
  ```

- Print Control

  ```JS
  * Language and country setting (Chinese)
  lwa_fp_docparams-langu   = 'D'.
  lwa_fp_docparams-country = 'CN'.
  * Call the generated function module
  CALL FUNCTION fm_name
    EXPORTING
      /1bcdwb/docparams     = lwa_fp_docparams
      im_mblnr              = lv_mblnr
  *  IMPORTING
  *   /1BCDWB/FORMOUTPUT    =
    EXCEPTIONS
      usage_error           = 1
      system_error          = 2
      internal_error        = 3.
  IF sy-subrc <> 0.
    MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
      WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
  ENDIF.
  ```
  
- Close spool job

  ```JS
  *&---- Close the spool job
  CALL FUNCTION 'FP_JOB_CLOSE'
    IMPORTING
      E_RESULT         = lv_result
    EXCEPTIONS
      usage_error      = 1
      system_error     = 2
      internal_error   = 3
      others           = 4.
  IF sy-subrc <> 0.
    MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
  	WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
  ENDIF.
  ```

  

