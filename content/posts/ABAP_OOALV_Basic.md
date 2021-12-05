---
title: " OO ALV 使用 "
date: 2018-07-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
 
---

## OO ALV

ALV GRID CONTROL 使用了控制器技术以实现屏幕显示，和所有的控制器一样，ALV GRID CONTROL 通过系统中的一个全局的类提供了方法，以响应它的动作。

使用了 ABAP 的对象以后，列表是通过 ALV 的一个实例 (INSTANCE) 来显示的，可以使用 ABAP 对象的事件管理。

#### ALV GRID CONTROL 实例

参照 CL_GUI_ALV_GRID 类定义实例 `DATA go_grid TYPE REF TO cl_gui_alv_grid.`

![继承结构](/images/ABAP/OOALV1.png)

### OO ALV 相关类

#### 容器类

- CL_GUI_CONTAINER：容器接口
- CL_GUI_CUSTOM_CONTAINER：用户自己定义控件区域


- CL_GUI_DOCKING_CONTAINER：动态创建容器，不需要在创建时绑定到预先绘制好的自定义控件中


- CL_GUI_SPLITTER_CONTAINER：可拆分的容器，可在同一屏幕创建多个 ALV 显示

#### 使用到的类和参数


- CL_GUI_ALV_GRID：ALV控制类

- LVC_T_FCAT：ALV显示参照字段表

- LVC_S_FCAT：ALV显示参照字段结构

- LVC_S_LAYO：ALV显示布局结构

- LVC_T_SORT：排序条件字段

- LVC_T_FILT：过滤条件字段

  ```ABAP
  DATA: go_container TYPE REF TO cl_gui_custom_container, "控制容器类"
        go_grid      TYPE REF TO cl_gui_alv_grid, "ALV Grid控制类"
        gt_fieldcat TYPE lvc_t_fcat,   "字段目录内表"
        gs_fieldcat TYPE lvc_s_fcat,   "字段目录结构"
        layout      TYPE lvc_s_layo,   "Layout 设置"
        gt_sort     TYPE lvc_t_sort,   "排序内表"
        gt_filter   TYPE lvc_t_filt,   "过滤内表"
        gt_exclude  TYPE ui_functions.
  START-OF-SELECTION .
    CALL SCREEN 1000.
  ```

### Customer Control、Container、ALV Grid 之间关系

ALV GRID 必须存在于一个容器当中，就是 FUNCTION 的 ALV 其实也是一样的，底层也是使用 CL_GUI_ALV_GRID 这个类的。作为控件对象，ALV Grid 实例需要一个容器来链接到屏幕。 

先在屏幕绘制一个用户自定义控件区域；然后以自定义控件区域为基础创建  CL_GUI_CUSTOM_CONTAINER 容器实例；最后以此容器实例来创建  CL_GUI_ALV_GRID 实例。

![](/images/ABAP/OOALV3.png)

#### 在屏幕中定义 Customer Control

![Customer Control](/images/ABAP/OOALV2.png)

屏幕的 PBO 和 PAI 定义：

```ABAP
PROCESS BEFORE OUTPUT.
  MODULE display_alv.
PROCESS AFTER INPUT.
  MODULE user_command.
```

#### 创建 Container 实例

在创建容器对象实例时，其构造方法有一个必传参数 CONTAINER_NAME，它的值就是屏幕上绘制的 Custom Control 的控件的名称。

```ABAP
* Creating custom container instance
IF go_container IS INITIAL.
  CREATE OBJECT go_container
    EXPORTING
      container_name    =  'CONTAINER'. "自定义控件名称"
    EXCEPTIONS
      cntl_error        = 1
      cntl_system_error = 2
      create_error      = 3
      lifetime_error    = 4
      lifetime_dynpro_dynpro_link = 5
      others = 6 .
  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno 
      WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENEIF.
ENDIF.
```

#### 创建 ALV GRID 实例

当容器实例创建好以后，CL_GUI_ALV_GRID 再以此容器对象作为参数 I_PARENT 的值实例化，即可得到实例化对象，最后再调用 CL_GUI_ALV_GRID 实例对象的 SET_TABLE_FOR_FIRST_DISPLAY 方法来生成 ALV 报表。

```ABAP
* Creating ALV Grid instance
IF go_grid IS INITIAL.
  CREATE OBJECT go_grid
    EXPORTING
      i_parent  = go_container. "Parent为Container容器"
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
ENDIF.
* Preparing field catalog
PERFORM prepare_field_catalog CHANGING gt_fieldcat .
* Preparing layout structure
PERFORM prepare_layout.
* Preparing sort conditions
PERFORM prepare_sort_table CHANGING gt_sort .
* Exclude toobar function
PERFORM exclude_tb_functions CHANGING gt_exclude.
```

### Field Catalog

字段目录必须包含有关要显示的每一列的显示选项的一些技术和附加信息。 

字段目录的生成分为：自动生成、半自动生成、手动生成。

字段目录的内表需要参照字典类型 `LVC_T_FCAT`。

#### 手动生成：宏定义

```ABAP
DEFINE mro_fieldcat.
  CLEAR: gs_fieldcat.
  gs_fieldcat-fieldname = &1 .
  gs_fieldcat-coltext   = &2 .
  gs_fieldcat-seltext   = &3 .
  gs_fieldcat-outputlen = &4 .
  gs_fieldcat-checkbox  = &5 .
  gs_fieldcat-edit      = &6 .
  APPEND gs_fieldcat TO gt_fieldcat.
END-OF-DEFINITION.
mro_fieldcat:
  'DATUM'  'Plan.Date' 'Plan.Date' '' '' '' ,
  'UZEIT'  'Plan.Time' 'Plan.Time' '' '' '' ,
  'STATUS' 'Status'    'Status'    '' '' '' .
```

#### 半自动：通过调用 Function Module 生成

```ABAP
FORM prepare_field_catalog CHANGING pt_fieldcat TYPE lvc_t_fcat.
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
	  " Exception handling"
	ENDIF. 
  LOOP AT pt_fieldcat INTO ls_fcat . 
	CASE pt_fieldcat-fieldname . 
	  WHEN 'CARRID' . 
		ls_fcat-outpulen = '10' . 
		ls_fcat-coltext = 'Airline Carrier ID' . 
		ls_fcat-edit = 'X' .
	    MODIFY pt_fieldcat FROM ls_fcat . 
	  WHEN 'PAYMENTSUM' . 
		ls_fcat-no_out = 'X' . 
		MODIFY pt_fieldcat FROM ls_fcat . 
	ENDCASE .
	CLEAR ls_fcat.
  ENDLOOP .
ENDFORM .
```

#### 自动生成

在调用显示 ALV 方法 set_table_for_first_display 时在输入参数 i_structure_name 中指点参数，将自动生成字段目录。**此参数的优先级最高**。 

- i_structure_name = 'XXXX'：指定输出表中数据的 DDIC 结构的名称。

### Layout 布局

定义 ALV Grid 的一般外观，需要在 `LVC_S_LAYO` 类型的结构中设置参数。 该结构包含此调整所服务的字段及其功能。

#### 设置布局

```ABAP
FORM prepare_layout.
  layout-zebra = 'X' .
  layout-cwidth_opt = 'X' .
  layout-grid_title = 'Flights' .
  layout-smalltitle = 'X' .
  layout-no_toolbar = 'X' . "隐藏全部工具条"
ENDFORM. "prepare_layout"
```

#### 选择方式

有时候，我们需要选择一些单元格，行或者列。在布局中，有个参数 SEL_MODE 可以设置我们不同的选择方式。

| Value | Desc                                                         |
| :---- | :----------------------------------------------------------- |
| SPACE | 等同于B，默认设置                                            |
| A     | 行和列的选择；无法选择单元格，多行，多列；用户可以使用最左边的选择按钮来选择多行 |
| B     | 单选；不可以多选行,不可以多选单元格，多行，多列              |
| C     | 多选；可以多选行,不可以多选单元格，多行，多列                |
| D     | 单元格的选择：可以多选单元格，多行，多列；任何单元格多选 用户可以使用最左边的选择按钮来选择多行 |

注意点：

- 如果设置了 ALV 是可编辑的，可能会覆盖在布局中选择方式的设置的
- 设置了选择方式以后，我们可以使用很多方法来获取用户的选择。比如GET_SELECTED_CELLS、GET_SELECTED_CELLS_ID、GET_SELECTED_ROWS、GET_SELECTED_COLUMNS
- 执行 PAI 以后，用户所选择的单元格、行或者列可能丢失。可以在PBO中，使用对应的 SET 方法来恢复这些选择

#### GUI Status：排除不需要的按钮

自定义按钮

```ABAP
DATA: lt_excl TYPE slis_t_extab.
APPEND 'CLOSE' TO lt_excl.
APPEND 'SALL'  TO lt_excl.
APPEND 'USAL'  TO lt_excl.
SET PF-STATUS 'STATUS' EXCLUDING lt_excl .
SET TITLEBAR 'TITLE'.
```

系统标准按钮

- 以 MC_FC_ 开头的名称是直接功能的名称。

- 以 MC_MB_ 开头的名称是包含一些子功能作为菜单项的功能菜单。

- 通过从后一种类型中排除一个，您可以排除其下的所有功能。

  ```ABAP
  FORM exclude_tb_functions CHANGING pt_exclude TYPE ui_functions . 
    DATA: ls_exclude TYPE ui_func.
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
    ls_exclude = cl_gui_alv_grid=>mc_fc_sort_asc.
    APPEND ls_exclude TO pt_exclude.
    ls_exclude = cl_gui_alv_grid=>mc_fc_sort_dsc .
    APPEND ls_exclude TO pt_exclude.
    ls_exclude = cl_gui_alv_grid=>mc_fc_find .
    APPEND ls_exclude TO pt_exclude.
    ls_exclude = cl_gui_alv_grid=>mc_fc_filter .
    APPEND ls_exclude TO pt_exclude.
    ls_exclude = cl_gui_alv_grid=>mc_fc_print .
    APPEND ls_exclude TO pt_exclude.
    ls_exclude = cl_gui_alv_grid=>mc_fc_print_prev .
    APPEND ls_exclude TO pt_exclude.
    ls_exclude = cl_gui_alv_grid=>mc_mb_export .
    APPEND ls_exclude TO pt_exclude.
    ls_exclude = cl_gui_alv_grid=>mc_fc_graph .
    APPEND ls_exclude TO pt_exclude.
    ls_exclude = cl_gui_alv_grid=>mc_mb_view .
    APPEND ls_exclude TO pt_exclude.
    ls_exclude = cl_gui_alv_grid=>mc_fc_detail .
    APPEND ls_exclude TO pt_exclude.
    ls_exclude = cl_gui_alv_grid=>mc_fc_help .
    APPEND ls_exclude TO pt_exclude.
    ls_exclude = cl_gui_alv_grid=>mc_fc_info .
    APPEND ls_exclude TO pt_exclude.
    ls_exclude = cl_gui_alv_grid=>mc_mb_variant.
    APPEND ls_exclude TO pt_exclude. 
  ENDFORM .
  ```

### ALV 显示

创建 ALV Grid 实例后，通过 CL_GUI_ALV_GRID 类的实例方法 `SET_TABLE_FOR_FIRST_DISPLAY` 来显示列表。 传递列表数据表、字段目录表、布局结构和附加信息等字段到方法中即可生成 ALV。

```ABAP
CALL METHOD go_grid->set_table_for_first_display 
   EXPORTING 
     i_structure_name              = 'XXXX'      "输出表中数据的DDIC结构的名称，自动生成字段目录。"
     is_variant                    = ls_variant  "指定布局变式"
     is_layout                     = layout      "布局设置"
     i_save                        = 'A'         "保存表格布局(X) & User-Spec(U)"
     i_default                     = 'X'         "是否允许用户定义默认布局"
     it_toolbar_excluding          = gt_exclude  "排除的按钮"
   CHANGING 
     it_outtab                     = gt_list[]   "需要显示的内表数据"
     it_fieldcatalog               = gt_fieldcat "结构字段"
     it_sort                       = gt_sort     "排序字段"
     it_filter                     = gt_filter   "初始化筛选器的列"
   EXCEPTIONS 
     invalid_parameter_combination = 1 
     program_error                 = 2 
     too_many_lines                = 3 
     OTHERS                        = 4.
```

#### 刷新 ALV 

我们不希望每次 PBO 触发时都创建我们的 ALV Grid。 第一遍应该创建它，其他时间使用时应该刷新。 但是，我们可能需要对外观、布局、字段目录等进行一些更改。 在这种情况下，我们将尝试使用其他 ALV 方法进行更改。

CL_GUI_ALV_GRID 重要方法：`REFRESH_TABLE_DISPLAY`

- IS_STABLE：有行、列两个参数，如果设置了相应的参数，对应的行或列属性就不会滚动。刷新的稳定性，就是滚动条保持不动。
- I_SOFT_REFRESH：软刷新。此参数仅在特殊情况下使用。 如果设置此参数，则刷新网格控件时，创建的任何总计、定义的任何排序顺序以及为显示的数据设置的任何过滤器都保持不变。 

```ABAP
CALL METHOD go_grid->refresh_table_display 
  EXPORTING
*   IS_STABLE = 
    I_SOFT_REFRESH = 'X'
  EXCEPTIONS
    finished = 1
    OTHERS = 2 .
IF sy-subrc <> 0.
  "Exception handling"
ENDIF.
```

### 其它功能

为了触发 ALV Grid 的一些附加功能，我们可以将一些附加数据作为参数传递。 例如，初始排序标准、要停用的按钮、布局颜色设置等...