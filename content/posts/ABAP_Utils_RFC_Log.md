---
title: " SAP RFC Function 日志工具 "
date: 2021-08-28
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

## SAP 与外部系统接口日志记录

### 工具结构设计

![Object Structure](/images/SAPUtils/RFC_LOG_1.png)

### 表设计

#### Log 信息表：ZFUNC_LOG

![ZFUNC_LOG](/images/SAPUtils/RFC_LOG_2.png)

#### 输入参数 Log 表： ZFUNC_LOG_DATA

![ZFUNC_LOG_DATA](/images/SAPUtils/RFC_LOG_3.png)

#### 输出参数 Log 表： ZFUNC_LOG_DATA_E

![ZFUNC_LOG_DATA_E](/images/SAPUtils/RFC_LOG_4.png)

### 程序结构设计

#### RFC 传入参数保存

- 传入参数如果存在转换规则，则进行对应的输入数据转换；没有就正常接收传入参数
- 读取 RFC Function 中的输入参数和 TABLE 的值，将参数转换成 JSON 格式数据，保存到输入参数表

#### RFC 传出参数保存

- 输出参数如果存在转换规则，则进行对应的输入数据转换
- 读取 RFC Function 中的输出参数和 TABLE 的值，将参数转换成 JSON 格式数据，保存到输出参数表

### 程序使用

#### 记录 RFC Log

```ABAP
REPORT  zfunc.
"统一管理程序
INCLUDE zfunc_top.
INCLUDE zfunc_trans_in. " 输入值进行对应转换 "
INCLUDE zfunc_begin.    " 保存数据到log表 "
*执行业务代码
" 执行完业务代码后，保存执行结束后的参数内容 "
INCLUDE zfunc_trans_out. " 输出值进行对应转换 "
INCLUDE zfunc_end.       " 保存结果数据到log表 "
*&---------------------------------------------------------------------*
*&      Form   update_statu
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*      -->IM_LOG_ID  text
*      -->IM_CODE    text
*      -->IM_TXT     text
*      -->IM_USER1   text
*      -->IM_USER2   text
*      -->IM_USER3   text
*----------------------------------------------------------------------*
FORM update_statu USING im_log_id TYPE zfunc_log_data-id
                        im_code   TYPE bapi_mtype
                        im_txt    TYPE bapi_msg
                        im_user1 im_user2 im_user3 .
  IF im_log_id IS NOT INITIAL.
    CALL FUNCTION 'ZFUNC_UPDATE_STATU' STARTING NEW TASK 'ZFUNC_LOG'
      EXPORTING
        i_id     = im_log_id
        i_msgty  = im_code
        i_msgtxt = im_txt
        i_user1  = im_user1
        i_user2  = im_user2
        i_user3  = im_user3.
  ENDIF.
ENDFORM.                    " update_statu "
```

#### RFC Log 查询报表

```ABAP
REPORT  zfunc_log.
INCLUDE zbc_incl_alv.
INCLUDE zbc_incl_alv_oo.
INCLUDE zbc_incl_alvevt.
TABLES:zfunc_log,tftit.
"报表主数据"
TYPES: BEGIN OF ty_s_out.
        INCLUDE STRUCTURE zfunc_log.
TYPES: time_begin TYPE char20,
       time_end   TYPE char20.
TYPES: canum      TYPE zfunc_log_data-canum,
       func_parameter TYPE zfunc_log_data-func_parameter,
       func_structure TYPE zfunc_log_data-func_structure,
       json_data      TYPE zfunc_log_data-json_data,
       stext          TYPE tftit-stext,
       zbz(20).
TYPES: END OF ty_s_out.
TYPES: ty_t_out TYPE TABLE OF ty_s_out.
DATA:gt_out TYPE ty_t_out.
DATA:gs_out TYPE ty_s_out.
"每一个RFC传参详细信息"
DATA:BEGIN OF gs_data,
  canum          TYPE zfunc_log_data-canum,
  func_parameter TYPE zfunc_log_data-func_parameter,
  func_paramtype TYPE zfunc_log_data-func_paramtype,
  func_structure TYPE zfunc_log_data-func_structure,
  json_data      TYPE string,
  param_type(20),
  stext          TYPE paramtext,
END OF gs_data.
DATA: gt_data LIKE TABLE OF gs_data.
START-OF-SELECTION.
  PERFORM frm_get_data.
END-OF-SELECTION.
  PERFORM frm_display_data.
```

