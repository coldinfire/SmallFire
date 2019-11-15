---
title: "ABAP DOI使用"
date: 2019-02-22
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
  - DOI

---


## 概述 ##
   DOI（Desktop office Integration）采用OO的思想实现与Office的结合使用。

   可以先上传模板到服务器(OAOR)，然后对模板进行填充。也可以通过代码创建ExceL文档。

   OAOR：class name, class type，object key。

   会使用到的对象：TYPE-POOLS:vrm, sbdst, soi.

   重要对象：

  - container: 存放excel电子表格(spreadsheet)的容器。spreadsheet需要一个容器来存放。
  - container control: 容器中用于创建和管理其他Office集成所需要的对象。container control是一个接口，类型是i_oi_container_control。
  - document proxy: 每一个document proxy的实例代表用office application打开的档,excel，    word。如果想打开多个文档，需要定义多个实例，类型为i_oi_document_proxy。
  - spreadsheet: spreadsheet接口，代表最终要操作的excel文档。类型是   i_oi_spreadsheet
  - errors：异常的处理，保存操作时的异常，类型是 i_oi_error。

如果读取服务器上的文档模板，需要cl_bds_document_set类：

  - business document set：business document set用于管理后续要操作的文档，可以包含一个或多个文档。

  操作步骤(OAOR上传文档后)

   1. 获取container
   2. 创建container control对象实例并初始化
   3. 创建document proxy对象的实例
   4. 打开一个服务器上的模板文档或新建一个excel文档
   5. 操作excel文档，设置excel的属性
   6. 退出时关闭excel文档，释放资源
   7. 异常管理

## 定义和使用 ##
### 字段定义： ###

```JS
* SAP Desktop Office Integration Interfaces
DATA: cl_container type ref to cl_gui_container,
      cl_control type ref to i_oi_container_control,
      cl_document type ref to i_oi_document_proxy,
      cl_spreadsheet type ref to i_oi_spreadsheet，
      cl_error    TYPE REF TO i_oi_error，
      cl_errors  TYPE REF TO i_oi_error OCCURS 0 WITH HEADER LINE.
* Spreadsheet interface structures for Excel data input
DATA: wa_cellitem    TYPE soi_generic_item,
      wa_rangeitem   TYPE soi_range_item,
      gt_ranges      TYPE soi_range_list,
      gt_excel_input TYPE soi_generic_table,
      wa_excel_input TYPE soi_generic_item,
      g_initialized  type c,
      g_retcode      TYPE soi_ret_string,
      gt_excel_format TYPE soi_format_table,
      wa_format      LIKE LINE OF gt_excel_format.
DATA: gt_itab     TYPE TABLE OF alsmex_tabline WITH HEADER LINE,
      gt_imt_tab  TYPE TABLE OF typ_area_excel,
      wa_imt_tab  LIKE LINE OF gt_imt_tab,
      g_macro     TYPE text100,
      g_sheet(10) TYPE c,
      g_cell_fit  TYPE c.
DATA: cl_bds_instance   TYPE REF TO cl_bds_document_set,
      gt_doc_signature  TYPE sbdst_signature,
      wa_doc_signature  LIKE LINE OF gt_doc_signature,
      gt_doc_components TYPE sbdst_components,
      gt_doc_uris       TYPE sbdst_uri,
      wa_doc_uris       LIKE LINE OF gt_doc_uris.
DATA: g_app      TYPE vrm_id,
      gt_applist TYPE vrm_values,
      g_excel    TYPE text80 VALUE 'Excel.Sheet',       "EXCEL的表单
      g_docu_type TYPE text80,
      g_url(256)  TYPE c,
      g_has_activex TYPE c,
      g_col TYPE i,         "字段所在的列数
      g_row TYPE i.         "字段所在的行数
```
以下三个值为Tcode:OAOR里面新建模板文件的参数

 DATA: g_classname  TYPE sbdst_classname VALUE 'HRFPM_EXCEL_STANDARD',

   g_classtype  TYPE sbdst_classtype VALUE 'OT',

   g_object_key TYPE sbdst_object_key VALUE 'ZTEST'.

### 开始创建DOI: ###
```JS
*===== Get_container (动态Container)
CREATE OBJECT cl_splitter
    EXPORTING
        parent  = cl_gui_container=>screen0
        rows    = 1
        columns = 1.
CALL METHOD cl_splitter->set_border
    EXPORTING
        border = cl_gui_cfw=>false.
        cl_container  = cl_splitter->get_container( row = 1 column = 1 ).
*===== Get the SAP DOI  i_oi_container_control interface
CALL METHOD c_oi_container_control_creator=>get_container_control
    IMPORTING
      control = cl_control
      error   = cl_error.
* check no errors occured
CALL METHOD cl_error->raise_message
    EXPORTING
      type = 'E'.
*===== Initialize the SAP DOI Container, tell it to run in the container
call method gr_control->init_control
    exporting
      inplace_enabled          = 'X '
      inplace_scroll_documents = 'X'
      register_on_close_event  = 'X'
      register_on_custom_event = 'X'
      r3_application_name      = 'DOI Test'
      parent                  = cl_container
      no_flush      =   'X'
    IMPORTING
      error                    = cl_errors.
* save error object in collection
  APPEND cl_errors.
*===== Ask the SAP DOI container for a i_oi_document_proxy for Excel
CALL METHOD cl_control->get_document_proxy
    EXPORTING
      document_type      = 'Excel.Sheet'
      no_flush           = 'X'
*    REGISTER_CONTAINER = 'X'
    IMPORTING
      document_proxy     = cl_docu_proxy
      error              = cl_errors.
  APPEND cl_errors.
```

### 对Excel进行操作： ###
```JS
 wa_doc_signature-prop_name = 'DESCRIPTION'.
 wa_doc_signature-prop_value = 'PP_REPORT'.
APPEND wa_doc_signature TO gt_doc_signature.
"===== Create_excel_document
call method gr_control->get_document_proxy
  exporting
    document_type  = 'Excel.Sheet'
    no_flush      = 'X'
  importing
    document_proxy = gr_document.
call method gr_document->create_document
  exporting
    document_title = 'DOI test by Stone Wang '
    no_flush      = 'X '
    open_inplace  = 'X'.
```
### 操作模板文档：

  操作excel模板文档，使用cl_bds_document_set类，这个类的get_with_url方法获取文档的url

```JS
*Business document system
*=====get_template_url
create object gr_bds_documents.
  call method cl_bds_document_set=>get_info
    exporting
      classname  = g_classname
      classtype  = g_classtype
      object_key = g_objectkey
    changing
      components = g_doc_components
      signature  = g_doc_signature.
call method cl_bds_document_set=>get_with_url
  exporting
    classname  = g_classname
    classtype  = g_classtype
    object_key = g_objectkey
  changing
    uris      = gt_bds_uris
    signature  = g_doc_signature.
 free gr_bds_documents.
 read table gt_bds_uris into gs_bds_url index 1.
 g_url = gs_bds_url-uri.
   <cl_bds_document_set> 的静态方法get_with_url获取excel template的url。数据存放在内表中，
读取后放在global变量g_template_url里面。

"Open the excel
call method gr_control->get_document_proxy
   exporting
     document_type      = 'Excel.Sheet'
     no_flush          = 'X'
     register_container = 'X'
   importing
     document_proxy    = gr_document.
call method gr_document->open_document
   exporting
     open_inplace = 'X'
     document_url = g_template_url.
 data: available type i.
call method gr_document->has_spreadsheet_interface
   exporting
     no_flush    = 'X'
   importing
     is_available = available.
call method gr_document->get_spreadsheet_interface
   exporting
     no_flush        = 'X'
   importing
     sheet_interface = gr_spreadsheet.
CALL method spreadsheet->select_sheet
   exporting
     name    = 'Sheet1'
     no_flush = ''
   importing
     error    = error
     retcode  = retcode.
CALL method spreadsheet->get_active_sheet
    exporting
      no_flush  = ''
    importing
      sheetname = sheetname
      error    = error
      retcode  = retcode.
CALL method spreadsheet->add_sheet
   exporting
     name    = '年度报表'
     no_flush = ''
   importing
     error    = error
     retcode  = retcode.
CALL method spreadsheet->delete_sheet
   exporting
     name    = sheetname
     no_flush = ''
   importing
     error    = error
     retcode  = retcode.
```
---------------------

作者：SAPmatinal

来源：CSDN

原文：https://blog.csdn.net/SAPmatinal/article/details/52776862

### 数据写入Excel: ###
   数据写入Excel，可以使用批量的方式或者逐个单元格写入的方式。批量写入的方式效率高，逐个单元格写入的方式比较灵活。将数据写入excel需要使用 i_oi_spreadsheet接口实例的两个方法：

```JS
* insert_range_dim 方法，定义一个范围(range)，设定range的名称、位置和大小。
CALL method gr_spreadsheet->insert_range_dim
 exporting
    name    = 'cell'
    no_flush = 'X'
    top      = 2
    left    = 1
    rows    = line_count
    columns  = 4.

* set_range_data 方法，写入数据到range，写入的时候，ranges参数设定range的名称和大小, contents参数
设定写入的内容。
CALL methodgr_spreadsheet->set_ranges_data
 exporting
    ranges=gt_ranges
    contents=gt_contents
    no_flush= 'X'.

Ex:*界定ranges范围
CALL METHOD R_HANDLE_EXCEL->INSERT_RANGE_DIM
  EXPORTING
      NAME= 'title'
      NO_FLUSH= 'X'
      TOP= 1
      LEFT     = 1
      ROWS     = 1
      COLUMNS= LV_TOTAL_COLUMNS.
  WA_RANGES-NAME= 'title'.
  WA_RANGES-ROWS = 1.
  WA_RANGES-COLUMNS= LV_TOTAL_COLUMNS.
  APPEND WA_RANGESTO IT_RANGES.
DATA LV_ROWTYPE I.
DATA LV_COLUMNTYPE I.
FIELD-SYMBOLS .
DATA LWA_FIELDCATTYPE SLIS_FIELDCAT_ALV.
CLEAR LV_COLUMN.
LOOP AT IT_FIELDCATINTO LWA_FIELDCAT.
  LV_COLUMN= LV_COLUMN +1.
  WA_CONTENTS-ROW= 1.
  WA_CONTENTS-COLUMN= LV_COLUMN.
  WA_CONTENTS-VALUE = LWA_FIELDCAT-SELTEXT_L.
  APPEND WA_CONTENTSTO IT_CONTENTS.
ENDLOOP.

* Set data
CALL METHOD R_HANDLE_EXCEL->SET_RANGES_DATA
  EXPORTING
    RANGES   = IT_RANGE
    CONTENTS= IT_CONTENTS
    NO_FLUSH= 'X'.
CALL METHOD R_HANDLE_EXCEL->FIT_WIDEST
  EXPORTING
    NAME= SPACE
    NO_FLUSH= 'X'.
```

### 对象销毁： ###
PAI的exit-command事件中对spreadsheet, control和container等对象的销毁。

```JS
module exit_program input.
  save_ok = ok_code.
  clear ok_code.
  if save_ok = 'EXIT'.
    if not gr_document is initial.
      call method gr_document->close_document.
      free gr_document.
    endif.
    if not gr_control is initial.
      call method gr_control->destroy_control.
      free gr_control.
    endif.
    if gr_container is not initial.
      call method gr_container->free.
    endif.
    leave program.
  endif.
endmodule.
```
## 其他实现 ##
### 1. 如何根据屏幕大小让 Excel 自适应 ###
```JS
data: cl_container type ref to cl_gui_container,
      cl_splitter type ref to cl_gui_splitter_container,
    ......
form get_dynamic_container.
  create object gr_splitter
    exporting
      parent      = cl_gui_container=>screen0
      rows        = 1
      columns  = 1 .
  call method gr_splitter->set_border
    exporting
      border = cl_gui_cfw=>false.
  gr_container = gr_splitter->get_container( row = 1 column = 1 ).
endform.
container control 初始化的时候，设定 parent 为 gr_container
```
### 2. Excel 单个单元格写入 ###
   单个单元格写入的方法，同批量写入一样，使用 i_oi_spreadsheet 接口的set_range_dim 方法和 set_range_data方法。区别在于 range 只包含一行一列：

```JS
form write_single_cell using p_row p_col p_value.
* define internal table for ranges and contents parameters
  data: lt_ranges type soi_range_list,
        ls_rangeitem type soi_range_item,
        lt_contents type soi_generic_table,
        ls_content type soi_generic_item.
* populate ranges
  clear ls_rangeitem.
  clear lt_ranges[].
  ls_rangeitem-name = 'cell' .
  ls_rangeitem-columns = 1.
  ls_rangeitem-rows = 1.
  ls_rangeitem-code = 4.
  append ls_rangeitem to lt_ranges.
* populate contents
  clear ls_content.
  clear lt_contents[].
  ls_content-column = 1.
  ls_content-row = 1.
  ls_content-value = p_value.
  append ls_content to lt_contents.
* 每次只写一行一列
  call method gr_spreadsheet->insert_range_dim
    exporting
      name    = 'cell'
      no_flush = 'X'
      top      = p_row
      left    = p_col
      rows    = 1
      columns  = 1.
  call method gr_spreadsheet->set_ranges_data
    exporting
      ranges  = lt_ranges
      contents = lt_contents
      no_flush = 'X'.
endform.   

循环写入：
loop at gt_spfli into gs_spfli.
   row_index = sy-tabix + 1.
   perform write_single_cell using row_index 1 gs_spfli-carrid.
   perform write_single_cell using row_index 2 gs_spfli-connid.
   perform write_single_cell using row_index 3 gs_spfli-cityfrom.
   perform write_single_cell using row_index 4 gs_spfli-cityto.
   clear gs_spfli.
endloop.
```

### 3. 设置 Excel 属性 ###
```JS
form set_excel_attributes.
* 修改WORK SHEET 的名字
  CALL METHOD cl_spreadsheet->set_sheet_name
    EXPORTING
      newname = '物料主数据清单'
      oldname = 'Sheet1'
    IMPORTING
      error   = cl_errors.
* set border line for range
  call method gr_spreadsheet->set_frame
    exporting
      rangename = 'cell'
      typ      = '127'
      color    = '1'
      no_flush  = 'X'.
* auto fit
  call method gr_spreadsheet->fit_widest
    exporting
      name    = space
      no_flush = 'X'.
endform.   
```

### 4. 错误处理 ###
通常有两种方法来处理错误：

 第一种方法：使用 c_oi_errors 的静态方法 raise_message 简单地显示相关的错误：
```JS
CALL METHODC_OI_ERRORS=>RAISE_MESSAGE
    EXPORTING
       TYPE = type
 type 可以是 A, E, W, I, S 其中之一。
```
  第二种方法是区分不同的错误，给用户一个更明确的提示：
```JS
IF ret_code EQ c_oi_errors=>ret_ok.
  " Document opened successfully
ELSEIF ret_code EQ c_oi_errors=> ret_document_already_open.
  " Special error handling, e.g. dialog box.
ELSE.
  CALL METHOD c_oi_errors=>raise_message
     EXPORTING type = 'E'.
ENDIF.
```
也可以把 ret_code 返回的错误码先储存在内表中，集中处理：
```JS
DATA: errors TYPE REF TO i_oi_error OCCURS 0 WITH HEADER LINE.
* DOI processing
CALL METHOD control->get_link_server
  EXPORTING server_type = server_type
    no_flush = 'X'
  IMPORTING link_server = link_server
    retcode = retcode
    error = errors.
APPEND errors.
LOOP AT errors. 
  CALL METHOD errors->raise_message
    EXPORTING
	  type = 'E'
    EXCEPTIONS 
      message_raised = 1
      flush_failed = 2.
ENDLOOP.
FREE errors.
```
原文：https://blog.csdn.net/stone0823/article/details/53819960

