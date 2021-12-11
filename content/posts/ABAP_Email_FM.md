---
title: " SAP 通过程序收发邮件 "
date: 2019-04-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 程序所需 CLASS 和 FM

#### CLASS

- CL_BCS：发送邮件的主要功能类，创建发送请求，添加发送内容，添加发送人等，最后发送指令的发出
- CL_DOCUMENT_BCS：该类的主要功能是存放邮件发送的内容
- CX_BCS：异常类，捕捉发送邮件过程中出现的异常

#### Function Module

- SO_DOCUMENT_SEND_API1
- SO_NEW_DOCUMENT_ATT_SEND_API1
- SO_NEW_DOCUMENT_SEND_API1

### 示例程序

```ABAP
*&---------------------------------------------------------------------*
*& Report  ZZ_SEND_EMAIL
*&---------------------------------------------------------------------*

REPORT  zz_send_email.
DATA:send_request TYPE REF TO cl_bcs VALUE IS INITIAL.
DATA:sender TYPE REF TO if_sender_bcs VALUE IS INITIAL,
     uname TYPE sy-uname.
DATA:recipient TYPE REF TO if_recipient_bcs VALUE IS INITIAL,
     email TYPE string.
DATA:document TYPE REF TO cl_document_bcs VALUE IS INITIAL,
     subject TYPE so_obj_des,
     lt_text TYPE soli_tab,
     ls_text LIKE LINE OF lt_text,
     file_size_char  TYPE so_obj_len.
DATA:filepath   TYPE string,
     path       TYPE string,
     attachment_len TYPE i,
     attachment_hex TYPE solix_tab.
DATA:send_result TYPE os_boolean,
     excep_error TYPE REF TO cx_bcs.
TRY.
    " 创建发送请求 "
    send_request = cl_bcs=>create_persistent( ).
    " 发件人 "
    uname  = sy-uname.
    sender = cl_sapuser_bcs=>create( uname ).
    send_request->set_sender( sender ).
    " 收件人 "
    LOOP AT lt_mailaccept.
      CLEAR: email,recipient.
      CONCATENATE lt_mailaccept-name '@XXXXX.COM' INTO email.
      TRANSLATE email TO LOWER CASE.
      recipient = cl_cam_address_bcs=>create_internet_address( email ).
      CALL METHOD send_request->add_recipient
        EXPORTING
          i_recipient  = recipient
          i_express    = 'X'
          i_copy       = ' '
          i_blind_copy = ' '
          i_no_forward = ' '.
    ENDLOOP.
    " 抄送人 "
    LOOP AT lt_mailaccept_copy.
      CLEAR: email,recipient.
      CONCATENATE lt_mailaccept_copy-name '@XXXXX' INTO email.
      TRANSLATE email TO LOWER CASE.
      recipient = cl_cam_address_bcs=>create_internet_address( email ).
      CALL METHOD send_request->add_recipient
        EXPORTING
          i_recipient  = recipient
          i_express    = 'X'
          i_copy       = 'X'
          i_blind_copy = ' '
          i_no_forward = ' '.
    ENDLOOP.
    " 增加发送内容到发送请求 "
    ls_text-line = 'Email body context'.
    APPEND ls_text TO lt_text.
    CLEAR ls_text.
    CREATE OBJECT document.
    document = cl_document_bcs=>create_document(
                 i_type  = 'RAW'        " Type of document "
                 i_subject = subject    " Email subject "
                 i_length  = file_size_char  " File size "
                 i_text  = lt_text      " Email body internal table "
                 i_importance = '1'     " Email Document priority "
    ).
    send_request->set_document( document ).
     " 添加附件 "
    CALL METHOD cl_gui_frontend_services=>gui_upload
      EXPORTING
        filename   = filepath
        filetype   = 'BIN'
      IMPORTING
        filelength = attachment_len
      CHANGING
        data_tab   = attachment_hex.
    DO.
      SPLIT filepath AT '\' INTO path filepath.
      SEARCH filepath FOR '\'.
      IF sy-subrc <> 0.
        EXIT.
      ENDIF.
    ENDDO.
    subject = filepath.
    file_size_char = attachment_len.
    CALL METHOD document->add_attachment(
      EXPORTING
        i_attachment_type    = 'BIN'
        i_attachment_subject = subject         "附件名称"
        i_attachment_size    = file_size_char  "附件大小"
        i_att_content_hex     = attachment_hex "附件内容"
    ). 
    " 立即发送 "
    send_request->set_send_immediately( 'X' ).
    send_request->send_request->set_link_to_outbox( 'X' ).
    " 发送 "
    CALL METHOD send_request->send
      EXPORTING
        i_with_error_screen = 'X'
      RECEIVING
        result              = send_result.
  CATCH cx_bcs INTO excep_error.
ENDTRY.
IF send_result = 'X'.
  COMMIT WORK AND WAIT.
  WRITE :/ 'Mail sent successfully'.
ELSE.
  ROLLBACK WORK.
  WRITE :/ 'Mail sent fail'.
ENDIF.
```

### 相关链接

- [发送带文本附件的Email](https://coldinfire.github.io/2019/ABAP_EmailText/)
- [发送带Excel附件的Email](https://coldinfire.github.io/2019/ABAP_EmailExcel/)
- 自动发送邮件BAPI