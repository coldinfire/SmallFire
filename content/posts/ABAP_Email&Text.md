---
title: " 发送带文本的 Email "
date: 2019-05-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

转自 [http://blog.chinaunix.net/uid-20591812-id-1918813.html](http://blog.chinaunix.net/uid-20591812-id-1918813.html)

### 程序实例

```JS
*&---------------------------------------------------------------------*
*& Report  Z14841_TEST010
*&---------------------------------------------------------------------*
REPORT  z14841_test010.
TYPES: BEGIN OF stru_master,
	kunnr  TYPE kunnr, " 客户编号 1"
	bukrs  TYPE bukrs, " 公司代码"
	pro_mill  TYPE werks_d,  " 工厂"
*quota     TYPE zcdfquota,  " 额度"
*account   TYPE char10,     " 帐期"
*zdays     TYPE zcdfdays,   " 天数"
*valid_beg TYPE zcdfdatum1, " 有效起始日期"
*valid_end TYPE zcdfdatum2, " 有效结束日期"
*docno     TYPE zcdfdocno,  " 授信申请编号"
*name1     TYPE name1_gp,   " 名称 1"
*assure_mode TYPE char10,   " 授信类别"
*quota1 TYPE zcdfquota,     " 额度"
*route  TYPE char1,         " 审批路径"
*uname  TYPE uname,         " 签准人"
*zlevel TYPE zcdflevel,     " 评级"
*zclass TYPE zcdfclass,     " 客户类型"
*zjye   TYPE dmbtr,         " 当月交易额"
*zqmye  TYPE dmbtr,         " 期末余额"
*zyqzk  TYPE dmbtr,         " 逾期金额"
END OF stru_master.
TYPES : tab_master TYPE TABLE OF stru_master.
DATA:lt_master TYPE	tab_master,
 	 wa_master TYPE stru_master.
*************************************************************
DATA: object_hd_change LIKE sood1 OCCURS 10 WITH HEADER LINE," 邮件正文的头信息 "
	  receivers LIKE soos1 OCCURS 10 WITH HEADER LINE,
      packing_list LIKE soxpl OCCURS 10 WITH HEADER LINE," 邮件附件的头信息 "
      objcont LIKE soli OCCURS 10 WITH HEADER LINE,      " 邮件正文 "
      att_cont LIKE soli OCCURS 10 WITH HEADER LINE,     " 邮件附件 "
      att_head LIKE soli OCCURS 10 WITH HEADER LINE.     " 头行 "
DATA: count TYPE i,
      length TYPE i.
DATA: lc_item1(24) TYPE c.
DATA: lc_item2(24) TYPE c.
DATA: lc_line_sub(29) TYPE c.
DATA: lc_line_sub2(52) TYPE c.
DATA: ls_data TYPE string.
DATA: li_len TYPE i.
DATA: ls_title TYPE string.
DATA: gs_string TYPE string.
REFRESH: objcont,receivers,packing_list,objcont,att_cont,att_head.

CLEAR: att_cont-line.
att_cont-line =  ' 附件文本的标题 '.
ls_title = att_cont-line.
APPEND att_cont.

CLEAR: att_cont-line.
att_cont-line =  ' 附件文本内容 '.
APPEND att_cont. " 如果有行内容，可以 append 多次 "
MOVE  ' 邮件内容标题 ' TO objcont-line.
APPEND objcont.
MOVE 'file' TO att_head-line.
APPEND att_head.

DESCRIBE TABLE objcont LINES count.
DESCRIBE FIELD objcont-line LENGTH length IN BYTE MODE .
object_hd_change-objla = 'E'.         " 创建文档使用的语言 "
object_hd_change-objnam = 'LIST'.     " 文档，文件夹或分配清单的名称 "
object_hd_change-objdes = gs_string.  " 内容的简短描述 CHAR50 "
object_hd_change-objsns = 'O'.        " 对象：灵敏度  P 机密 F 功能 O 标准 "
object_hd_change-objlen = count * length. " 文档内容的大小 "
APPEND object_hd_change.

* 接收者
receivers-recextnam = ['coldfire@163.com'](mailto:'coldfire@163.com').
receivers-recesc = 'U'.   " 地址类型， U: 互联网地址 "
APPEND receivers.         " 收件人地址 "

DESCRIBE TABLE att_cont LINES count.     " 邮件附件总行数 "
DESCRIBE FIELD att_cont-line LENGTH length IN BYTE MODE.      " 邮件每行的长度 "
packing_list-head_start = '1'.           " 传输包中的对象表头开始行 "
packing_list-body_start = '1'.           " 对象包中的对象内容开始行 " 
packing_list-head_num   = '0'.           " 在对象包内的对象表头行号 "
packing_list-body_num   = count * length." 在对象包内的对象内容行号 "
packing_list-objtp      = 'RAW'.         " 文档类别代码 "
packing_list-objdes = gs_string.
packing_list-objnam = gs_string.
* OBJDES OBJNAM
APPEND packing_list.
* 调用函数发送邮件
CALL FUNCTION 'SO_OBJECT_SEND'
  EXPORTING
   "New object: General header data"
    object_hd_change    = object_hd_change
    object_type         = 'RAW'       " RAW  SAP 编辑程序文件 "
    sender              = sy-uname    " 发送者用户名 "
  TABLES
    objcont             = objcont     " Content "
	"Recipient table with send attributes"
    receivers           = receivers    " 接收人地址
    packing_list        = packing_list " 邮件内容
    att_cont            = att_cont     " 附件
    att_head            = att_head     " 标题
  EXCEPTIONS
    active_user_not_exist      = 1
    communication_failure      = 2
    component_not_available    = 3
    folder_not_exist           = 4
    folder_no_authorization    = 5
    forwarder_not_exist        = 6
    note_not_exist             = 7
    object_not_exist           = 8
    object_not_sent            = 9
    object_no_authorization    = 10
    object_type_not_exist      = 11
    operation_no_authorization = 12
    owner_not_exist            = 13
    parameter_error            = 14
    substitute_not_active      = 15
    substitute_not_defined     = 16
    system_failure             = 17
    too_much_receivers         = 18
    user_not_exist             = 19
    originator_not_exist       = 20
    x_error                    = 21
    OTHERS                     = 22.
IF sy-subrc = 0.
  COMMIT WORK.
ENDIF.
*Instructs mail send program for SAPCONNECT to send email(rsconn01)
*PERFORM initiate_mail_execute_program.
WAIT UP TO 2 SECONDS.
SUBMIT rsconn01 WITH mode = 'INT'
                WITH output = 'X'
                AND RETURN.
```

