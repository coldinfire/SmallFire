---
title: " ABAP 调用HTTP请求 "
date: 2021-03-13
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---



### SAP 使用HTTP

#### 

### 示例程序

```ABAP
REPORT ZZ_SEND_HTTP.
* Selection Screen
  SELECTION-SCREEN BEGIN OF BLOCK bk01 WITH FRAME TITLE text-001.
    PARAMETER p_fname TYPE RS38L_FNAM.
    PARAMETER p_json  TYPE string.
  SELECTION-SCREEN END OF BLOCK bk01.
* Message
  TYPE: BEGIN OF str_message,
    type    TYPE bapi_mtype,
    message TYPE bapi_msg,
  END OF str_message.
  DATA: e_data TYPE str_message.
* Connection Parameter
  DATA:lt_setting TYPE TABLE OF zws_http_url WITH HEADER LINE.
  DATA:lv_url TYPE string.
  DATA:lv_user TYPE string.
  DATA:lv_password TYPE string.
  DATA:lv_authorization TYPE string.
  DATA:lv_error TYPE c.
* HTTP Connection
  DATA:lo_http_client TYPE REF TO if_http_client.
  DATA:lv_message TYPE string.
  DATA:lv_json TYPE string.
  DATA:lv_len TYPE i.
* Return Log Table
  CLEAR: lv_url,lv_user,lv_password,lv_authorization,lv_error,lv_message.
  CLEAR: lo_http_client.
* Prepare HTTP Connection Parameter
  CLEAR lt_setting.
  REFRESH lt_setting.
  SELECT *
    INTO TABLE lt_setting
    FROM zws_http_url
   WHERE func_name = p_fname.
  IF sy-subrc EQ 0.
    SORT lt_setting BY zz_key.
  ENDIF.
  
  CLEAR: lv_url,lv_user,lv_password,lv_authorization,lv_error.
  CLEAR: lo_http_client.
  READ TABLE lt_setting WITH KEY zz_key = 'URL' BINARY SEARCH.
  IF sy-subrc EQ 0.
    lv_url = lt_setting-zz_data.
  ELSE.
    lv_error = abap_true.
  ENDIF.
  READ TABLE lt_setting WITH KEY zz_key = 'USERNAME' BINARY SEARCH.
  IF sy-subrc EQ 0.
    lv_user = lt_setting-zz_data.
  ELSE.
    lv_error = abap_true.
  ENDIF.
  READ TABLE lt_setting WITH KEY zz_key = 'PASSWORD' BINARY SEARCH.
  IF sy-subrc EQ 0.
    lv_password = lt_setting-zz_data.
  ELSE.
    lv_error = abap_true.
  ENDIF.
  READ TABLE lt_setting WITH KEY zz_key = 'AUTHORIZATION' BINARY SEARCH.
  IF sy-subrc EQ 0.
    lv_authorization = lt_setting-zz_data.
  ELSE.
    lv_error = abap_true.
  ENDIF.
  IF lv_error = abap_true.
    e_data-type = 'E'.
    e_data-message = '读取系统设置有错，请检查'.
    RETURN.
  ENDIF.
* 创建URL连接
  CALL METHOD cl_http_client=>create_by_url
    EXPORTING
      url                = lv_url
    IMPORTING
      client             = lo_http_client
    EXCEPTIONS
      argument_not_found = 1
      plugin_not_active  = 2
      internal_error     = 3
      OTHERS             = 4.
  IF sy-subrc <> 0.
    lo_http_client->get_last_error( importing message = lv_message ).
    e_data-type = 'E'.
    e_data-message = lv_message.
    RETURN.
  ENDIF.
* USER NAME & PASSWORD
  CALL METHOD lo_http_client->authenticate
    EXPORTING
      username = lv_user
      password = lv_password.
* SET HEADER FIELD
  CALL METHOD lo_http_client->request->set_header_field
    EXPORTING
      name  = 'Authorization'
      value = lv_authorization.
  CALL METHOD lo_http_client->request->set_header_field
    EXPORTING
      name  = 'Password'
      value = 'XXXXX'.
  CALL METHOD lo_http_client->request->set_header_field
    EXPORTING
      name  = 'Content-Type'
      value = 'application/json'.
* SET content type
  CALL METHOD lo_http_client->request->set_content_type
    EXPORTING
      content_type = 'application/json'.
* data->json
 IF p_json IS INITIAL.
    e_data-type = 'E'.
    e_data-message = '传入参数为空，请检查'.
    RETURN.
  ENDIF.
* set data
  lv_len = STRLEN( p_json ).
  CALL METHOD lo_http_client->request->set_cdata
    EXPORTING
      data   = p_json
      length = lv_len.
* send data
  DO 2 TIMES.
    CALL METHOD lo_http_client->send
      EXCEPTIONS
        http_communication_failure = 1
        http_invalid_state         = 2
        http_processing_failed     = 3
        http_invalid_timeout       = 4
        OTHERS                     = 5.
    IF sy-subrc <> 0.
      e_data-type = 'E'.
      e_data-message = '发送HTTP请求失败，请检查'.
      WAIT UP TO 5 SECONDS.   "最大发送2次，第一次发送失败，则延迟5秒发送第二次
    ELSE.
      CLEAR:e_data.
      EXIT.
    ENDIF.
  ENDDO.
  IF e_data-type IS NOT INITIAL.
    RETURN.
  ENDIF.
* receive
  CALL METHOD lo_http_client->receive
    EXCEPTIONS
      http_communication_failure = 1
      http_invalid_state         = 2
      http_processing_failed     = 3
      OTHERS                     = 4.
  IF sy-subrc <> 0.
    e_data-type = 'E'.
    e_data-message = '接收HTTP响应失败，请检查'.
    RETURN.
  ENDIF.
* Get HTTP Response Data
  CLEAR lv_json.
  CALL METHOD lo_http_client->response->get_cdata
    RECEIVING
      data = lv_json.
* JSON->DATA
 WRITE: / lv_json.
 WRITE: / e_data.
```

