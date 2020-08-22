---
title: " SAP发送邮件 "
date: 2019-04-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---



###  SAP 发送邮件程序代码

```JS
TRY.
" 创建发送请求 "
  l_send_request = cl_bcs=>create_persistent( ).
" 设定发送内容 " 
  l_document = cl_document_bcs=>create_document( i_type  = 'RAW'
                                           i_text  = i_content[]
                                           I_IMPORTANCE = '1'
                                           i_subject = l_subject ).
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
  l_send_request->set_send_immediately( 'X' ).
" 发送 "
  CALL METHOD l_send_request->send( ).
    COMMIT WORK.
  CATCH cx_document_bcs INTO l_bcs_exception.
  CATCH cx_send_req_bcs INTO l_send_exception.
  CATCH cx_address_bcs  INTO l_addr_exception.
ENDTRY.
```

