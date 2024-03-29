---
title: " SALV 使用简介 "
date: 2019-06-03
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

引用自：[https://www.cnblogs.com/jiangzhengjun/p/4291387.html](https://www.cnblogs.com/jiangzhengjun/p/4291387.html)

### 简介

SALV 可以使用函数方式生成 ALV ，不用创建屏幕就可以调用的全屏方式显示的 ALV。

SALV 的 GRID 报表可以在后台运行，但以前函数方式或 OO 方式生成的 GRID 不能。

SALV 与现有的方法（Function ALV）相比，为了方便以接口的方式提供更整合及细微的功能。

SALV 不提供编辑功能，但可以通过 SALV 适配器调用 CL_GUI_ALV_GRID 修改成编辑模式，就可以在 ALV 中修改数据了。

这些新的面向对象的 ALV 模型具有许多优点：

- 简化设计：该模型使用高度集成的面向对象设计，这使程序员可以轻松开发 ALV
- 统一对象模型：此模型只有一个主类，该主类将获取并设置整个布局的参数

### 主要的报表类型

| ALV 类型               | 类型描述                 |
| :--------------------- | :----------------------- |
| CL_SALV_TABLE          | 简单的ALV报表类型        |
| CL_SALV_HIERSEQU_TABLE | 以层次结构显示的报表类型 |
| CL_SALV_TREE           | 以树形结构显示的报表类型 |

要显示ALV，只需要调用以下两个方法即可：

![CL_SALV_TABLE](/images/ABAP/SALV1.png)

- FACTORY：静态方法。定义要显示在屏幕上的数据；定义 ALV 报表样式；调用此方法就会返回 CL_SALV_TABLE 类型的实例对象
- DISPLAY：实例方法。由静态方法产生的实例对象调用该方法显示 ALV

#### FACTORY 静态方法

所有类都有静态方法 FACTORY，该方法将取回 ALV 的实例。调用方法 `CL_SALV_TABLE => FACTORY( ). `来获取 ALV 的实例。返回值 R_SALV_TABLE 保存产生的实例对象。

 有三种显示方法可以通过传入参数设置：

- LIST_DISPLAY：该参数决定了列表显示的模式是以 List 普通列表方式显示，还是 Grid 网格方式显示
- R_CONTAINER：用户自定义控件区域的引用对象，类型为 CL_GUI_CONTAINER
- CONTAINER_NAME：屏幕上用户自定义控件区域（Custom Control）的名称

| Factory 参数   | 全屏模式   | List列表  | 利用控制器的模式          |
| :------------- | :--------- | :-------- | :------------------------ |
| LIST_DISPLAY   | ABAP_FALSE | ABAP_TRUE | ABAP_FALSE                |
| R_CONTAINER    | 初始值     | 初始值    | CL_GUI_CONTAINER 对象引用 |
| CONTAINER_NAME | 初始值     | 初始值    | Custom Control name       |

#### DISPLAY 方法显示 ALV

实例方法，调用此方法显示 ALV。

- SALV 的列数最多只能显示 90 列

- SALV 每个单元格最长输出 128 个字符

- 排序和小记(sort 和 subtotals)最多 9 层或 9 列

- 合计或小记的字段长度一定要够长，防止溢出
- SALV 中的小记(subtotals)和合计(totals)的值不能返回到程序中

- SALV 显示的字段一定要是 flat 的不能是 deep 的，也就是字段不能是表和结构。
- SALV 如果用了 grid 网格形式显示，是不支持后台 job 的

#### 使用 SALV 实例

参数定义

```ABAP
************************************************************************
**   Global References for Classes                                    **
************************************************************************
DATA: gr_table     TYPE REF TO cl_salv_table.
DATA: gr_display   TYPE REF TO cl_salv_display_settings.
DATA: gr_functions TYPE REF TO cl_salv_functions_list.
DATA: gr_columns   TYPE REF TO cl_salv_columns_table.
DATA: gr_column    TYPE REF TO cl_salv_column_table.
DATA: gr_layout    TYPE REF TO cl_salv_layout.
DATA: gr_sort      TYPE REF TO cl_salv_sorts.
DATA: gr_filter    TYPE REF TO cl_salv_filters.
DATA: gr_aggrs     TYPE REF TO cl_salv_aggregations.
DATA: gr_events    TYPE REF TO cl_salv_events_table.
DATA: gr_container TYPE REF TO cl_gui_custom_container.
```

*SALV 简单使用*

```ABAP
START-OF-SELECTION.
   "SALV 通过factory获取实例对象"
   cl_salv_table=>factory( IMPORTING r_salv_table = gr_table 
                           CHANGING t_table = source_table ).
   "SALV 设置工具栏：激活所有的ALV内置通用按钮"
   gr_functions = gr_table->get_functions( ).
   gr_functions->set_all( abap_true ).
   "SALV 显示设置"
   gr_display = gr_table->get_display_settings( ).
   gr_display->set_list_header( 'This is our custom heading' ).
   gr_display->set_striped_pattern( abap_true ).
   "SALV 字段设置"
   gr_columns = gr_table->get_columns( ).
   gr_column ?= gr_columns->get_column( 'c_name' ).
   gr_column->set_long_text( 'long text' ).
   gr_column->set_medium_text( 'medium text' ).
   gr_column->set_short_text( 'short text' ).
   "SALV 排序字段设置"
   gr_sort = gr_table->get_sorts( ).
   gr_sort->add_sort( columnname = 'CITYTO' subtotal = abap_true ).
   "SALV 聚合字段设置"
   gr_aggrs = gr_table->get_aggregations( ).
   gr_aggrs->add_aggregation( 'DISTANCE' ).
   "SALV Layout 设置"
   gr_layout =  gr_table->get_layout( ).
   lv_key-report = sy-repid.
   gr_layout->set_key( lv_key ).
   gr_layout->set_save_restriction( cl_salv_layout=>restrict_none ).
   "SALV 显示"
   gr_table->display( ).
```

*使用 Container*

```ABAP
" 判断容器是否已分配了一个有效引用 "
IF gr_container IS NOT BOUND.
  "创建容器"
  CREATE OBJECT gr_container
    EXPORTING
      container_name = 'CONTAINER_1'. " 屏幕上用户自定义控件名 "
ENDIF.      
"创建 ALV"
cl_salv_table=>factory(
  EXPORTING
    r_container = gr_container
*   list_display = abap_true "以列表形式显示"
    container_name = 'CONTAINER_1'
  IMPORTING
      r_salv_table = gr_table
    CHANGING
      t_table = gt_data[] ).
"设置工具栏：激活所有的ALV内置通用按钮"
gr_functions = gr_table->get_functions( ).
gr_functions->set_all( abap_true ). 
"显示 ALV"
gr_table->display( ).
```

### 设置相关属性方法

对 SALV 进行设置，先需要通过 CL_SALV_TABLE 对象实例获取相关设置对象，然后再对这些设置对进行操作。

如对样式的设置，需要先通过 CL_SALV_TABLE 实例对象的 GET_LAYOUT( ) 方法拿到对应的对象，实后通过该对象相关方法对 SALV 进行设置；除样式外，如排序、工具栏设置、事件等，都是先通过某个 GET 方法拿到相应对象，再通过该对象相关方法对 SALV 进行设置，这与以前 Function ALV、及 OO ALV 不太一样。

![CL_SALV_TABLE Methods](/images/ABAP/SALV2.png)

