---
title: "OO ALV 事件管理"
date: 2018-07-10
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
---

### ALV 对象注册事件

如果我们想处理由 ALV Grid 实例触发的事件，我们应该定义并实现一个事件处理程序类。 创建 ALV Grid 实例后，我们必须注册此事件处理程序类的实例来处理 ALV Grid 事件。

(1) HANDLE_TOOLBAR：这个事件用于给ALV加自定义工具条按钮。

(2) HANDLE_CLICK：用于给ALV点击其中一行后处理代码段。

(3) HANDLE_COMMAND：事件用于接收用户按了自定义按钮后，触发的代码段。

(4) HANDLE_DOUBLE_CLICK：双击事件

(5) DATA_CHANGED：可编辑字段的数据发生变化时触发，可用来检查数据的输入正确性

(6) DATA_CHANGED_FINISHED：当数据修改完成后触发，数据没有被修改，当失去焦点或回车时触发

......

**定义事件**

```ABAP
CLASS cl_event_receiver DEFINITION.
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
ENDCLASS.                    "cl_event_receiver DEFINITION"
```

**事件执行的方法代码**

```ABAP
CLASS cl_event_receiver IMPLEMENTATION.
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
ENDCLASS.                    "cl_event_receiver IMPLEMENTATION"
```

**注册事件**

在创建GRID实例后注册事件：

```ABAP
CREATE OBJECT event_receiver.
  "注册事件 handler 方法"
   SET HANDLER event_receiver->handle_hotspot_click  FOR g_grid01.
   SET HANDLER event_receiver->handle_double_click   FOR g_grid01.
   SET HANDLER event_receiver->handle_toolbar FOR g_grid01.
   SET HANDLER event_receiver->handle_command FOR g_grid01.
```

**单元格编辑**

设置FIELDCAT的 EDIT 属性。还需要在显示ALV前添加触发数据改变事件：

```ABAP
CALL METHOD ALV_GRID->REGISTER_EDIT_EVENT
  EXPORTING
    I_EVENT_ID = CL_GUI_ALV_GRID=>MC_EVT_MODIFIED. "必须设置一种触发方式"
```

- 按回车触发：I_EVENT_ID = CL_GUI_ALV_GRID=>MC_EVENT_ENTER.
- 单元格失去焦点触发：I_EVENT_ID = CL_GUI_ALV_GRID=>MC_EVENT_MODIFIES.