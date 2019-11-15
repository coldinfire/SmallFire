---
title: " SAP下载程序源码工具 "
date: 2019-04-10
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abap
  - utils

---



程序：转自 http://blog.sina.com.cn/s/blog_4d1570de0100pvhd.html

```JS
*@---------------------------------------------------------------------*
*@ Report ZZXUE01 下载程序代码
*@ T-code
*@---------------------------------------------------------------------*
*@ Created by Xavery Hsueh on 2011-03-01
*@ Lasted Edited date:
*@---------------------------------------------------------------------*
"REPORT XXX NO STANDARD PAGE HEADING.

***********************************************************************@
** 声明数据库表
***********************************************************************@
TABLES:rs38m,
trdir. 
"***********************************************************************@
"** 内表结构类型的定义
"***********************************************************************@
DATA BEGIN OF dynpfields OCCURS 1.
INCLUDE STRUCTURE dynpread.
DATA END OF dynpfields.

TYPES:BEGIN OF typ_result,
box TYPE c,
tabix TYPE sytabix, "顺序号
name TYPE char40, "程序名称
cnam TYPE cnam, "创建人员
unam TYPE unam, "最后修改人
code(72) TYPE c, "程序描述
END OF typ_result.
***********************************************************************@
** 变量与内表的定义
***********************************************************************@
DATA:gt_report TYPE TABLE OF tdline WITH HEADER LINE.
DATA:gt_result TYPE TABLE OF typ_result WITH HEADER LINE.
DATA:gt_trdir TYPE TABLE OF trdir WITH HEADER LINE.
DATA:gt_btab TYPE TABLE OF textpool WITH HEADER LINE.

DATA:g_filenm TYPE rlgrap-filename. "文件名称
RANGES r_prog FOR rs38m-programm. "程序名称
*@------------------ ALV 相关的变量 -----------------------------------*
TYPE-POOLS:slis.
DATA: g_repid LIKE sy-repid,
wa_print TYPE slis_print_alv,
gt_list_top_of_page TYPE slis_t_listheader,
gt_events TYPE slis_t_event WITH HEADER LINE,
gt_sort TYPE slis_t_sortinfo_alv,
wa_layout TYPE slis_layout_alv,
gt_fieldcat TYPE slis_t_fieldcat_alv WITH HEADER LINE,
wa_fieldcat LIKE LINE OF gt_fieldcat,
g_save TYPE c,
g_pos TYPE i.
***********************************************************************@
** 宏定义
***********************************************************************@
DEFINE mcr_field.
clear wa_fieldcat.
g_pos = g_pos + 1 .
wa_fieldcat-col_pos = g_pos.
wa_fieldcat-fieldname = &1.
wa_fieldcat-tabname = 'I_RESULT'.

" wa_fieldcat-no_out = 'X'. "field no display, choose from layout
  wa_fieldcat-key = ' '. "SUBTOTAL KEY
  wa_fieldcat-seltext_l = &2.
  wa_fieldcat-outputlen = &3.
  append wa_fieldcat to gt_fieldcat.
  END-OF-DEFINITION.
*@---------------------------------------------------------------------*
*@ MACRO MCR_RANGE 初始化选择条件
*@---------------------------------------------------------------------*
* &1 RANGE 变量
* &2 操作符
* &3 LOW
* &4 HIGH
*----------------------------------------------------------------------*
  DEFINE mcr_range.
  clear &1.
  &1-sign = 'I'.
  &1-option = &2.
  &1-low = &3.
  &1-high = &4.
  append &1.
  END-OF-DEFINITION.
***********************************************************************@
** 屏幕定义
***********************************************************************@
  SELECTION-SCREEN BEGIN OF BLOCK xavery WITH FRAME TITLE text_001.
  SELECTION-SCREEN BEGIN OF LINE.
  SELECTION-SCREEN COMMENT 1(15) text_002 FOR FIELD p_prog.
  PARAMETERS:p_prog TYPE rs38m-programm MEMORY ID rid.
  SELECTION-SCREEN END OF LINE.

SELECTION-SCREEN BEGIN OF LINE.
SELECTION-SCREEN COMMENT 1(15) text_003 FOR FIELD p_cnam.
PARAMETERS:p_cnam TYPE cnam DEFAULT sy-uname.
SELECTION-SCREEN END OF LINE.

SELECTION-SCREEN BEGIN OF LINE.
SELECTION-SCREEN COMMENT 1(15) text_004 FOR FIELD p_filenm.
PARAMETERS:p_filenm TYPE rlgrap-filename.
SELECTION-SCREEN END OF LINE.
SELECTION-SCREEN END OF BLOCK xavery.
***********************************************************************@
** 执行程序事件
***********************************************************************@
INITIALIZATION.
PERFORM f_init_condition.

AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_prog.
PERFORM sub_get_program.

START-OF-SELECTION.
PERFORM sub_query_report.
PERFORM sub_process_report.

END-OF-SELECTION.
PERFORM sub_init_layout.
PERFORM sub_create_fieldcat.
PERFORM sub_display_as_alv. "以ALV的方式输出结果表
*@---------------------------------------------------------------------*
*@ Form F_INIT_CONDITION
*@---------------------------------------------------------------------*
* 初始化选择条件
*----------------------------------------------------------------------*
  FORM f_init_condition .
  text_001 = '查询条件'.
  text_002 = '程序名称'.
  text_003 = '程序创建人'.
  text_004 = '下载文件名称'.
" 选择屏幕初始值
  p_prog = 'Z*'.
  p_filenm = 'C:\ABAP\'.
  ENDFORM. " F_INIT_CONDITION
*&---------------------------------------------------------------------*
*& Form SUB_GET_PROGRAM
*&---------------------------------------------------------------------*
* text
*----------------------------------------------------------------------*
  FORM sub_get_program .
  DATA: repid LIKE sy-repid.
  dynpfields-fieldname = 'P_PROG'.
  APPEND dynpfields.
  repid = sy-repid.
  CALL FUNCTION 'DYNP_VALUES_READ'
  EXPORTING
  dyname = repid
  dynumb = sy-dynnr
  TABLES
  dynpfields = dynpfields
  EXCEPTIONS
  OTHERS.
  READ TABLE dynpfields INDEX 1.
  p_prog = dynpfields-fieldvalue.
  PERFORM program_directory USING p_prog 'X'.
  ENDFORM. " SUB_GET_PROGRAM

*---------------------------------------------------------------------*
* FORM PROGRAM_DIRECTORY *
*---------------------------------------------------------------------*
  FORM program_directory USING programm LIKE rs38m-programm
  f4_call.
  DATA: info_object LIKE euobj-id,
  l_programm LIKE rs38m-programm.
  IF sy-tcode(4) = 'SE38'.
  info_object = 'PROG'.
  IF f4_call = 'X'.
  CALL FUNCTION 'REPOSITORY_INFO_SYSTEM_F4'
  EXPORTING
  object_type = info_object
  object_name = programm
  suppress_selection = 'X'
  IMPORTING
  object_name_selected = programm
  EXCEPTIONS
  cancel = 01.
  ELSE.
  CALL FUNCTION 'REPOSITORY_INFO_SYSTEM'
  EXPORTING
  object_type = info_object
  action = 'S'
  object_name = programm
  IMPORTING
  object_name_selected = programm
  EXCEPTIONS
  cancel = 01
  wrong_type = 02.
  ENDIF.
  ELSE.
  l_programm = programm.
  IF l_programm = space.
  SUBMIT rsabadab AND RETURN VIA SELECTION-SCREEN
  WITH f4_call = f4_call.
  ELSE.
  SUBMIT rsabadab AND RETURN VIA SELECTION-SCREEN
  WITH repname CP l_programm
  WITH f4_call = f4_call.
  ENDIF.
  GET PARAMETER ID 'RID' FIELD p_prog.
  ENDIF.
  ENDFORM. "program_directory
*&---------------------------------------------------------------------*
*& Form SUB_QUERY_REPORT
*&---------------------------------------------------------------------*
* 查询程序代码
*----------------------------------------------------------------------*
  FORM sub_query_report .
  mcr_range r_prog 'CP' p_prog ''.
  SELECT * FROM trdir
  INTO TABLE gt_trdir
  WHERE name IN r_prog AND
  cnam EQ p_cnam AND
  subc NE 'X'.
  ENDFORM. " SUB_QUERY_REPORT
*&---------------------------------------------------------------------*
*& Form SUB_DOWNLOAD_REPORT
*&---------------------------------------------------------------------*
*下载程序代码
*----------------------------------------------------------------------*
  FORM sub_download_report USING l_filenm TYPE rlgrap-filename.
  DATA: binfilesize TYPE i.
  DATA: l_file TYPE string.
  DATA: l_message TYPE char100.
  CONCATENATE '文件已下载到：' p_filenm '文件夹中！'
  INTO l_message.
  l_file = l_filenm.
  CALL FUNCTION 'GUI_DOWNLOAD'
  EXPORTING
  bin_filesize = binfilesize
  filename = l_file
  filetype = 'ASC'
  TABLES
  data_tab = gt_report[]
  EXCEPTIONS
  file_write_error = 1
  no_batch = 2
  gui_refuse_filetransfer = 3
  invalid_type = 4
  no_authority = 5
  unknown_error = 6
  header_not_allowed = 7
  separator_not_allowed = 8
  filesize_not_allowed = 9
  header_too_long = 10
  dp_error_create = 11
  dp_error_send = 12
  dp_error_write = 13
  unknown_dp_error = 14
  access_denied = 15
  dp_out_of_memory = 16
  disk_full = 17
  dp_timeout = 18
  file_not_found = 19
  dataprovider_exception = 20
  control_flush_error = 21
  OTHERS = 22.

IF sy-subrc = 0.
MESSAGE l_message TYPE 'S'.
ENDIF.
ENDFORM. " SUB_DOWNLOAD_REPORT
*&---------------------------------------------------------------------*
*& Form SUB_PROCESS_REPORT
*&---------------------------------------------------------------------*
* 处理文件名称，并放到内表中
*----------------------------------------------------------------------*
  FORM sub_process_report .
  SORT gt_trdir BY name.
  LOOP AT gt_trdir.
  CLEAR gt_result.
  gt_result-name = gt_trdir-name.
  gt_result-cnam = gt_trdir-cnam.
  gt_result-unam = gt_trdir-unam.
  gt_result-tabix = sy-tabix.
  REFRESH gt_btab.
  READ TEXTPOOL gt_trdir-name INTO gt_btab LANGUAGE sy-langu.
  CLEAR gt_btab.
  READ TABLE gt_btab WITH KEY 'R'.
  IF sy-subrc = 0.
  MOVE gt_btab-entry TO gt_result-code.
  ENDIF.
  APPEND gt_result.
  ENDLOOP.
" 清空内表
  FREE gt_trdir.
  ENDFORM. " SUB_PROCESS_REPORT
*&---------------------------------------------------------------------*
*& Form SUB_CREATE_FIELDCAT
*&---------------------------------------------------------------------*
* text
*----------------------------------------------------------------------*
  FORM sub_create_fieldcat .
  CLEAR gt_fieldcat[].
  mcr_field 'TABIX' '顺序号' '10'.
  mcr_field 'NAME' '程序名称' '15'.
  mcr_field 'CNAM' '程序创建人' '20'.
  mcr_field 'UNAM' '最后修改人' '20'.
  mcr_field 'CODE' '程序描述' '30'.
  ENDFORM. " SUB_CREATE_FIELDCAT
*&---------------------------------------------------------------------*
*& Form SUB_INIT_LAYOUT
*&---------------------------------------------------------------------*
* text
*----------------------------------------------------------------------*
  FORM sub_init_layout .
  wa_layout-zebra = 'X'.
  wa_layout-window_titlebar = '开发程序名称清单'.
  wa_layout-colwidth_optimize = 'X'.
  wa_layout-box_fieldname = 'BOX'.
  ENDFORM. " SUB_INIT_LAYOUT
*&---------------------------------------------------------------------*
*& Form SUB_DISPLAY_AS_ALV
*&---------------------------------------------------------------------*
* text
*----------------------------------------------------------------------*
  FORM sub_display_as_alv .
  g_repid = sy-repid.
*ABAP List Viewer
  CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY'
  EXPORTING
  i_callback_program = g_repid
  i_structure_name = 'TYP_RESULT'
  i_callback_user_command = 'SUB_USER_COMMAND'
  i_callback_pf_status_set = 'SUB_SET_PF_STATUS'
  i_save = g_save
  is_layout = wa_layout
  it_fieldcat = gt_fieldcat[]
  TABLES
  t_outtab = gt_result
  EXCEPTIONS
  program_error = 1
  OTHERS = 2.
  ENDFORM. " SUB_DISPLAY_AS_ALV
*@--------------------------------------------------------------------*
*@ Form sub_user_command
*@--------------------------------------------------------------------*
* -->R_UCOMM 事务功能码
* -->RS_SELFIELD ALV相关的数据
*---------------------------------------------------------------------*
  FORM sub_user_command USING r_ucomm LIKE sy-ucomm
  rs_selfield TYPE slis_selfield.

CASE r_ucomm.
WHEN '&IC1'. "双击事件的功能码
WHEN 'DOWNLOAD'. "下载代码
PERFORM sub_ucomm_down.
ENDCASE.

* 刷新ALV屏幕报表
  rs_selfield-refresh = 'X'.
  ENDFORM. "sub_user_command
*@---------------------------------------------------------------------*
*@ FORM SUB_SET_PF_STATUS *
*@---------------------------------------------------------------------*
* 设置ALV菜单
* 通过SE41，拷贝程序SAPLSLVC_FULLSCREEN的状态STANDARD_FULLSCREEN过来
*@---------------------------------------------------------------------*
  FORM sub_set_pf_status USING rt_extab TYPE slis_t_extab.
  SET PF-STATUS 'STANDARD_FULLSCREEN'.
  ENDFORM. "sub_set_pf_status
*&---------------------------------------------------------------------*
*& Form SUB_UCOMM_DOWN
*&---------------------------------------------------------------------*
* 下载选中的行的程序代码
*----------------------------------------------------------------------*
  FORM sub_ucomm_down .
  LOOP AT gt_result WHERE box = 'X'.
* 得到文件名称
  CLEAR g_filenm.
  CONCATENATE p_filenm gt_result-name '-'
  gt_result-code '.txt'
  INTO g_filenm.
* 得到程序代码
  CLEAR gt_report[].
  READ REPORT gt_result-name INTO gt_report.
* 下载程序
  PERFORM sub_download_report USING g_filenm.
  ENDLOOP.
  ENDFORM. " SUB_UCOMM_DOWN
```

