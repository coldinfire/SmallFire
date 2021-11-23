---
title: " ABAP DOI 使用 "
date: 2019-02-22
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils
  - ALV

---


## 概述 ##
DOI（Desktop office Integration）采用 OO 的思想实现与 Office 的结合使用，通过 DOI 对文档进行操作和处理。SAP 标准DEMO： `SAPRDEMOEXCEL_EXPORT`、`SAPRDEMO_DOI_BDS`。

文档使用方式：

- 先上传模板到服务器(OAOR)，然后由 DOI 对模板进行调用
  - OAOR：class name (HRFPM_EXCEL_STANDARD)、class type (OT)、object key
- 通过代码直接创建 ExceL / Word 文档

#### DOI 核心对象

- container：类型是 `cl_gui_custom_container`；存放 Excel 电子表格(spreadsheet)的容器，一般是程序中默认的 screen。标准的 container 接口的类型是 `cl_gui_container` 。
- container control：类型是 `i_oi_container_control`；容器中用于创建和管理其他 Office 集成所需要的对象。
- document proxy：类型是 `i_oi_document_proxy`；每一个 document proxy 的实例代表用 office application 打开的文档，如果想打开多个文档，需要定义多个实例。
- spreadsheet: 类型是`i_oi_spreadsheet`；spreadsheet 接口代表最终要操作的 excel 文档。
- errors：类型是 `i_oi_error`；异常的处理，保存操作时产生的异常信息
- retcode ：类型是 `soi_ret_string`；执行方法时返回的消息字符串
- splitter container：类型是`cl_gui_splitter_container`；动态的可以拆分的容器，可存放多个标准的(cl_gui_container)电子表格

如果读取服务器上的文档模板，需要 `cl_bds_document_set` 类：

  - business document set：类型`cl_bds_document_set`，用于管理后续要操作的文档，可以包含一个或多个文档。
  - link server：类型`i_oi_link_server`，连接服务器对象

####   DOI 操作 Excel 步骤 

   1. 获取 container 容器
   2. 创建 container control 对象实例并初始化
   3. 创建 document proxy 对象的实例
   4. 打开一个服务器上的模板文档或新建一个 excel 文档
   5. 操作 excel 文档，设置 excel 的属性或调用相关方法
   6. 退出时关闭 excel 文档，释放资源
   7. 异常信息管理

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
DATA: document_type   TYPE soi_document_type,
      document_format TYPE soi_document_type.
DATA: is_open_inplace  TYPE i VALUE 0,
      is_open_for_edit TYPE i VALUE 0,
      is_available     TYPE i.
* Spreadsheet interface structures for Excel data input
DATA: gt_ranges      TYPE soi_range_list,
      gs_range       LIKE LINE OF gt_ranges,
      gt_contents    TYPE soi_generic_table,
      gs_contents    LIKE LINE OF gt_contents,
      g_initialized  TYPE c,
      gt_excel_format TYPE soi_format_table,
      gs_excel_format LIKE LINE OF gt_excel_format,
      gs_cellitem    TYPE soi_generic_item,
      gs_rangeitem   TYPE soi_range_item.
```
OAOR  模板方法参数定义

```ABAP
* OAOR Template parameter
DATA: g_classname  TYPE sbdst_classname VALUE 'HRFPM_EXCEL_STANDARD',
      g_classtype  TYPE sbdst_classtype VALUE 'OT',
      g_objectkey  TYPE sbdst_object_key VALUE 'ZTEST'.
"BDS Method Parameter"
DATA: bds_documents  TYPE REF TO cl_bds_document_set,
      link_server    TYPE REF TO i_oi_link_server,
      gt_doc_signature  TYPE sbdst_signature,
      gs_doc_signature  LIKE LINE OF gt_doc_signature,
      gt_doc_components TYPE sbdst_components,
      gs_doc_components LIKE LINE OF gt_doc_components,
      gt_bds_uris       TYPE sbdst_uri,
      gs_bds_uri        LIKE LINE OF gt_bds_uris,
      gt_doc_properties TYPE sbdst_properties,
      gs_doc_properties LIKE LINE OF gt_doc_properties,
      doc_mimetype      TYPE bapicompon-mimetype,
      template_url      TYPE t_url.
```

### 创建 DOI ###
```ABAP
CALL SCREEN 100.
"Screen 100 PBO & PAI"
MODULE status_0100 OUTPUT.
  SET PF-STATUS 'MAIN0100'.
  SET TITLEBAR '001'.
  PERFORM get_dynamic_container.
  PERFORM create_container_control.
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

FORM create_container_control .
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
        container_name = 'DOI_PARENT'. "100屏幕上的Custom Control控件名"
*===== Get the SAP DOI container control interface =====*
    CALL METHOD c_oi_container_control_creator=>get_container_control
      IMPORTING
        control = control
        error   = error.
   "check no errors occured"
    CALL METHOD error->raise_message EXPORTING type = 'E'.
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
    "check no errors occured"
    CALL METHOD error->raise_message EXPORTING type = 'E'.
  ENDIF.
ENDFORM.                    " create_container_control "
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
*   Create Docuent: open_inplace控制文档是独立显示还是在SAP GUI中嵌入显示(X)
    METHODS: create_document
               IMPORTING open_inplace  TYPE c DEFAULT ' '
               EXPORTING retcode TYPE soi_ret_string.
*   Open Docuent
    METHODS: open_document
               IMPORTING open_inplace  TYPE c DEFAULT ' '
                         open_readonly TYPE c DEFAULT ' '
                         doc_url TYPE t_url DEFAULT ' '
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
               EXPORTING retcode      TYPE soi_ret_string
                         error        TYPE REF TO i_oi_error.
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
    DATA: control TYPE REF TO i_oi_container_control.
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
*              IMPORTING open_inplace  TYPE c DEFAULT ' '
*              RETURNING value(retcode) TYPE t_oi_ret_string.
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
*                           doc_url TYPE t_url DEFAULT ' '
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
    me->doc_url = doc_url.
    IF NOT doc_url IS INITIAL.
      CALL METHOD proxy->open_document
        EXPORTING
          document_url  = doc_url
          open_inplace  = open_inplace
          open_readonly = open_readonly
        IMPORTING
          retcode       = retcode.
      IF retcode NE c_oi_errors=>ret_ok.
        EXIT.
      ENDIF.
    ELSE.
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

```ABAP
MODULE user_command_0100 INPUT.
  DATA: l_fcode LIKE fcode.
  l_fcode = fcode.
  CLEAR fcode.
  CALL METHOD cl_gui_cfw=>dispatch.
  CASE l_fcode.
    WHEN 'CREATE'.
*    Create a New Excel Document
      IF NOT control IS INITIAL.
        IF NOT document IS INITIAL.
          CALL METHOD document->close_document.
        ENDIF.
        document_type = 'Excel.Sheet.8'.
        document_format = 'OLE'. "soi_docformat_compound"
        " 实例化自定义对象，通过对象的方法操作 Document "
        CREATE OBJECT document
                    EXPORTING control = control
                              document_type = document_type
                              document_format = document_format.
        CALL METHOD document->create_document
                    EXPORTING open_inplace = 'X'
                    IMPORTING retcode = retcode.
        CALL METHOD c_oi_errors=>raise_message EXPORTING type = 'E'.
        is_open_inplace = 1. is_open_for_edit = 1.
      ENDIF.
    WHEN 'EXIT'.
*    Close the Document and Release Control then Leave Program.
      IF NOT document IS INITIAL.
        CALL METHOD document->close_document.
        FREE document.
      ENDIF.
      IF NOT control IS INITIAL.
        CALL METHOD control->destroy_control IMPORTING retcode = retcode.
        FREE control.
      ENDIF.
      LEAVE PROGRAM.
    WHEN 'OPEN'.
      IF NOT document IS INITIAL AND document->data_size NE 0.
        IF NOT control IS INITIAL.
          IF is_open_for_edit EQ 1.
            CALL METHOD document->reopen_document
                        IMPORTING retcode = retcode.
          ELSE.
            CALL METHOD document->open_document
                        IMPORTING retcode = retcode.
          ENDIF.
          CALL METHOD c_oi_errors=>raise_message EXPORTING type = 'E'.
          is_open_inplace = 0. is_open_for_edit = 1.
        ENDIF.
      ELSE.
        MESSAGE 'First craete and save the excel document once' TYPE 'W'.
      ENDIF.
    WHEN 'VIEW'.
      IF NOT document IS INITIAL AND document->data_size NE 0.
        IF NOT control IS INITIAL.
          CALL METHOD document->view_document
                            IMPORTING retcode = retcode.
          is_open_inplace = 0. is_open_for_edit = 0.
          CALL METHOD c_oi_errors=>raise_message EXPORTING type = 'E'.
        ENDIF.
      ELSE.
        MESSAGE 'Please create a document first!' TYPE 'E'.
      ENDIF.
    WHEN 'CLOSE'.
      IF NOT document IS INITIAL.
        DATA: error TYPE REF TO i_oi_error.
        CALL METHOD document->close_document
                      EXPORTING do_save = 'X'
                      IMPORTING error = error.
        is_open_inplace = 0. is_open_for_edit = 0.
        CALL METHOD error->raise_message EXPORTING type = 'W'.
      ELSE.
        MESSAGE 'No document being processed' TYPE 'E'.
      ENDIF.
    WHEN 'SAVEDOC'.
      IF NOT document IS INITIAL.
         DATA : document_size TYPE I.
         CALL METHOD document->save_document_to_url
               EXPORTING url           = 'FILE://C:\temp\TESTEXPORT.xls'
               CHANGING  document_size = document_size.
        MESSAGE 'Document is Exported to C:\temp\TESTEXPORT.xls' TYPE 'I'.
      ELSE.
        MESSAGE 'Please create a document first!' TYPE 'E'.
      ENDIF.

  ENDCASE.
ENDMODULE.                 " USER_COMMAND_0100  INPUT "
```
#### 操作模板文档

操作 excel 模板文档，使用 cl_bds_document_set 类，这个类的 get_with_url 方法获取文档的 url。

```ABAP
* Business document system
*===== Server Link Check =====*
CALL METHOD control->get_link_server
  EXPORTING
    no_flush    = 'X'
  IMPORTING
    link_server = link_server
    error       = error.
CALL METHOD link_server->start_link_server
  EXPORTING
    no_flush = 'X'
  IMPORTING
    error    = error.
*===== Create BDS Object =====*
IF bds_documents IS INITIAL.
  CREATE OBJECT bds_documents.
ENDIF.
*===== Get template info =====*
CALL METHOD bds_documents=>get_info  "_newest_only  "alternativly
  EXPORTING
    classname  = g_classname
    classtype  = g_classtype
    object_key = g_objectkey
  CHANGING
    components = gt_doc_components
    signature  = gt_doc_signature.
  EXCEPTIONS
    nothing_found = 1
    error_kpro    = 2.
IF sy-subrc NE 0 AND sy-subrc NE 1.
  MESSAGE 'Error in the Business Document Service (BDS)' TYPE 'E'.
ENDIF.
gs_doc_signature-prop_name = 'DESCRIPTION'.
LOOP AT doc_signature INTO wa_doc_signature WHERE prop_name = 'DESCRIPTION'.
  gs_doc_signature-prop_value = wa_doc_signature-prop_value.
ENDLOOP.
APPEND gs_doc_signature TO gt_doc_signature.
CLEAR gs_doc_signature.
* READ TABLE gt_doc_components INTO gs_doc_components INDEX 1.
* doc_mimetype = gs_doc_components-mimetype.
*===== Get template url =====*
CLEAR: gt_bds_uris[],gs_bds_uri.
CALL METHOD bds_documents=>get_with_url
  EXPORTING
    classname  = g_classname
    classtype  = g_classtype
    object_key = g_objectkey
  CHANGING
    uris       = gt_bds_uris
    signature  = gt_doc_signature.
  EXCEPTIONS
    nothing_found   = 1
    error_kpro      = 2
    internal_error  = 3
    parameter_error = 4
    not_authorized  = 5
    not_allowed     = 6.
IF sy-subrc NE 0 AND sy-subrc NE 1 AND sy-subrc NE 6.
  MESSAGE 'Error in the Business Document Service (BDS)' TYPE 'S' DISPLAY LIKE 'E'.
  LEAVE TO SCREEN 0.
ENDIF.
READ TABLE gt_bds_uris INTO gs_bds_uri INDEX 1.
template_url = gs_bds_uri-uri.
*===== Open the Excel =====*
CREATE OBJECT document
  EXPORTING
    control        = control
    document_type  = document_type
    document_format = document_format.
CALL METHOD document->close_document.
CALL METHOD document->open_document
   EXPORTING
     open_inplace = 'X'
     document_url = template_url
   IMPORTING
     error        = error.
IF error->error_code EQ 'OPEN_DOCUMENT_FAILED'.
  CALL METHOD cl_gui_cfw=>flush.
  CALL METHOD cl_gui_cfw=>dispatch.
  CALL METHOD link_server->stop_link_server
    IMPORTING
      error   = error
      retcode = retcode.
  FREE: control,link_server,document,error,bds_documents.
  CALL METHOD custom_container->free.
  LEAVE TO SCREEN 0.
ENDIF.
```
### Excel 操作

#### 获取文件操作接口

```ABAP
*===== Get Spreadsheet Interface =====*
CALL METHOD document->has_spreadsheet_interface
   IMPORTING
     is_available = is_available.
IF NOT is_available IS INITIAL.
  CALL METHOD document->get_spreadsheet_interface.
ENDIF.
```

#### 操作Excel文件

```ABAP
CALL METHOD spreadsheet_interface->select_sheet
   EXPORTING
     name    = sel_sheetname
     no_flush = ''
   IMPORTING
     error    = error
     retcode  = retcode.
CALL METHOD spreadsheet_interface->get_active_sheet
    EXPORTING
      no_flush  = ''
    IMPORTING
      sheetname = sheetname
      error    = error
      retcode  = retcode.
CALL METHOD spreadsheet_interface->add_sheet
   EXPORTING
     name    = add_sheetname
     no_flush = ''
   IMPORTING
     error    = error
     retcode  = retcode.
CALL METHOD spreadsheet_interface->delete_sheet
   EXPORTING
     name    = del_sheetname
     no_flush = ''
   IMPORTING
     error    = error
     retcode  = retcode.
CALL METHOD spreadsheet_interface->set_sheet_name
  EXPORTING
    newname  = new_sheetname
    oldname  = 'Sheet1'
    no_flush = 'X'.
```

#### 数据写入Excel ####

数据写入Excel，可以使用批量的方式或者逐个单元格写入的方式。批量写入的方式效率高，逐个单元格写入的方式比较灵活。将数据写入 excel 需要使用 `i_oi_spreadsheet` 接口实例的两个方法：

- insert_range_dim：定义一个范围(range)，设定 range 的名称、位置和大小。
- set_range_data：写入数据到 range，写入的时候，ranges 参数设定 range 的名称和大小，contents参数设定写入的内容。

```ABAP
*===== insert_range_dim:界定 ranges 范围 =====*
CALL METHOD spreadsheet_interface->insert_range_dim
  EXPORTING
    name     = 'cp'
    no_flush = 'X'
    top      = row "位置开始行"
    left     = col "位置开始列"
    rows     = rows_number    "Range 总行数"
    columns  = columns_number "Range 总列数"
  IMPORTING
    error    = error.
REFRESH: gt_ranges,gt_contents.
gs_range-name = 'cp'.
gs_range-columns = rows_number.
gs_range-rows = columns_number.
APPEND gs_range TO gt_ranges.
*===== set_range_data =====*
DATA: lv_row TYPE i,lv_column TYPE i.
DATA: lwa_fieldcat TYPE slis_fieldcat_alv.
CLEAR lv_column.
LOOP AT it_fieldcat INTO lwa_fieldcat.
  lv_column = lv_column + 1.
  gs_content-row = 1.
  gs_content-column = lv_column.
  gs_content-value  = lwa_fieldcat-seltext_l.
  APPEND gs_content TO gt_contents.
ENDLOOP.
CALL METHOD spreadsheet_interface->set_ranges_data
  EXPORTING
    ranges   = gt_ranges
    contents = gt_contents
    no_flush = 'X'
  IMPORTING
    error    = error.
CALL METHOD spreadsheet_interface->fit_widest
  EXPORTING
    name     = space
    no_flush = 'X'.
REFRESH: gt_ranges, gt_contents.
```

### 对象销毁 ###
PAI 的 exit-command 事件中对 spreadsheet、control 和 container 等对象进行销毁操作。

```ABAP
MODULE exit_program INPUT.
  save_ok = ok_code.
  CLEAR ok_code.
  IF save_ok = 'EXIT'.
    IF NOT document IS INITIAL.
      CALL METHOD document->close_document.
      FREE document.
    ENDIF.
    IF NOT control IS INITIAL.
      CALL METHOD control->destroy_control.
      FREE control.
    endif.
    IF NOT container IS INITIAL.
      CALL METHOD container->free.
    ENDIF.
    LEAVE PROGRAM.
  ENDIF.
ENDMODULE.
```
### 错误处理 ###

#### 第一种方法

使用 c_oi_errors 的静态方法 raise_message 简单地显示相关的错误：type 可以是 A, E, W, I, S 其中之一。

```ABAP
CALL METHOD C_OI_ERRORS=>RAISE_MESSAGE
    EXPORTING
       TYPE = 'E'.
```

#### 第二种方法

区分不同的错误，给用户一个更明确的提示。

```ABAP
IF ret_code EQ c_oi_errors=>ret_ok.
  " Document opened successfully "
ELSEIF ret_code EQ c_oi_errors=>ret_document_already_open.
  " Special error handling, e.g. dialog box."
ELSE.
  CALL METHOD c_oi_errors=>raise_message
    EXPORTING 
      type = 'E'.
ENDIF.
```

#### 集中处理

因为 Excel 操作多个步骤，为了在过程中间减少对用户的干扰，也可以把 `ret_code` 返回的错误码先储存在内表中，集中处理。

```ABAP
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

单个单元格写入的方法，同批量写入一样，使用 i_oi_spreadsheet 接口的 set_range_dim 方法和 set_range_data方法。区别在于 range 只包含一行一列。

```ABAP
FORM write_single_cell USING p_row p_col p_value.
* define internal table for ranges and contents parameters
  DATA: lt_ranges TYPE soi_range_list,
        ls_rangeitem TYPE soi_range_item,
        lt_contents TYPE soi_generic_table,
        ls_content TYPE soi_generic_item.
* populate ranges
  CLEAR ls_rangeitem.
  CLEAR lt_ranges[].
  ls_rangeitem-name = 'cp' .
  ls_rangeitem-columns = 1.
  ls_rangeitem-rows = 1.
  ls_rangeitem-code = 4.
  APPEND ls_rangeitem TO lt_ranges.
* populate contents
  CLEAR ls_content.
  CLEAR lt_contents[].
  ls_content-column = 1.
  ls_content-row = 1.
  ls_content-value = p_value.
  APPEND ls_content TO lt_contents.
* 每次只写一行一列
  CALL METHOD spreadsheet_interface->insert_range_dim
    EXPORTING
      name     = 'cp'
      no_flush = 'X'
      top      = p_row
      left     = p_col
      rows     = 1
      columns  = 1.
  CALL METHOD spreadsheet_interface->set_ranges_data
    EXPORTING
      ranges   = lt_ranges
      contents = lt_contents
      no_flush = 'X'.
ENDFORM.   
* 循环写入
LOOP AT gt_spfli INTO gs_spfli.
   row_index = sy-tabix + 1.
   PERFORM write_single_cell USING row_index 1 gs_spfli-carrid.
   PERFORM write_single_cell USING row_index 2 gs_spfli-connid.
   PERFORM write_single_cell USING row_index 3 gs_spfli-cityfrom.
   PERFORM write_single_cell USING row_index 4 gs_spfli-cityto.
   CLEAR gs_spfli.
ENDLOOP.
```

### 3. 设置 Excel 属性 ###
```ABAP
FORM set_excel_attributes.
* set border line for range
  CALL METHOD spreadsheet_interface->set_frame
    EXPORTING
      rangename = 'cp'
      typ       = '127'
      color     = '1'
      no_flush  = 'X'.
* set font
  CALL METHOD spreadsheet_interface->set_font
    EXPORTING
      rangename = 'cp'
      family    = 'Times New Roman'
      size      = 9
      bold      = 0
      italic    = 0
      align     = 0
    IMPORTING
      error     = error
      retcode   = retcode.
* set format
  call method spreadsheet_interface->set_format
    exporting
      rangename = 'cp'
      typ       = 0
      currency  = 'RMB'
    importing
      error     = error
      retcode   = retcode.
* auto fit
  CALL METHOD spreadsheet_interface->fit_widest
    EXPORTING
      name    = space
      no_flush = 'X'.
ENDFORM.   
```

### 4.金额数字格式转换

```ABAP
FORM frm_output_format USING l_num CHANGING l_result.
  DATA:l_amt(17),
       l_int(10),
       l_char(17),
       l_fac(2),
       l_len TYPE i,
       l_count TYPE i,
       l_pos TYPE i,
       l_rest TYPE i,
       l_time TYPE i.
  CONSTANTS: c_tab VALUE ',',
             c_pot VALUE '.'.
  CLEAR l_amt.
  IF l_num > 0.
    l_amt = ABS( l_num ).
    CONDENSE l_amt.
    SPLIT l_amt AT c_pot INTO l_int l_fac.
    l_len = STRLEN( l_int ).
    l_count = l_len.
    l_rest = l_len MOD 3.
    IF l_rest = 0.
      l_time = l_len DIV 3.
    ELSE.
      l_time = l_len DIV 3 + 1.
    ENDIF.
    DO l_time TIMES.
      l_count = l_count - 3.
      IF l_count > 0.
        CONCATENATE c_tab l_int+l_count(3) l_char INTO l_char.
      ELSEIF l_count <= 0.
        l_count = l_count + 3.
        CONCATENATE l_int+0(l_count) l_char INTO l_char.
        EXIT.
      ENDIF.
    ENDDO.
    CLEAR l_len.
    l_len = STRLEN( l_fac ).
    IF l_fac IS INITIAL.
      CONCATENATE l_char '.00' INTO l_result.
    ELSEIF l_len = 1.
      CONCATENATE l_char '.' l_fac '0' INTO l_result.
    ELSEIF l_len = 2.
      CONCATENATE l_char '.' l_fac INTO l_result.
    ENDIF.
    CONDENSE l_result.
  ELSEIF l_num < 0.

    l_amt = ABS( l_num ).
    CONDENSE l_amt.
    SPLIT l_amt AT c_pot INTO l_int l_fac.
    l_len = STRLEN( l_int ).
    l_count = l_len.
    l_rest = l_len MOD 3.
    IF l_rest = 0.
      l_time = l_len DIV 3.
    ELSE.
      l_time = l_len DIV 3 + 1.
    ENDIF.
    DO l_time TIMES.
      l_count = l_count - 3.
      IF l_count > 0.
        CONCATENATE c_tab l_int+l_count(3) l_char INTO l_char.
      ELSEIF l_count <= 0.
        l_count = l_count + 3.
        CONCATENATE l_int+0(l_count) l_char INTO l_char.
        EXIT.
      ENDIF.
    ENDDO.
    CLEAR l_len.
    l_len = STRLEN( l_fac ).
    IF l_fac IS INITIAL.
      CONCATENATE '-' l_char '.00' INTO l_result.
    ELSEIF l_len = 1.
      CONCATENATE '-' l_char '.' l_fac '0' INTO l_result.
    ELSEIF l_len = 2.
      CONCATENATE '-' l_char '.' l_fac INTO l_result.
    ENDIF.
    CONDENSE l_result.
  ELSE.
    l_result = '0.00'.
  ENDIF.
ENDFORM.                    "frm_output_format"

DATA: l_amt(20) TYPE c,
      l_amt_doccur TYPE DMBTR.
PERFORM frm_output_format USING     l_amt_doccur
                          CHANGING  l_amt.
```

参考文章

- [ABAP DOI展示EXCEL或WORD ](https://blog.csdn.net/SAPmatinal/article/details/52776862)

- [ABAP DOI详解](https://blog.csdn.net/stone0823/article/details/53819960)