---
title: "OO ALV 使用"
date: 2018-07-04
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

**ALV GRID继承结构**    

![继承结构](/images/ABAP/OOALV1.png)

### 相关类

#### 容器类

- CL_GUI_CONTAINER：容器接口
- CL_GUI_CUSTOM_CONTAINER：用户自己定义控件区域


- CL_GUI_DOCKING_CONTAINER：动态创建容器，不需要在创建时绑定到预先绘制好的自定义控件中


- CL_GUI_SPLITTER_CONTAINER：可在同一屏幕创建多个ALV显示

#### ALV Grid 类


- CL_GUI_ALV_GRID：ALV控制类
- LVC_T_FCAT：ALV显示参照字段表
- LVC_S_FCAT：ALV显示参照字段结构
- LVC_S_LAYO：ALV显示布局结构
- LVC_T_SORT：排序条件字段
- LVC_T_FILT：过滤条件字段

### 控制区域、Container、ALV Grid 之间关系

作为控件对象，ALV Grid 实例需要一个容器来链接到屏幕。 

先在屏幕绘制一个用户自定义控件区域，然后以自定义控件区域为基础创建  CL_GUI_CUSTOM_CONTAINER 容器实例，最后以此容器实例来创建  CL_GUI_ALV_GRID 实例。

#### 在屏幕中定义 Customer Control

![屏幕定义](/images/ABAP/OOALV2.png)

#### 创建 SAP Container 实例

```ABAP
DATA: obj_container TYPE REF TO cl_gui_custom_container, "控制容器类"
      obj_alv     TYPE REF TO cl_gui_alv_grid, "ALV Grid控制类"
      gt_fieldcat TYPE lvc_t_fcat,   "字段目录内表"
      gs_fieldcat TYPE lvc_s_fcat,   "字段目录结构"
      gs_layout   TYPE lvc_s_layo,   "Layout 设置"
      gt_sort     TYPE lvc_t_sort,   "排序内表"
      gt_filt     TYPE lvc_t_filt,   "过滤内表"
      gt_exclude  TYPE ui_functions. 
* Creating custom container instance
IF obj_container IS INITIAL.
  CREATE OBJECT obj_container
    EXPORTING
      container_name  =  'USER_CONTAINER'. "自定义控件名称"
    EXCEPTONS
      cntl_error      = 1
      cntl_system_error = 2
      create_error = 3
      lifetime_error = 4
      lifetime_dynpro_dynpro_link = 5
      others = 6 .
  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno 
      WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENEIF.
ENDIF.
```

#### 创建 ALV GRID 实例

```ABAP
* Creating ALV Grid instance
IF obj_alv IS INITIAL.
  CREATE OBJECT obj_alv
    EXPORTING
      i_parent  = obj_container. "Parent为Container容器"
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
PERFORM prepare_layout CHANGING gs_layout .
* Preparing sort conditions
PERFORM prepare_sort_table CHANGING gt_sort .
* Additional preparations
```

### FieldCat 字段目录

使用一个内表来定义有关如何显示列表字段的规范。 这个内表被称为“字段目录”。 字段目录必须包含有关要显示的每一列的显示选项的一些技术和附加信息。 

字段目录的生成分为：自动生成、半自动生成、手动生成。

字段目录的内表需要参照字典类型 `LVC_T_FCAT`。

#### 手动生成：宏定义

```ABAP
DEFINE M_FIELDCAT.
  CLEAR: gs_fieldcat.
  gs_fieldcat-fieldname = &1 .
  gs_fieldcat-coltext   = &2 .
  gs_fieldcat-seltext   = &3 .
  gs_fieldcat-outputlen = &4 .
  gs_fieldcat-checkbox  = &5 .
  gs_fieldcat-edit      = &6 .
  APPEND gs_fieldcat TO gt_fieldcat.
END-OF-DEFINITION.
M_FIELDCAT:
  'DATUM'  'Plan.Date' 'Plan.Date' '' '' '' ,
  'UZEIT'  'Plan.Time' 'Plan.Time' '' '' '' ,
  'STATUS' 'Status'    'Status'    '' '' '' .
```

#### 半自动：通过调用 Function Module 生成

```ABAP
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
	  " Exception handling"
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
FORM prepare_layout CHANGING ps_layout TYPE lvc_s_layo.     
  ps_layout-zebra = 'X' .
  ps_layout-cwidth_opt = 'X' .
  ps_layout-grid_title = 'Flights' .
  ps_layout-smalltitle = 'X' .
ENDFORM. "prepare_layout"
```

#### 排除不需要的按钮

自定义按钮

```ABAP
DATA: lt_excl TYPE slis_t_extab.
  APPEND 'CLOSE' TO lt_excl.
  APPEND 'SALL.PUL' TO lt_excl.
  APPEND 'UALL.PUL' TO lt_excl.
SET PF-STATUS 'STATUS' EXCLUDING lt_excl .
SET TITLEBAR 'TITLE'.
```

系统标准按钮

- 以 MC_FC_ 开头的名称是直接功能的名称。
- 以 MC_MB_ 开头的名称是包含一些子功能作为菜单项的功能菜单。
-  通过从后一种类型中排除一个，您可以排除其下的所有功能。

```ABAP
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

### Sort 设置排序条件

可以为 ALV 数据设置排序条件。 初始化排序功能，在方法 set_table_for_first_display 中添加由系统标准的排序结构 `LVC_T_SORT` 生成的内表`GT_SORT`。

可以分别使用 `GET_SORT_CRITERIA` 和 `SET_SORT_CRITERIA` 方法获取和设置应用的排序标准。

```ABAP
FORM prepare_sort_table CHANGING pt_sort TYPE lvc_t_sort .
  DATA ls_sort TYPE lvc_s_sort .
  CLEAR: ls_sort,pt_sort.
  ls_sort-spos = '1' .
  ls_sort-fieldname = 'XXXX' .
  ls_sort-up = 'X' . "A to Z"
  ls_sort-down = space .
  APPEND ls_sort TO pt_sort .
  CLEAR ls_sort.
  ls_sort-spos = '2' .
  ls_sort-fieldname = 'XXXX' .
  ls_sort-up = space .
  ls_sort-down = 'X' . "Z to A"
  APPEND ls_sort TO pt_sort .
  CLEAR ls_sort.
ENDFORM. "prepare_sort_table"
```

#### 排序注意问题

- 如果要排序的任何一个字段不在字段目录中，程序会 Dump。

- 当使用 ALV Grid 对数据进行排序时，默认情况下它会垂直合并具有相同内容的字段。 为了避免所有的列都执行默认情况，可以设置布局结构的 `no_merging = 'X'`。 如果只想对某些列禁用合并，设置该列对应的字段目录行的 no_merging 字段即可。

### Filtering 设置过滤条件

过滤和排序的使用类似。 使用过滤条件时，必须填写参照类型 `LVC_T_FILT` 生成的内表。 填充此类型内表类似于填充 RANGES 变量。

可以分别使用 `GET_FILTER_CRITERIA` 和 `SET_FILTER_CRITERIA` 方法获取和设置应用的过滤条件。

```ABAP
FORM prepare_filter_table CHANGING pt_filt TYPE lvc_t_filt .
  DATA ls_filt TYPE lvc_s_filt .
  CLEAR: ls_filt,pt_filt .
  ls_filt-fieldname = 'FLDATE' .
  ls_filt-sign = 'E' .
  ls_filt-option = 'BT' .
  ls_filt-low = '20180101' .
  ls_filt-high = '20181231' .
  APPEND ls_filt TO pt_filt .
  CLEAR ls_filt.
ENDFORM.  " prepare_filter_table "
```

### ALV显示

创建 ALV Grid 实例后，通过调用实例的方法来显示列表。 传递列表数据表、字段目录表、布局结构和附加信息等字段到方法中。

#### 显示 ALV

CL_GUI_ALV_GRID 重要方法： `set_table_for_first_display`。

```ABAP
CALL METHOD obj_alv->set_table_for_first_display 
   EXPORTING 
     i_structure_name              = 'XXXX'      "输出表中数据的DDIC结构的名称，自动生成字段目录。"
     is_variant                    = ls_variant  "指定布局变式"
     is_layout                     = gs_layout   "布局设置"
     i_save                        = 'A'         "保存表格布局(X) & User-Spec(U)"
     i_default                     = 'X'         "是否允许用户定义默认布局"
     it_toolbar_excluding          = lt_exclude  "排除的按钮"
   CHANGING 
     it_outtab                     = gt_list[]   "需要显示的内表数据"
     it_fieldcatalog               = gt_fieldcat "结构字段"
     it_sort                       = gt_sort     "排序字段"
     it_filter                     = gt_filt     "初始化筛选器的列"
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
CALL METHOD obj_alv->refresh_table_display 
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

为了触发 ALV Grid 的一些附加功能，我们可以将一些附加数据作为参数传递。 例如，初始排序标准、要停用的按钮等...