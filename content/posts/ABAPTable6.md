---
title: "报表开发<OO ALV>"
date: 2018-07-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
 
---

## OO ALV

​	ALV GRID CONTROL 使用了控制器技术以实现屏幕显示，和所有的控制器一样，ALV GRID CONTROL 通过系统中的一个全局的类提供了方法，以响应它的动作.

​	使用了 ABAP 的对象以后，列表是通过 ALV 的一个实例 (INSTANCE) 来显示的，可以使用 ABAP 对象的事件管理.

**ALV GRID继承结构**    ![继承结构](/images/ABAP/OOALV1.png)

### 相关类

------

- CL_GUI_CUSTOM_CONTAINER：用户自己定义控件区域


- CL_GUI_DOCKING_CONTAINER：动态创建容器，不需要在创建时绑定到预先绘制好的自定义控件中


- CL_GUI_SPLITTER_CONTAINER：可在同一屏幕创建多个ALV显示


- CL_GUI_ALV_GRID：ALV控制类
- CL_GUI_CONTAINER：容器接口

- LVC_T_FCAT：ALV显示参照字段表

- LVC_S_FCAT：ALV显示参照字段结构

***定义***

​	DATA obj_wcl_container TYPE REF TO CL_GUI_CUSTOM_CONTAINER."控制容器类

​	DATA obj_wcl_alv TYPE REF TO CL_GUI_ALV_GRID . "ALV控制类

​	DATA GS_LAYOUT1 TYPE LVC_S_LAYO. "布局结构	

​	DATA GT_FIELDCAT TYPE LVC_T_FCAT. "存放字段目录的内表

​	DATA GS_FIELDCAT TYPE LVC_S_FCAT."存放字段目录的结构

​	DATA lt_exclude TYPE ui_functions. "alv不需要的图标按钮 

### 控制区域、Container、Grid关系

------

​	先在屏幕绘制一个用户自定义控件区域，然后以自定义区域为基础创建 CL_GUI_CUSTOM_CONTAINER容器实例,最后以此容器实例来创建 CL_GUI_ALV_GRID实例。

```JS
DATA : obj_wcl_container TYPE REF TO cl_gui_custom_container, "控制容器类
       obj_wcl_alv TYPE REF TO cl_gui_alv_grid . "ALV控制类
“创建SAP容器实例
IF obj_wcl_alv IS INITIAL.
 CREATE OBJECT obj_wcl_container
	EXPORTING
      container_name  =  'OBJ_WCL_CONTAINER'. "自定义控件名称
	EXCEPTONS
      cntl_error      = 1.
	  ...
  IF sy-subrc <> 0.
	MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENEIF.
”创建GRID实例
 CREATE OBJECT obj_wcl_alv
    EXPORTING
      i_parent  = obj_wcl_container.
    EXCEPTIONS
        error_cntl_create = 1
        error_cntl_init   = 2
        error_cntl_link   = 3
        error_dp_create   = 4
        OTHERS            = 5.
    IF sy-subrc <> 0.
      MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
                 WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
    ENDIF.
```

**创建多个ALV：**

```JS
DATA:G_SPLITTER TYPE REF TO CL_GUI_SPLITTER_CONTAINER,
     G_CONTAINER_2000L TYPE REF TO CL_GUI_CONTAINER,
     G_CONTAINER_2000R TYPE REF TO CL_GUI_CONTAINER.
     
IF g_splitter IS INITIAL.
    CREATE OBJECT g_splitter  “定义有两个ALV
      EXPORTING
        parent  = cl_gui_container=>screen0
        rows    = 2
        columns = 1.
        
    CALL METHOD g_splitter->set_border
      EXPORTING
        border = cl_gui_cfw=>false.
        
    g_container_2000l = g_splitter->get_container( row = 1 column = 1 ).
    g_container_2000r = g_splitter->get_container( row = 2 column = 1 ).
    
    g_splitter->set_row_height( id = 1 height = 20 ).
    g_splitter->set_row_height( id = 2 height = 80 ).
```



### 给ALV对象注册事件

------

(1) HANDLE_TOOLBAR:这个事件用于给ALV加自定义工具条按钮。

(2) HANDLE_CLICK:用于给ALV点击其中一行后处理代码段。

(3) HANDLE_COMMAND:事件用于接收用户按了自定义按钮后，触发的代码段。

(4) HANDLE_DOUBLE_CLICK：双击事件

(5) DATA_CHANGED：可编辑字段的数据发生变化时触发，可用来检查数据的输入正确性

(6) DATA_CHANGED_FINISHED：当数据修改完成后触发，数据没有被修改，当失去焦点或回车时触发

......

**定义事件**

```JS
CLASS cl_event_receiver DEFINITION.
  PUBLIC SECTION.
    " 声明单击事件的方法
    METHODS handle_hotspot_click
      FOR EVENT hotspot_click OF cl_gui_alv_grid
      IMPORTING e_row_id e_column_id.
    " 声明双击事件方法
    METHODS handle_double_click
      FOR EVENT double_click OF cl_gui_alv_grid
      IMPORTING e_row e_column.
    " 声明 Toolbar 事件方法
    METHODS handle_toolbar
      FOR EVENT toolbar OF cl_gui_alv_grid
      IMPORTING e_object e_interactive.
    " 声明 USER-COMMAND 事件方法
    METHODS handle_command
      FOR EVENT user_command OF cl_gui_alv_grid
      IMPORTING e_ucomm.
ENDCLASS.                    "cl_event_receiver DEFINITION
```

**事件执行的方法代码**

```JS
CLASS cl_event_receiver IMPLEMENTATION.
  " 单击事件方法的实现
  METHOD handle_hotspot_click.
    CONDENSE e_row_id     NO-GAPS.
    CONDENSE e_column_id  NO-GAPS.
    MESSAGE i001(00) WITH ' 单击事件 -> 行号:' e_row_id  '、列名：' e_column_id.
  ENDMETHOD.                    "handle_HOTSPOT_CLICK
  " 双击事件方法的实现
  METHOD handle_double_click.
    CONDENSE e_row     NO-GAPS.
    CONDENSE e_column  NO-GAPS.
    MESSAGE i001(00) WITH ' 双击事件 -> 行号:' e_row  '、列名：' e_column.
  ENDMETHOD.                    "handle_double_click
  " 实现 Toolbar 事件方法
  METHOD handle_toolbar.
    DATA: ls_toolbar TYPE stb_button.
    CLEAR: ls_toolbar.
    ls_toolbar-butn_type = 3. " 分隔符
    APPEND ls_toolbar TO e_object->mt_toolbar.
    CLEAR: ls_toolbar.
    ls_toolbar-function = 'DISP'.    " 功能码
    ls_toolbar-icon = icon_display.  " 图标名称
    ls_toolbar-quickinfo = ' 显示'.   " 图标的提示信息
    ls_toolbar-butn_type = 0.        " 0 表示正常按钮
    ls_toolbar-disabled = ''.        " X 表示灰色，不可用
    ls_toolbar-text = ' 按钮 1'.       " 按钮上显示的文本
    APPEND ls_toolbar TO e_object->mt_toolbar.
  ENDMETHOD.                    "handle_toolbar
  " 实现 USER-COMMAND 事件方法
  METHOD handle_command.
    CASE e_ucomm.
    WHEN 'DISP'.
       MESSAGE i001(00) WITH 'Toolbar 事件 + USER-COMMAND 事件 '.
    ENDCASE.
  ENDMETHOD.                    "HANDLE_COMMAND
ENDCLASS.                    "cl_event_receiver IMPLEMENTATION
```

**注册事件**

在创建GRID实例后注册事件：

```JS
CREATE OBJECT event_receiver.
  " 注册事件 handler 方法
   SET HANDLER event_receiver->handle_hotspot_click  FOR g_grid01.
   SET HANDLER event_receiver->handle_double_click   FOR g_grid01.
   SET HANDLER event_receiver->handle_toolbar FOR g_grid01.
   SET HANDLER event_receiver->handle_command FOR g_grid01.
```

**单元格编辑**

设置FIELDCAT的 EDIT 属性。

```JS

还需要在显示ALV前添加触发数据改变事件：
CALL METHOD ALV_GRID->REGISTER_EDIT_EVENT
  EXPORTING
    I_EVENT_ID = CL_GUI_ALV_GRID=>MC_EVT_MODIFIED. “必须设置一种触发方式
    
按回车触发：I_EVENT_ID = CL_GUI_ALV_GRID=>MC_EVENT_ENTER.
单元格失去焦点触发：I_EVENT_ID = CL_GUI_ALV_GRID=>MC_EVENT_MODIFIES.
```
### ALV显示

------

CL_GUI_ALV_GRID重要方法

```JS
-显示ALV 
 CALL METHOD obj_wcl_alv->set_table_for_first_display 
    EXPORTING 
      “i_structure_name              = 'SPFLI'      :参照表结构字段显示
      is_variant                    = ls_variant   :指定布局变式
      is_layout                     = ls_layout    :布局设置
      i_save                        = 'A'          :保存表格布局
      it_toolbar_excluding          = lt_exclude   :排除的按钮
    CHANGING 
      it_outtab                     = gt_list[]    :需要显示的内表数据
      it_fieldcatalog               = lt_fieldcat  :结构字段
    EXCEPTIONS 
      invalid_parameter_combination = 1 
      program_error                 = 2 
      too_many_lines                = 3 
      OTHERS                        = 4.

  方法 -> REFRESH_TABLE_DISPLAY.

```

**刷新：REFRESH_TABLE_DISPLAY.**

```JS
CALL METHODgr_alvgrid->refresh_table_display *
  EXPORTING
  	IS_STABLE = 
	I_SOFT_REFRESH = 'X'
  EXCEPTIONS
	finished = 1
	OTHERS = 2 .
IF sy-subrc <> 0.
	异常处理
ENDIF 

IS_STABLE：有行列两个参数，如果设置了相应的参数，回应的行或列舒心就不会滚动。刷新的稳定性，就是滚动条保持不动
I_SOFT_REFRESH:  软刷新，为了显示数据而设置的过滤都将保持不变。临时给ALV创建的合计，排序，等保持不变
```
### FieldCat

------

设置显示数据的字段目录：

- 宏定义

```JS
DEFINE M_FIELDCAT.
  CLEAR: &1.
  &1-FIELDNAME = &2 .
  &1-COLTEXT   = &3 .
  &1-CHECKBOX  = &4 .
  &1-REPREP    = &5 .
  &1-EDIT      = &6 .
  &1-OUTPUTLEN = &7 .
  &1-DECIMALS  = &8 .
  APPEND &1.
END-OF-DEFINITION.
DATA: alv1_fieldcat type standard table of lvc_s_fcat with header line.
M_FIELDCAT:
  alv1_fieldcat  'DATUM'         'Plan.Date'          ''      ''     ''    ''          '',
  "alv1_fieldcat  'UZEIT'        'Plan.Time'          ''      ''     ''    ''          '',
  alv1_fieldcat  'STATUS'        'Status'             ''      ''     ''    ''          ''.
    
```

- 通过调用BAPI完成

```JS
FORM prepare_field_catalog CHANGING pt_fieldcat TYPE lvc_t_fcat .
  DATA ls_fcat type lvc_s_fcat . 
  CALL FUNCTION 'LVC_FIELDCATALOG_MERGE' 
    EXPORTING
      i_structure_name = 'SFLIGHT' 
	CHANGING
      ct_fieldcat = pt_fieldcat[] 
	EXCEPTIONS
	  inconsistent_interface = 1 
	  program_error = 2 
	  OTHERS = 3. 
	IF sy-subrc <> 0. 
	  Exception handling
	ENDIF. 

  LOOP AT pt_fieldcat INTO ls_fcat . 
	CASE pt_fieldcat-fieldname . 
	  WHEN 'CARRID' . 
		ls_fcat-outpulen = '10' . 
		ls_fcat-coltext = 'Airline Carrier ID' . 
		ls_fcat-edit = 'X'.
	    MODIFY pt_fieldcat FROM ls_fcat . 
	  WHEN 'PAYMENTSUM' . 
		ls_fcat-no_out = 'X' . 
		MODIFY pt_fieldcat FROM ls_fcat . 
	ENDCASE .
  ENDLOOP .
ENDFORM .
```

### Layout

------

设置布局：

```JS
FORM prepare_layout CHANGING ps_layout TYPElvc_s_layo.     
	ps_layout-zebra = 'X' .    
	ps_layout-grid_title = 'Flights' .    
    ps_layout-smalltitle = 'X' .
ENDFORM. " prepare_layout
```

排除不必要的按钮：

- 自定义按钮

  ```JS
  DATA: lt_excl TYPE slis_t_extab.
    APPEND 'CLOSE' TO lt_excl.
    APPEND 'SALL.PUL' TO lt_excl.
    APPEND 'UALL.PUL' TO lt_excl.
  SET PF-STATUS 'STATUS' EXCLUDING  lt_excl .
  SET TITLEBAR 'TITLE'.
  ```

- 系统标准按钮

  ```JS
  FORM exclude_tb_functions CHANGING pt_exclude TYPE ui_functions .     
    DATA ls_exclude TYPE ui_func.      
    ls_exclude = cl_gui_alv_grid=>mc_fc_maximum .      
      APPEND ls_exclude TO pt_exclude.      
    ls_exclude = cl_gui_alv_grid=>mc_fc_minimum .    
      APPEND ls_exclude TO pt_exclude.     
    ls_exclude = cl_gui_alv_grid=>mc_fc_subtot .    
      APPEND ls_exclude TO pt_exclude.     
    ls_exclude = cl_gui_alv_grid=>mc_fc_sum .      
      APPEND ls_exclude TO pt_exclude.      
    ls_exclude = cl_gui_alv_grid=>mc_fc_average .     
   	APPEND ls_exclude TO pt_exclude.      
    ls_exclude = cl_gui_alv_grid=>mc_mb_sum .     
   	APPEND ls_exclude TO pt_exclude.      
    ls_exclude = cl_gui_alv_grid=>mc_mb_subtot .
    	APPEND ls_exclude TO pt_exclude.
  ENDFORM .
  ```

  
