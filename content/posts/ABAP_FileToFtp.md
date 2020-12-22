---
title: " Field Symbols "
date: 2018-12-15
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils
---



原文链接：[https://sapabap-tutorial.blogspot.com/2016/11/send-pdf-file-to-ftp-server.html](https://sapabap-tutorial.blogspot.com/2016/11/send-pdf-file-to-ftp-server.html)

### Send pdf file to ftp server

```html
REPORT  zpdfsendtoftp.
DATA : BEGIN OF t_vbak OCCURS 0,
         vbeln TYPE vbrk-vbeln,
         vkorg TYPE vbak-vkorg,
         vkbur TYPE vbak-vkbur,
       END OF t_vbak.
DATA: fm_name TYPE rs38l_fnam,
      p_form  TYPE tdsfname VALUE 'ZTEST1'.   "smartforms name
DATA: carr_id TYPE sbook-carrid,
      cparam        TYPE ssfctrlop,
      outop         TYPE ssfcompop,
      tab_otf_data  TYPE ssfcrescl,
      pdf_tab       LIKE tline OCCURS 0 WITH HEADER LINE,
      tab_otf_final TYPE itcoo OCCURS 0 WITH HEADER LINE,
      file_size     TYPE i,
      bin_filesize  TYPE i,
      file_name     TYPE string,
      file_path     TYPE string,
      full_path     TYPE string.

PARAMETERS : vbeln TYPE vbak-vbeln.   " input parameter
SELECT vbeln vkorg vkbur FROM vbak INTO TABLE t_vbak WHERE vbeln = vbeln.
outop-tddest = 'LP01'.
cparam-no_dialog = 'X'.
cparam-preview = space.
cparam-getotf = 'X'.
CALL FUNCTION 'SSF_FUNCTION_MODULE_NAME'
  EXPORTING
    formname           = p_form
  IMPORTING
    fm_name            = fm_name
  EXCEPTIONS
    no_form            = 1
    no_function_module = 2
    OTHERS             = 3.
CALL FUNCTION fm_name
  EXPORTING
    control_parameters = cparam
    output_options     = outop
    user_settings      = space
    vbeln              = vbeln
  IMPORTING
    job_output_info    = tab_otf_data
  TABLES
    t_vbak             = t_vbak.
"  "
tab_otf_final[] = tab_otf_data-otfdata[].
CALL FUNCTION 'CONVERT_OTF'
  EXPORTING
    format                = 'PDF'
    max_linewidth         = 132
  IMPORTING
    bin_filesize          = bin_filesize
  TABLES
    otf                   = tab_otf_final
    lines                 = pdf_tab
  EXCEPTIONS
    err_max_linewidth     = 1
    err_format            = 2
    err_conv_not_possible = 3
    err_bad_otf           = 4
    OTHERS                = 5.
IF sy-subrc <> 0.
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
  WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
ENDIF.
"""""""""""" send pdf file to server """""""""""
DATA : i_tab_converted_data  TYPE STANDARD TABLE OF char200 WITH HEADER LINE.
TYPES:BEGIN OF ty_ftp,
    zcountry  TYPE zftpi-zcountry,
    zuser     TYPE zftpi-zuser,
    zpasswd   TYPE zftpi-zpasswd,
    zhost     TYPE zftpi-zhost,
    zrfc_dest TYPE zftpi-zrfc_dest,
    zapp      TYPE zftpi-zapp,
    zftp      TYPE zftpi-zftp,
  END OF ty_ftp,
  BEGIN OF xml_line,
    data(256) TYPE x,
  END OF xml_line.
DATA: lt_xml_table      TYPE TABLE OF xml_line,
      l_xml_size        TYPE i,
      l_rc              TYPE i,
      lv_success        TYPE i VALUE 1,
      l_element_package TYPE REF TO if_ixml_element,
      l_element_details TYPE REF TO if_ixml_element,
      l_value           TYPE string.
DATA: result   TYPE TABLE OF text WITH HEADER LINE,
      commands TYPE TABLE OF text WITH HEADER LINE.
DATA: c TYPE likp-ablad.
DATA: country TYPE char2.
DATA :rec  TYPE xml_line. "Adjust as per requirement/OS limits
DATA filepath(128) TYPE c.

DATA: w_hdl         TYPE i,
      c_key         TYPE i VALUE 26101957,
      l_slen        TYPE i,
      command_index TYPE i.
DATA: l_interface_key TYPE char30,
      l_filename      TYPE char200,
      l_content_type  TYPE char10,
      lv_command(200) TYPE c.
DATA: t_time  TYPE sy-uzeit,
      lv_move TYPE c,
      lv_pick TYPE c,
      lv_pack TYPE c.
DATA: l_user(30) TYPE c VALUE 'ftptest123', "user name of ftp server
      l_pwd(30)  TYPE c VALUE 'pass123', "password of ftp server
      l_host(64) TYPE c VALUE 'enter host', "ip address of FTP server
      l_dest     LIKE rfcdes-rfcdest VALUE 'SAPFTPA'. "Background RFC destination
filepath = '/home'.
OPEN DATASET filepath FOR OUTPUT IN BINARY MODE.
IF sy-subrc = 0.
  LOOP AT lt_xml_table INTO rec.
    TRANSFER rec TO filepath.
  ENDLOOP.
ENDIF.
CLOSE DATASET filepath.
CONCATENATE '/' 'test' '.pdf' INTO l_filename.  " file name
CONCATENATE 'put' filepath l_filename INTO lv_command SEPARATED BY space.
*HTTP_SCRAMBLE: used to scramble the password provided in a format recognized by SAP.
SET EXTENDED CHECK OFF.
l_slen = strlen( l_pwd ).
CALL FUNCTION 'HTTP_SCRAMBLE'
  EXPORTING
    source      = l_pwd
    sourcelen   = l_slen
    key         = c_key
  IMPORTING
    destination = l_pwd.
* To Connect to the Server using FTP
CALL FUNCTION 'FTP_CONNECT'
  EXPORTING
    user            = l_user
    password        = l_pwd
    host            = l_host
    rfc_destination = l_dest
  IMPORTING
    handle          = w_hdl
  EXCEPTIONS
    OTHERS          = 1.
IF sy-subrc <> 0.
  lv_success = 0.
  EXIT.
ENDIF.
REFRESH commands.

CALL FUNCTION 'FTP_R3_TO_SERVER'
  EXPORTING
    handle        = w_hdl
    fname         = l_filename
    blob_length   = bin_filesize
  TABLES
    blob          = pdf_tab
  EXCEPTIONS
    tcpip_error   = 1
    command_error = 2
    data_error    = 3
    OTHERS        = 4.

* To disconnect the FTP
CALL FUNCTION 'FTP_DISCONNECT'
  EXPORTING
    handle = w_hdl.

*RFC_CONNECTION_CLOSE:This is used to disconnect the RFC connection between SAP and other system.
CALL FUNCTION 'RFC_CONNECTION_CLOSE'
  EXPORTING
    destination = l_dest
  EXCEPTIONS
    OTHERS      = 1.
WRITE: 'pdf file send successfully'.
```

