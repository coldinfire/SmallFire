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
DOI（Desktop office Integration）采用 OO 的思想实现与 Office 的结合使用。SAP 标准DEMO： SAPRDEMOEXCEL_EXPORT、SAPRDEMO_DOI_BDS。

文档使用方式：

- 先上传模板到服务器(OAOR)，然后对模板进行填充
  - OAOR：class name、class type、object key
- 通过代码直接创建 ExceL / Word 文档

#### 核心对象

- container：类型是 `cl_gui_custom_container`；存放 Excel 电子表格(spreadsheet)的容器，spreadsheet 需要一个容器来存放，标准的 container 接口的类型是 `cl_gui_container`
- container control：类型是 `i_oi_container_control`；容器中用于创建和管理其他 Office 集成所需要的对象
- document proxy：类型是 `i_oi_document_proxy`；每一个 document proxy 的实例代表用 office application 打开的文档。如果想打开多个文档，需要定义多个实例
- spreadsheet: 类型是`i_oi_spreadsheet`；spreadsheet 接口代表最终要操作的 excel 文档。
- errors：类型是 `i_oi_error`；异常的处理，保存操作时产生的异常信息
- retcode ：类型是 `soi_ret_string`；执行方法时返回的消息字符串
- splitter container：类型是`cl_gui_splitter_container`；动态的可以拆分的容器，可存放多个标准的(cl_gui_container)电子表格

如果读取服务器上的文档模板，需要 `cl_bds_document_set` 类：

  - business document set：类型`cl_bds_document_set`，用于管理后续要操作的文档，可以包含一个或多个文档。
  - link server：类型`i_oi_link_server`，连接服务器对象

####   操作 Excel 步骤 (OAOR上传文档后)

   1. 获取 container 容器
   2. 创建 container control 对象实例并初始化
   3. 创建 document proxy 对象的实例
   4. 打开一个服务器上的模板文档或新建一个 excel 文档
   5. 操作 excel 文档，设置 excel 的属性
   6. 退出时关闭 excel 文档，释放资源
   7. 异常管理

## DOI 定义和使用 ##
### 字段定义 ###

```ABAP
TYPE-POOLS: slis,soi,sbdst,vrm.
CLASS c_oi_errors DEFINITION LOAD.
TYPES:t_url LIKE bapiuri-uri.
DATA: fcode LIKE sy-ucomm.
* SAP Desktop Office Integration Interfaces
DATA: container TYPE REF TO cl_gui_container,
      custom_container TYPE REF TO cl_gui_custom_container,
      control   TYPE REF TO i_oi_container_control,
*      document  TYPE REF TO i_oi_document_proxy,
      spreadsheet_interface TYPE REF TO i_oi_spreadsheet,
      error     TYPE REF TO i_oi_error,
      retcode   TYPE soi_ret_string,
      splitter  TYPE REF TO cl_gui_splitter_container.
* Spreadsheet Option Paramter
DATA: document_type TYPE soi_document_type,
      document_format TYPE soi_document_type,
      doc_url TYPE t_url.

DATA: is_open_inplace  TYPE i VALUE 0.
DATA: is_open_for_edit TYPE i VALUE 0.
DATA: is_available     TYPE i.
* Spreadsheet interface structures for Excel data input
DATA: wa_cellitem    TYPE soi_generic_item,
      wa_rangeitem   TYPE soi_range_item,
      gt_ranges      TYPE soi_range_list,
      gt_excel_input TYPE soi_generic_table,
      wa_excel_input TYPE soi_generic_item,
      g_initialized  TYPE c,
      gt_excel_format TYPE soi_format_table,
      wa_format      LIKE LINE OF gt_excel_format.
```
OAOR  模板方法参数定义

```ABAP
* OAOR Template parameter
DATA: g_classname  TYPE sbdst_classname VALUE 'HRFPM_EXCEL_STANDARD',
      g_classtype  TYPE sbdst_classtype VALUE 'OT',
      g_object_key TYPE sbdst_object_key VALUE 'ZTEST'.
"BDS Method Parameter"
DATA: bds_instance   TYPE REF TO cl_bds_document_set,
      link_server    TYPE REF TO i_oi_link_server,
      gt_doc_signature  TYPE sbdst_signature,
      wa_doc_signature  LIKE LINE OF gt_doc_signature,
      gt_doc_components TYPE sbdst_components,
      gt_doc_uris       TYPE sbdst_uri,
      wa_doc_uris       LIKE LINE OF gt_doc_uris.
```

### 创建 DOI ###
```ABAP
CALL SCREEN 100.
"Screen 100 PBO & PAI"
MODULE status_0100 OUTPUT.
  SET PF-STATUS 'MAIN0100'.
  SET TITLEBAR '001'.
  PERFORM get_dynamic_container.
  PERFORM get_control.
ENDMODULE.                 " STATUS_0100  OUTPUT "
MODULE user_command_0100 INPUT.
  ...
ENDMODULE.                 " USER_COMMAND_0100  INPUT "
"DOI Define"
FORM get_dynamic_container.
  CREATE OBJECT splitter
    EXPORTING
      parent  = cl_gui_container=>screen0
      rows    = 1
      columns = 1.
  CALL METHOD splitter->set_border
    EXPORTING
      border = cl_gui_cfw=>false.
  container = splitter->get_container( row = 1 column = 1 ).
ENDFORM.                    " GET_DYNAMIC_CONTAINER "

FORM get_control .
  IF control IS INITIAL.
    DATA:l_has_activex.
    CALL FUNCTION 'GUI_HAS_ACTIVEX'
      IMPORTING
        return = l_has_activex.
    IF l_has_activex IS INITIAL.
      MESSAGE 'Use a Windows GUI for this program' TYPE 'E'.
    ENDIF.
*===== Get container (静态Container) =====*
    CREATE OBJECT custom_container
      EXPORTING
        container_name = 'DOI_PARENT'. "100屏幕上的控件名."
*===== Get the SAP DOI container control interface =====*
    CALL METHOD c_oi_container_control_creator=>get_container_control
      IMPORTING
        control = control
        error   = error.
   "check no errors occured"
    CALL METHOD error->raise_message
      EXPORTING
        type = 'E'.
*===== Initialize the SAP DOI Container, tell it to run in the container =====*
    CALL METHOD control->init_control
      EXPORTING
        inplace_enabled          = 'X'
        inplace_scroll_documents = 'X'
        register_on_close_event  = 'X'
        register_on_custom_event = 'X'
        r3_application_name      = sy-repid
        parent                   = custom_container
        no_flush                 = 'X'
      IMPORTING
        error                    = error.
    CALL METHOD error->raise_message
      EXPORTING
        type = 'E'.
  ENDIF.
ENDFORM.                    " GET_CONTROL "
```

### 对 Document 进行操作 ###

#### 自定义类并封装标准的方法

```ABAP
*---------------------------------------------------------------------*
*       CLASS c_office_document DEFINITION
*---------------------------------------------------------------------*
CLASS c_office_document DEFINITION.
  PUBLIC SECTION.
    DATA: proxy TYPE REF TO i_oi_document_proxy.
    DATA: document_type TYPE soi_document_type.
    DATA: document_format TYPE soi_document_type.
    DATA: has_changed_at_reopen TYPE i.
    DATA: data_table TYPE sbdst_content,
          data_size TYPE i,
          doc_url TYPE t_url.
*   Constructor
    METHODS: constructor
              IMPORTING control TYPE REF TO i_oi_container_control
                        document_type TYPE c
                        document_format TYPE c.
*   Close Event Processor
    METHODS: on_close_document
              FOR EVENT on_close_document OF i_oi_document_proxy
              IMPORTING document_proxy has_changed.
*   Create Docuent
    METHODS: create_document
                  IMPORTING open_inplace  TYPE c DEFAULT ' '
                  EXPORTING retcode TYPE soi_ret_string.
*   Open Docuent
    METHODS: open_document
                  IMPORTING open_inplace  TYPE c DEFAULT ' '
                            open_readonly TYPE c DEFAULT ' '
                  EXPORTING retcode TYPE soi_ret_string.
*   Re-Open Docuent
    METHODS: reopen_document
                  IMPORTING open_inplace  TYPE c DEFAULT ' '
                  EXPORTING retcode TYPE soi_ret_string.
    METHODS: view_document
                  EXPORTING retcode TYPE soi_ret_string.
*   Close Docuent
    METHODS: close_document
                  IMPORTING do_save     TYPE c DEFAULT ' '
                  EXPORTING retcode     TYPE soi_ret_string
                            error       TYPE REF TO i_oi_error.
*   Check for availability of Spreadsheet interface
    METHODS: has_spreadsheet_interface
                  EXPORTING is_available TYPE i
                            retcode      TYPE soi_ret_string
                            error        TYPE REF TO i_oi_error.
*   Get the Spreadsheet interface
    METHODS: get_spreadsheet_interface
                  EXPORTING retcode         TYPE soi_ret_string
                            error           TYPE REF TO i_oi_error.
*   Method in Spreadsheet interface to set the data for ranges in Excel
    METHODS: set_ranges_data
                 IMPORTING ranges    TYPE soi_range_list
                           contents  TYPE soi_generic_table
                 EXPORTING error     TYPE REF TO i_oi_error
                           retcode   TYPE soi_ret_string.
*   Method in Spreadsheet interface to set the ranges in Excel
    METHODS: insert_range
                IMPORTING rows    TYPE i
                          columns TYPE i
                          name    TYPE c
                EXPORTING error   TYPE REF TO i_oi_error
                          retcode TYPE soi_ret_string.
*   Save the document at given URL.
    METHODS: save_document_to_url
                 IMPORTING url         TYPE t_url
                 EXPORTING error       TYPE REF TO i_oi_error
                           retcode     TYPE soi_ret_string
                 CHANGING  document_size TYPE i.
  PRIVATE SECTION.
    DATA: control  TYPE REF TO i_oi_container_control.
ENDCLASS.                    "c_office_document DEFINITION"

DATA: document TYPE REF TO c_office_document.

************************************************************************
*  CLASS c_office_document IMPLEMENTATION.
************************************************************************
CLASS c_office_document IMPLEMENTATION.
  METHOD: constructor.
*              IMPORTING control TYPE REF TO i_oi_container_control
*                        document_type TYPE soi_document_type
*                        document_format TYPE soi_document_type
    me->control = control.
    me->document_type = document_type.
    me->document_format = document_format.
  ENDMETHOD.                    "constructor"
  METHOD create_document.
*                 IMPORTING open_inplace  TYPE c DEFAULT ' '
*                 RETURNING value(retcode) TYPE t_oi_ret_string.
    IF NOT proxy IS INITIAL.
      CALL METHOD me->close_document.
    ENDIF.
    CALL METHOD control->get_document_proxy
      EXPORTING
        document_type   = document_type
        document_format = document_format
      IMPORTING
        document_proxy  = proxy
        retcode         = retcode.
    IF retcode NE c_oi_errors=>ret_ok.
      EXIT.
    ENDIF.
    CALL METHOD proxy->create_document
      EXPORTING
        create_view_data = 'X'
        open_inplace     = open_inplace
      IMPORTING
        retcode          = retcode.
    IF retcode NE c_oi_errors=>ret_ok.
      EXIT.
    ENDIF.
    SET HANDLER me->on_close_document FOR proxy.
  ENDMETHOD.                    "create_document"
  METHOD open_document.
*                 IMPORTING open_inplace  TYPE c DEFAULT ' '
*                           open_readonly TYPE c DEFAULT ' '
*                 RETURNING value(retcode) TYPE t_oi_ret_string.
    IF NOT proxy IS INITIAL.
      CALL METHOD me->close_document.
    ENDIF.
    CALL METHOD control->get_document_proxy
      EXPORTING
        document_type   = document_type
        document_format = document_format
      IMPORTING
        document_proxy  = proxy
        retcode         = retcode.
    IF retcode NE c_oi_errors=>ret_ok.
      EXIT.
    ENDIF.
    CALL METHOD proxy->open_document_from_table
      EXPORTING
        document_table = data_table
        document_size  = data_size
        open_inplace   = open_inplace
        open_readonly  = open_readonly
      IMPORTING
        retcode        = retcode.
    IF retcode NE c_oi_errors=>ret_ok.
      EXIT.
    ENDIF.
    SET HANDLER me->on_close_document FOR proxy.
  ENDMETHOD.                    "open_document"
  METHOD reopen_document.
*                 IMPORTING open_inplace  TYPE c DEFAULT ' '
*                 RETURNING value(retcode) TYPE t_oi_ret_string.
    DATA: l_has_changed TYPE i.
    CALL METHOD proxy->reopen_document
      EXPORTING
        open_inplace = open_inplace
      IMPORTING
        has_changed  = l_has_changed
        retcode      = retcode.
    IF retcode NE c_oi_errors=>ret_ok.
      EXIT.
    ENDIF.
    IF NOT l_has_changed IS INITIAL.
      has_changed_at_reopen = l_has_changed.
    ENDIF.
    SET HANDLER me->on_close_document FOR proxy.
  ENDMETHOD.                    "reopen_document"
  METHOD view_document.
*                 RETURNING value(retcode) TYPE t_oi_ret_string.
    IF NOT proxy IS INITIAL.
      CALL METHOD me->close_document.
    ENDIF.
    CALL METHOD control->get_document_proxy
      EXPORTING
        document_type   = document_type
        document_format = document_format
      IMPORTING
        document_proxy  = proxy
        retcode         = retcode.
    IF retcode NE c_oi_errors=>ret_ok.
      EXIT.
    ENDIF.
    CALL METHOD proxy->view_document_from_table
      EXPORTING
        document_table = data_table
        document_size  = data_size
        open_inplace   = 'X'
      IMPORTING
        retcode        = retcode.
    IF retcode NE c_oi_errors=>ret_ok.
      EXIT.
    ENDIF.
    SET HANDLER me->on_close_document FOR proxy.
  ENDMETHOD.                    "view_document"
  METHOD close_document.
*                 IMPORTING do_save TYPE c DEFAULT ' '
*                 RETURNING value(retcode) TYPE t_oi_ret_string.
    DATA: has_changed TYPE i, is_closed TYPE i.
    DATA: save_error TYPE REF TO i_oi_error.
    IF NOT proxy IS INITIAL.
      CALL METHOD proxy->is_destroyed
        IMPORTING
          ret_value = is_closed.
      IF is_closed IS INITIAL.
        CALL METHOD proxy->close_document
          EXPORTING
            do_save     = do_save
          IMPORTING
            has_changed = has_changed
            retcode     = retcode
            error       = error.
        IF retcode NE c_oi_errors=>ret_ok.
          EXIT.
        ENDIF.
      ENDIF.
      IF NOT has_changed IS INITIAL OR
                       ( NOT has_changed_at_reopen IS INITIAL AND
                         NOT do_save IS INITIAL ).
        CALL METHOD proxy->save_document_to_table
          EXPORTING
            no_flush       = 'X'
          IMPORTING
            error          = save_error
          CHANGING
            document_table = data_table
            document_size  = data_size.
      ENDIF.
      CALL METHOD proxy->release_document
        IMPORTING
          retcode = retcode
          error   = error.
      IF NOT save_error IS INITIAL.
        IF save_error->error_code NE c_oi_errors=>ret_ok.
          CALL METHOD save_error->flush_error.
          retcode = save_error->error_code.
          error = save_error.
        ENDIF.
      ENDIF.
      CLEAR: has_changed_at_reopen.
      SET HANDLER me->on_close_document FOR proxy ACTIVATION ' '.
    ELSE.
      retcode = c_oi_errors=>ret_document_not_open.
    ENDIF.
  ENDMETHOD.                    "close_document"
  METHOD on_close_document.
*              FOR EVENT on_close_document OF c_oi_container_control
*              IMPORTING document_proxy has_changed.
    DATA: answer, do_save.
    DATA: save_error TYPE REF TO i_oi_error.
    is_open_inplace = 0. is_open_for_edit = 0.
    IF NOT has_changed IS INITIAL OR NOT has_changed_at_reopen IS INITIAL.
      do_save = 'X'.
      CALL METHOD me->close_document
        EXPORTING
          do_save = do_save
        IMPORTING
          error   = save_error.
      IF NOT save_error IS INITIAL.
        CALL METHOD save_error->raise_message
          EXPORTING
            type = 'E'.
      ENDIF.
    ENDIF.
  ENDMETHOD.                    "on_close_document"
  METHOD has_spreadsheet_interface.
*                  EXPORTING is_available TYPE i
*                            retcode      TYPE soi_ret_string
*                            error        TYPE REF TO i_oi_error.
    CALL METHOD proxy->has_spreadsheet_interface
      IMPORTING
        is_available = is_available
        error        = error
        retcode      = retcode.
    IF retcode NE c_oi_errors=>ret_ok.
      EXIT.
    ENDIF.
  ENDMETHOD.                    "has_spreadsheet_interface"
  METHOD get_spreadsheet_interface.
*                 EXPORTING sheet_interface TYPE REF to i_oi_spreadsheet
*                           retcode         TYPE soi_ret_string
*                           error           TYPE REF TO i_oi_error.
    CALL METHOD proxy->get_spreadsheet_interface
      IMPORTING
        sheet_interface = spreadsheet_interface
        error           = error
        retcode         = retcode.
    IF retcode NE c_oi_errors=>ret_ok.
      EXIT.
    ENDIF.
  ENDMETHOD.                    "get_spreadsheet_interface"
  METHOD set_ranges_data.
*                 IMPORTING ranges    TYPE soi_range_list
*                           contents  TYPE soi_generic_table
*                 EXPORTING error     TYPE REF TO i_oi_error
*                           retcode   TYPE soi_ret_string.
    CALL METHOD spreadsheet_interface->set_ranges_data
      EXPORTING
        ranges   = ranges
        contents = contents
      IMPORTING
        error    = error
        retcode  = retcode.
    IF retcode NE c_oi_errors=>ret_ok.
      EXIT.
    ENDIF.
  ENDMETHOD.                    "set_ranges_data"
  METHOD insert_range.
*                IMPORTING rows    TYPE i
*                          columns TYPE i
*                          name    TYPE c
*                EXPORTING error   TYPE REF TO i_oi_error
*                          retcode TYPE soi_ret_string.
    CALL METHOD spreadsheet_interface->insert_range
      EXPORTING
        name    = name
        rows    = rows
        columns = columns
      IMPORTING
        error   = error
        retcode = retcode.
    IF retcode NE c_oi_errors=>ret_ok.
      EXIT.
    ENDIF.
  ENDMETHOD.                    "insert_range"
  METHOD save_document_to_url.
    CALL METHOD proxy->save_document_to_url
      EXPORTING
        url           = url
      IMPORTING
        retcode       = retcode
      CHANGING
        document_size = document_size.
    IF retcode NE c_oi_errors=>ret_ok.
      EXIT.
    ENDIF.
  ENDMETHOD.                    "save_document_to_url"
ENDCLASS.                    "c_office_document IMPLEMENTATION"
```

#### 使用自定义类操作 Document

```JS
MODULE user_command_0100 INPUT.
  ...
ENDMODULE.                 " USER_COMMAND_0100  INPUT "

*===== Create_excel_document
call method control->get_document_proxy
  exporting
    document_type  = 'Excel.Sheet'
    no_flush      = 'X'
  importing
    document_proxy = gr_document.
call method document->create_document
  exporting
    document_title = 'DOI test by Stone Wang '
    no_flush      = 'X '
    open_inplace  = 'X'.
```
#### 操作模板文档：

操作 excel 模板文档，使用 cl_bds_document_set 类，这个类的 get_with_url 方法获取文档的 url。

```JS
* Business document system
*===== Server Link Check =====*
CALL METHOD control->get_link_server
  EXPORTING
    no_flush    = 'X'
  IMPORTING
    link_server = link_server
    error       = error.
CALL METHOD control->init_control
  EXPORTING
    r3_application_name   = sy-repid      
    parent                = customer_container
    inplace_show_toolbars = ''.
CALL METHOD go_link_server->start_link_server
  EXPORTING
    no_flush = 'X'
  IMPORTING
    error    = error.
*===== Create Document =====*
CREATE OBJECT document
  EXPORTING
    control        = control
    document_type  = document_type
    document_format = document_format.
IF bds_instance IS INITIAL.
  CREATE OBJECT bds_instance.
ENDIF.
*===== =====*
wa_doc_signature-prop_name  = 'DESCRIPTION'.
wa_doc_signature-prop_value = 'ZFBL5NP'.
APPEND wa_doc_signature TO gt_doc_signature.
CLEAR g_wa_doc_signature.
CLEAR: gt_doc_uris[],wa_doc_uris.
*===== Get template url =====*
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
    uris       = gt_bds_uris
    signature  = g_doc_signature.
 free gr_bds_documents.
 read table gt_bds_uris into gs_bds_url index 1.
 g_url = gs_bds_url-uri.
"<cl_bds_document_set> 的静态方法get_with_url获取excel template的url。"
"数据存放在内表中，读取后放在global变量g_template_url里面。"

"Open the excel"
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
```
原文：[https://blog.csdn.net/SAPmatinal/article/details/52776862](https://blog.csdn.net/SAPmatinal/article/details/52776862)

### Excel 操作

#### 操作Excel文件

```ABAP
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

#### 数据写入Excel ####

数据写入Excel，可以使用批量的方式或者逐个单元格写入的方式。批量写入的方式效率高，逐个单元格写入的方式比较灵活。将数据写入 excel 需要使用 `i_oi_spreadsheet` 接口实例的两个方法：

- insert_range_dim：定义一个范围(range)，设定range的名称、位置和大小。
- set_range_data：写入数据到range，写入的时候，ranges参数设定range的名称和大小，contents参数设定写入的内容

```ABAP
* insert_range_dim
CALL METHOD spreadsheet->insert_range_dim
 exporting
    name     = 'cell'
    no_flush = 'X'
    top      = 2
    left     = 1
    rows     = line_count
    columns  = 4.
* set_range_data
CALL METHOD spreadsheet->set_ranges_data
 exporting
    ranges   = gt_ranges
    contents = gt_contents
    no_flush = 'X'.
* 界定ranges范围
CALL METHOD R_HANDLE_EXCEL->INSERT_RANGE_DIM
  EXPORTING
      NAME     = 'title'
      NO_FLUSH = 'X'
      TOP      = 1
      LEFT     = 1
      ROWS     = 1
      COLUMNS= LV_TOTAL_COLUMNS.
  WA_RANGES-NAME= 'title'.
  WA_RANGES-ROWS = 1.
  WA_RANGES-COLUMNS= LV_TOTAL_COLUMNS.
  APPEND WA_RANGESTO IT_RANGES.
DATA LV_ROW TYPE I.
DATA LV_COLUMN TYPE I.
FIELD-SYMBOLS .
DATA LWA_FIELDCAT TYPE SLIS_FIELDCAT_ALV.
CLEAR LV_COLUMN.
LOOP AT IT_FIELDCAT INTO LWA_FIELDCAT.
  LV_COLUMN = LV_COLUMN + 1.
  WA_CONTENTS-ROW = 1.
  WA_CONTENTS-COLUMN = LV_COLUMN.
  WA_CONTENTS-VALUE  = LWA_FIELDCAT-SELTEXT_L.
  APPEND WA_CONTENTSTO IT_CONTENTS.
ENDLOOP.
* Set data
CALL METHOD R_HANDLE_EXCEL->SET_RANGES_DATA
  EXPORTING
    RANGES   = IT_RANGE
    CONTENTS = IT_CONTENTS
    NO_FLUSH = 'X'.
CALL METHOD R_HANDLE_EXCEL->FIT_WIDEST
  EXPORTING
    NAME     = SPACE
    NO_FLUSH = 'X'.
```

### 对象销毁： ###
PAI 的 exit-command 事件中对 spreadsheet、control 和 container 等对象进行销毁操作。

```JS
module exit_program input.
  save_ok = ok_code.
  clear ok_code.
  if save_ok = 'EXIT'.
    if not document is initial.
      call method document->close_document.
      free document.
    endif.
    if not control is initial.
      call method control->destroy_control.
      free control.
    endif.
    if container is not initial.
      call method container->free.
    endif.
    leave program.
  endif.
endmodule.
```
## 其他实现 ##
### 1. 如何根据屏幕大小让 Excel 自适应 ###

使用：splitter 

在 container control 初始化的时候，设定 parent 为 splitter 生成的 container。

```ABAP
DATA: container type ref to cl_gui_container,
      splitter  type ref to cl_gui_splitter_container,
    ......
FORM get_dynamic_container.
  CREATE OBJECT splitter
    EXPORTING
      parent  = cl_gui_container=>screen0
      rows    = 1
      columns = 1.
  CALL METHOD splitter->set_border
    EXPORTING
      border = cl_gui_cfw=>false.
  container = splitter->get_container( row = 1 column = 1 ).
ENDFORM.                    " GET_DYNAMIC_CONTAINER "
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
* 循环写入：
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
  " Document opened successfully"
ELSEIF ret_code EQ c_oi_errors=> ret_document_already_open.
  " Special error handling, e.g. dialog box."
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

