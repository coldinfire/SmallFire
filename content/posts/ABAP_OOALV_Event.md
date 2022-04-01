---
title: " OO ALV 事件管理 "
date: 2018-07-17
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - OOALV

---

### ALV 对象注册事件

如果我们想处理 ALV Grid 实例触发的事件，我们应该定义并实现一个事件处理程序类。 创建 ALV Grid 实例后，我们必须注册此事件处理程序类的实例来处理 ALV Grid 事件。

(1) HANDLE_TOOLBAR：这个事件用于给 ALV 加自定义工具条按钮。

(2) HANDLE_CLICK：用于给 ALV 点击其中一行后处理代码段。

(3) HANDLE_COMMAND：事件用于接收用户按了自定义按钮后，触发的代码段。

(4) HANDLE_DOUBLE_CLICK：双击事件处理。

(5) DATA_CHANGED：可编辑字段的数据发生变化时触发，可用来检查数据的输入正确性。

(6) DATA_CHANGED_FINISHED：当数据修改完成后触发，数据没有被修改，当失去焦点或回车时触发。

(7) HANDLE_F4_HELP：F4 搜索帮助

#### 事件类定义

```ABAP
CLASS lcl_event_receiver DEFINITION.
  PUBLIC SECTION.
    "声明单击事件的方法"
    METHODS handle_hotspot_click
      FOR EVENT hotspot_click OF cl_gui_alv_grid
      IMPORTING e_row_id e_column_id.
    "声明双击事件方法"
    METHODS handle_double_click
      FOR EVENT double_click OF cl_gui_alv_grid
      IMPORTING e_row e_column.
    "声明 Toolbar 事件方法"
    METHODS handle_toolbar
      FOR EVENT toolbar OF cl_gui_alv_grid
      IMPORTING e_object e_interactive.
    "声明 USER-COMMAND 事件方法"
    METHODS handle_command
      FOR EVENT user_command OF cl_gui_alv_grid
      IMPORTING e_ucomm.
    "声明数据修改事件"
    METHODS handle_data_changed 
      FOR EVENT data_changed OF cl_gui_alv_grid
      IMPORTING er_data_changed e_onf4_before e_onf4_after e_ucomm.
    "F4 Help"
    METHODS handle_f4_help
      FOR EVENT onf4 OF cl_gui_alv_grid
      IMPORTING e_fieldname es_row_no er_event_data et_bad_cells e_display.
ENDCLASS.                    "cl_event_receiver DEFINITION"
```

#### 事件类实现

```ABAP
CLASS lcl_event_receiver IMPLEMENTATION.
  "单击事件方法的实现"
  METHOD handle_hotspot_click.
    CONDENSE e_row_id     NO-GAPS.
    CONDENSE e_column_id  NO-GAPS.
    MESSAGE i001(00) WITH ' 单击事件 -> 行号:' e_row_id  '、列名：' e_column_id.
  ENDMETHOD.                    "handle_HOTSPOT_CLICK"
  "双击事件方法的实现"
  METHOD handle_double_click.
    CONDENSE e_row     NO-GAPS.
    CONDENSE e_column  NO-GAPS.
    MESSAGE i001(00) WITH ' 双击事件 -> 行号:' e_row  '、列名：' e_column.
  ENDMETHOD.                    "handle_double_click"
  "实现 Toolbar 事件方法"
  METHOD handle_toolbar.
    DATA: ls_toolbar TYPE stb_button.
    CLEAR: ls_toolbar.
    ls_toolbar-butn_type = 3.   "分隔符"
    APPEND ls_toolbar TO e_object->mt_toolbar.
    CLEAR: ls_toolbar.
    ls_toolbar-function = 'DISP'.    "功能码"
    ls_toolbar-icon = icon_display.  "图标名称"
    ls_toolbar-quickinfo = ' 显示'.   "图标的提示信息"
    ls_toolbar-butn_type = 0.        "0 表示正常按钮"
    ls_toolbar-disabled = ''.        "X 表示灰色，不可用"
    ls_toolbar-text = 'btn1'.      "按钮上显示的文本"
    APPEND ls_toolbar TO e_object->mt_toolbar.
  ENDMETHOD.                    "handle_toolbar"
  "实现 USER-COMMAND 事件方法"
  METHOD handle_command.
    CASE e_ucomm.
    WHEN 'DISP'.
       MESSAGE i001(00) WITH 'Toolbar 事件 + USER-COMMAND 事件 '.
    ENDCASE.
  ENDMETHOD.                    "HANDLE_COMMAND"
  "F4 Help"
  METHOD handle_f4_help.
    FIELD-SYMBOLS: <fs_alv> TYPE alv.
    CASE e_fieldname.
    WHEN 'XXXX'.
      READ TABLE alv assiging <fs_alv> INDEX es_row_no-row_id.
      IF sy-subrc = 0.
        PERFORM frm_get_f4_value CHANGING <fs_alv>-xxxx.
      ENDIF.
    ENDCASE.
    CALL METHOD go_grid->refresh_table_display.
  ENDMETHOD.
ENDCLASS.                    "cl_event_receiver IMPLEMENTATION"
```

#### 注册事件

在创建 ALV GRID 实例后注册事件

```ABAP
CLASS lcl_event_receiver DEFINITION DEFERRED.
DATA event_receiver TYPE REF TO lcl_event_receiver.
CREATE OBJECT event_receiver. "创建事件"
"注册事件 handler 方法"
SET HANDLER event_receiver->handle_hotspot_click  FOR go_grid.
SET HANDLER event_receiver->handle_double_click   FOR go_grid.
SET HANDLER event_receiver->handle_toolbar        FOR go_grid.
SET HANDLER event_receiver->handle_command        FOR go_grid.
SET HANDLER event_receiver->handle_data_changed   FOR go_grid.
SET HANDLER event_receiver->handle_f4_help        FOR go_grid.
```

### 控制数据变化

ALV 提供给我们控制数据的输入，有两个事件：**data_changed** 和 **data_changed_finished** 。第一个事件在可编辑字段的数据发生变化时触发，可用来检查数据的输入；第二个事件是当数据修改完成后触发。

除了设置 FIELDCAT 的 EDIT 属性。还需要在显示 ALV 后添加触发数据改变事件，必须设置一种方式，否则不会触发数据更新事件。

```ABAP
CALL METHOD ALV_GRID->REGISTER_EDIT_EVENT
  EXPORTING
    I_EVENT_ID = CL_GUI_ALV_GRID=>MC_EVT_MODIFIED. 
```


- `I_EVENT_ID = CL_GUI_ALV_GRID=>MC_EVENT_ENTER.`
- 在单元格修改后回车或者执行其他操作时触发事件,此类型可用于多个单元格修改后一起检查修改的值
- `I_EVENT_ID = CL_GUI_ALV_GRID=>MC_EVENT_MODIFIES.`

  - 当光标焦点移开被修改单元格后既触发事件,此类型可用于每个每个单元个的实时更新检查

#### 获取 ALV 字段修改信息

为了获取 ALV 里字段修改的一些信息，DATA_CHANGED 事件会把参考`CL_ALV_CHANGED_DATA_PROTOCOL` 创建的实例通过参数 ER_DATA_CHANGED 传递给 ALV。通过该参数我们可以知道哪些单元格发生了变化。

- 类 CL_ALV_CHANGED_DATA_PROTOCOL 的一些方法

  | Methods             | Desc               |
  | ------------------- | ------------------ |
  | GET_CELL_VALUE      | 获取单元格值       |
  | MODIFY_CELL         | 修改单元格         |
  | ADD_PROTOCOL_ENTRY  | 增加日志记录       |
  | PROTOCOL_IS_VISIBLE | 是否允许错误表可见 |
  | REFRESH_PROTOCOL    | 刷新日志记录       |

- 类 CL_ALV_CHANGED_DATA_PROTOCOL 的一些属性

  | Attributes       | Desc                                     |
  | :--------------- | :--------------------------------------- |
  | MT_MOD_CELLS     | 包含带有行和字段名称的已修改单元格的数据 |
  | MT_MOD_ROWS      | 包含修改的行数据，类型是数字             |
  | MT_GOOD_CELLS    | 包含当前单元格的值                       |
  | MT_DELETED_ROWS  | 包含从列表中删除的行                     |
  | MT_INSERTED_ROWS | 包含列表中新插入的行                     |

实现 DATA Change 事件方法

```ABAP
METHOD handle_data_changed.
  DATA: mod_data TYPE lvc_s_modi.
        ls_output TYPE str_output,
        lv_menge  TYPE ekpo-netwr,
        lv_menge_ori TYPE ekpo-netwr.
  SORT er_data_changed->mt_good_cells BY row_id.
  LOOP AT er_data_changed->mt_good_cells INTO mod_data.
    CLEAR: ls_output,lv_menge.
    IF mod_data-fieldname = 'NETWR'.
      READ TABLE gt_output INDEX mod_data-row_id INTO ls_output.
      IF sy-subrc = 0.
        CALL METHOD er_data_changed->get_cell_value
          EXPORTING
            i_row_id    = mod_data-row_id
            i_fieldname = mod_data-fieldname
          IMPORTING
            e_value     = lv_menge.
        ......
       lv_menge_ori = mod_data-value.
       CALL METHOD er_data_changed->add_protocol_entry
         EXPORTING
           i_msgid     = '00'
           i_msgty     = 'E'
           i_msgno     = '001'
           i_msgv1     = 'Data Format Error'
           i_msgv2     = lv_menge
           i_msgv3     = '.'
           i_fieldname = mod_data-fieldname.
        ......
        CALL METHOD er_data_changed->modify_cell
          EXPORTING
            i_row_id    = mod_data-row_id
            i_fieldname = mod_data-fieldname
            i_value     = lv_menge.
      ENDIF.
    ENDIF.
  ENDLOOP.
ENDMETHOD.
```

### F4 Help

#### 定义 fieldcat  属性

```ABAP
fieldcat-f4availabl = 'X'.
fieldcat-edit = 'X'.
```

#### F4 事件注册到 ALV

```ABAP
DATA: lt_f4 TYPE lvc_t_f4,
      ls_f4 TYPE lvc_s_f4.
CALL METHOD go_grid->set_table_for_first_display.
CLEAR: ls_f4.
ls_f4-fieldname = 'XXXX'.
ls_f4-register  = 'X'.
ls_f4-getbefore = 'X'.
ls_f4-chngeafter = ''.
ls_f4-internal = ''.
INSERT ls_f4 INTO TABLE lt_f4.

CALL METHOD go_grid->register_f4_for_fields
  EXPORTING
    it_f4 = lt_f4.
```

#### 自定义 F4，获取数据

```ABAP
FORM frm_get_f4_value USING i_matnr TYPE xxx
                      CHANGING e_value TYPE xxx.
  DATA: value_tab TYPE TABLE OF xxxx WITH HEADER LINE.
  DATA: lt_ret_tab TYPE TABLE OF ddshretval WITH HEADER LINE.
  CALL FUNCTION 'F4IF_INT_TABLE_VALUE_REQUEST'
    EXPORTING
      retfield        = 'SUB_NAME_M'
      value_org       = 'S'
    TABLES
      value_tab       = value_tab
      return_tab      = lt_ret_tab
    EXCEPTIONS
      parameter_error = 1
      no_values_fount = 2
      others          = 3.
  IF sy-subrc = 0.
    READ TABLE lt_ret_tab INDEX 1.
    IF sy-subrc = 0 AND lt_ret_tab-fieldval IS NOT INITIAL.
      e_value = lt_ret_tab-fieldval.
    ENDIF.
  ENDIF.
ENDFORM.
```

