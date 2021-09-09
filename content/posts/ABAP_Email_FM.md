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

- CL_BCS
- CL_DOCUMENT_BCS

#### Function Module

- SO_DOCUMENT_SEND_API1
- SO_NEW_DOCUMENT_ATT_SEND_API1
- SO_NEW_DOCUMENT_SEND_API1

### 示例程序

```JS
DATA:l_send_request TYPE REF TO CL_BCS VALUE IS INITIAL.
DATA:l_document TYPE REF TO CL_DOCUMENT_BCS VALUE IS INITIAL,
     i_text TYPE BCSY_TEXT,     " Table for body "
     w_text LIKE LINE OF i_text,
     l_subject TYPE SO_OBJ_DES.
DATA:l_sender TYPE REF TO IF_SENDER_BCS VALUE IS INITIAL,
     l_uname TYPE sy-uname.
DATA:l_recipient TYPE REF TO IF_RECIPIENT_BCS VALUE IS INITIAL,
     i_email TYPE STRING.
TRY.
" 创建发送请求 "
  l_send_request = cl_bcs=>create_persistent( ).
" 设定发送内容 " 
  w_text-line = 'Email body context'.
  APPEND w_text TO i_text.
  CLEAR w_text.
  l_document = cl_document_bcs=>create_document(
                   i_type  = 'RAW'       " Type of document "
                   i_subject = l_subject " Email subject "
                   i_text  = i_text " Email body internal table "
                   I_IMPORTANCE = '1'    " Email Document priority "
                    ).
" 增加发送内容到发送请求 "
  CALL METHOD l_send_request->set_document( l_document ).
" 取得发送者（取得发件人，前提是这个邮箱地址能发邮件，并且不需要密码）"
  l_uname = sy-uname.
  l_sender = cl_sapuser_bcs=>create( l_uname ).
  CALL METHOD l_send_request->set_sender
    EXPORTING
      i_sender = l_sender.
" 设置收件人 "
   LOOP AT it_mailaccept.
     CLEAR i_email.
     CONCATENATE it_mailaccept-name '@XXXXX.COM' INTO i_email.
     TRANSLATE i_email TO LOWER CASE.
     l_recipient = cl_cam_address_bcs=>create_internet_address( i_email ).
     CALL METHOD l_send_request->add_recipient
       EXPORTING
         i_recipient  = l_recipient
         i_express    = 'X'
         i_copy       = ' '
         i_blind_copy = ' '
         i_no_forward = ' '.
   ENDLOOP.
" 设置抄送人 "
    LOOP AT it_mailaccept_copy.
      CLEAR i_email.
      CONCATENATE it_mailaccept_copy-name '@XXXXX' INTO i_email.
      TRANSLATE i_email TO LOWER CASE.
      l_recipient = cl_cam_address_bcs=>create_internet_address( i_email ).
      CALL METHOD l_send_request->add_recipient
        EXPORTING
          i_recipient  = l_recipient
          i_express    = 'X'
          i_copy       = 'X'
          i_blind_copy = ' '
          i_no_forward = ' '.
    ENDLOOP.
" 立即发送 "
  CALL METHOD l_send_request->set_send_immediately( 'X' ).
" 发送 "
  CALL METHOD l_send_request->send( 
    EXPORTING
      I_WITH_ERROR_SCREEN = 'X'
  ).
  COMMIT WORK.
  IF SY-SUBRC = 0.
    WRITE :/ 'Mail sent successfully'.
  ENDIF.
  CATCH cx_document_bcs INTO l_bcs_exception.
  CATCH cx_send_req_bcs INTO l_send_exception.
  CATCH cx_address_bcs  INTO l_addr_exception.
ENDTRY.
```

### 相关链接

- [发送带文本附件的Email](https://coldinfire.github.io/2019/ABAP_EmailText/)
- [发送带Excel附件的Email](https://coldinfire.github.io/2019/ABAP_EmailExcel/)
- 自动发送邮件BAPI