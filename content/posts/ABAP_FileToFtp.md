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
DATA: function_name TYPE rs38l_fnam,
      form_name  TYPE tdsfname VALUE 'ZTEST1'.
DATA: carr_id TYPE sbook-carrid,
      ctrlop  TYPE ssfctrlop,
      compop   TYPE ssfcompop,
      tab_otf_data  TYPE ssfcrescl.
DATA: pdf_tab       LIKE tline OCCURS 0 WITH HEADER LINE,
      tab_otf_final TYPE itcoo OCCURS 0 WITH HEADER LINE,
      bin_filesize  TYPE i,
      pdf_xstring TYPE xstring,
      file_name     TYPE string,
      file_path     TYPE string,
      full_path     TYPE string.

PARAMETERS : vbeln TYPE vbak-vbeln.   " input parameter
SELECT vbeln vkorg vkbur FROM vbak INTO TABLE t_vbak WHERE vbeln = vbeln.
compop-tddest = 'LP01'.
ctrlop-no_dialog = 'X'.
ctrlop-preview = space.
ctrlop-getotf = 'X'.
CALL FUNCTION 'SSF_FUNCTION_MODULE_NAME'
  EXPORTING
    formname           = form_name
  IMPORTING
    fm_name            = function_name
  EXCEPTIONS
    no_form            = 1
    no_function_module = 2
    OTHERS             = 3.
CALL FUNCTION function_name
  EXPORTING
    control_parameters = ctrlop
    output_options     = compop
    user_settings      = space
    vbeln              = vbeln
  IMPORTING
    job_output_info    = tab_otf_data
  TABLES
    t_vbak             = t_vbak.
" Convert data to PDF "
tab_otf_final[] = tab_otf_data-otfdata[].
CALL FUNCTION 'CONVERT_OTF'
  EXPORTING
    format                = 'PDF'
    max_linewidth         = 132
  IMPORTING
    bin_filesize          = bin_filesize
    bin_file              = pdf_xstring
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
" send pdf file to server "
DATA: handle   TYPE i,
      key      TYPE i VALUE 26101957,
      pwd_len  TYPE i,
      command_index TYPE i.
DATA: user(30) TYPE c VALUE 'ftptest123', "user name of ftp server"
      pwd(30)  TYPE c VALUE 'pass123',    "password of ftp server"
      host(64) TYPE c VALUE 'enter host', "ip address of FTP server"
      dest     LIKE rfcdes-rfcdest VALUE 'SAPFTPA'. "Background RFC destination"
DATA filename TYPE char200.
DATA bindata  TYPE TABLE OF blob WITH HEADER LINE.
DATA result   TYPE TABLE OF text WITH HEADER LINE.
DATA commands TYPE TABLE OF text WITH HEADER LINE.

DATA i_tab_converted_data TYPE STANDARD TABLE OF char200 WITH HEADER LINE.
TYPES:BEGIN OF str_xml,
  data(256) TYPE x,
END OF str_xml.
DATA: xml_table TYPE TABLE OF str_xml,
      xml_line  TYPE str_xml, "Adjust as per requirement/OS limits"
      filepath(128) TYPE c,
      command(200) TYPE c.

filepath = '/home'.
OPEN DATASET filepath FOR OUTPUT IN BINARY MODE.
IF sy-subrc = 0.
  LOOP AT xml_table INTO xml_line.
    TRANSFER xml_line TO filepath.
  ENDLOOP.
ENDIF.
CLOSE DATASET filepath.
" Scramble the password provided in a format recognized by SAP."
SET EXTENDED CHECK OFF.
pwd_len = strlen( pwd ).
CALL FUNCTION 'HTTP_SCRAMBLE'
  EXPORTING
    source      = pwd
    sourcelen   = pwd_len
    key         = key
  IMPORTING
    destination = pwd.
IF sy-subrc <> 0.
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
    WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
ENDIF.
" To Connect to the Server using FTP "
CALL FUNCTION 'FTP_CONNECT'
  EXPORTING
    user            = user
    password        = pwd
    host            = host
    rfc_destination = dest
  IMPORTING
    handle          = handle
  EXCEPTIONS
    OTHERS          = 1.
IF sy-subrc <> 0.
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
    WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
ENDIF.
" Upload File "
CONCATENATE '/' 'test' '.pdf' INTO filename.
CALL FUNCTION 'SCMS_XSTRING_TO_BINARY'
  EXPORTING
    buffer     = pdf_xstring
  TABLES
    binary_tab = bindata.
CALL FUNCTION 'FTP_COMMAND'
  EXPORTING
    handle        = handle
    command       = 'set passive on'
  TABLES
    data          = result
  EXCEPTIONS
    command_error = 1
    tcpip_error   = 2.
CALL FUNCTION 'FTP_R3_TO_SERVER'
  EXPORTING
    handle        = handle
    fname         = filename
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
    handle = handle
  EXCEPTIONS
    OTHERS = 1.
* Disconnect the RFC connection between SAP and other system.
CALL FUNCTION 'RFC_CONNECTION_CLOSE'
  EXPORTING
    destination = dest
  EXCEPTIONS
    OTHERS      = 1.
WRITE: 'pdf file send successfully'.
```

