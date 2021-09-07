---
title: " OO ALV 不同Container "
date: 2018-07-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

### 1.Custom container

自定义容器可以使用类 `CL_GUI_CUSTOM_CONTAINER` 创建，但它需要一个可以放置它的父容器，或者需要在自定义 Screen 中创建一个自定义控制区域。 这个解决方案主要是我现在开发的应用程序中使用的。 但在一个开发中将自定义容器与其他容器混合使用也很常见。

许多人在包含自定义容器区域的屏幕的 PBO 事件期间创建自定义容器，但您不必这样做。 您也可以在调用屏幕之前创建它，就像在使用 CL_GUI_CUSTOM_CONTAINER 的简单示例中一样，您可以在图 1 和演示程序 ZDEMO_AIN_CL01 中找到它。

不要试图在这个程序中找到任何东西，这不是为了在网格上快速显示数据所需要的。 没有 GUI 状态，没有 PBO 和 PAI 处理。 我故意这样做只是为了创建非常简单的示例。 

当然，为了能够显示网格，我必须创建一个 SCREEN 0100，其中包含名为 CC 的自定义容器区域，您可以在图 2 中找到它。我对屏幕所做的唯一附加设置是将其大小设置为 200 x 255 并且我已经提供了垂直和水平调整区域大小的可能性，因此它适合所有屏幕。

```ABAP
REPORT zdemo_ain_c101.
select * up to 50 rows form spfli int table @data(flights).
data(grid) = new cl_gui_alv_grid(
  i_parent = new cl_gui_customg_container( container_name = 'CC' )
).
grid->set_table_for_first_display(
  exporting
    i_structure_name    =   'SPFLI'
  changing
    it_outtab           =    flights
  exceptions
    invalid_parameter_combination = 1
    program_error                 = 2
    too_many_lines                = 3
    others                        = 4
).
IF sy-subrc = 0
  CALL SCREEN 0100.
ENDIF.
```

### 2.Splitter container

Splitter Container 可以使用类 `CL_GUI_SPLITTER_CONTAINER` 创建， 需要一个自定义容器作为父容器才能工作。 它用于将屏幕区域划分为多个容器。

为简化起见，你可以决定拆分器将有多少行和列。 所以它就像一个表格，甚至是 *HTML* 中的 *DIV*，您可以在其中放置您的内容。 您可以创建多级拆分器，因此如果您愿意，可以将区域拆分为两行一列，然后在顶行创建一个拆分器，该拆分器将分为两行三列。 您的想象力、屏幕尺寸和用户体验将提示您在这里进行最佳布局。

程序 *ZDEMO_AIN_CL02* 中的简单示例将创建一个具有两行一列的拆分器。 此处的 *SCREEN* 0100 与 *ZDEMO_AIN_CL01* 中的完全相同。

```ABAP
REPORT zdemo_ain_c102.
select * up to 50 rows form spfli int table @data(flights).
DATA:G_SPLITTER TYPE REF TO CL_GUI_SPLITTER_CONTAINER,
     G_CONTAINER_2000L TYPE REF TO CL_GUI_CONTAINER,
     G_CONTAINER_2000R TYPE REF TO CL_GUI_CONTAINER.
IF g_splitter IS INITIAL.
    CREATE OBJECT g_splitter  "定义一个屏幕包含两个ALV"
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
ENDIF.
data(grid) = new cl_gui_alv_grid(
  i_parent = g_container_2000l
).
grid->set_table_for_first_display(
  exporting
    i_structure_name    =   'SPFLI'
  changing
    it_outtab           =    flights
  exceptions
    invalid_parameter_combination = 1
    program_error                 = 2
    too_many_lines                = 3
    others                        = 4
).
IF sy-subrc = 0
  CALL SCREEN 0100.
ENDIF.
```

由于我没有对除行数和列数之外的拆分器进行任何编程，然后它会自动将自定义容器区域拆分为两个相等的部分，在顶行我们将看到网格，在底部将有 一个空的空间，因为我们没有在那里放任何东西。 

### 3.Docking container

Docking Container 可以使用类`CL_GUI_DOCKING_CONTAINER`创建，不需要任何父级，自定义屏幕上的自定义容器区域也不需要。 

创建和显示时，它停靠在屏幕的四个位置之一：顶部-top、底部-bottom、左侧- left、右侧-right。 在大多数情况下，停靠容器用于显示一些导航菜单，但由于可以将其用作网格父级，因此也可以使用它来显示其中一些有限数量的列。

```ABAP
REPORT zdemo_ain_c103.
PARAMETERS: dummy AS CHECKBOX.
AT SELECTION-SCREEN OUTPUT.
  select * up to 50 rows form spfli int table @data(flights).
data(docking) = new cl_gui_docking_container(
  side      = cl_gui_docking_container=>dock_at_left
  extension = 300
  repid     = sy-repid
  dynnr     = sy-dynnr
).
data(grid) = new cl_gui_alv_grid( i_parent = docking ).
grid->set_table_for_first_display(
  exporting
    i_structure_name    =   'SPFLI'
  changing
    it_outtab           =    flights
  exceptions
    invalid_parameter_combination = 1
    program_error                 = 2
    too_many_lines                = 3
    others                        = 4
).
IF sy-subrc = 0
ENDIF.
```

为了能够在不创建任何屏幕的情况下使用 Docking Container，我只添加了一个虚拟参数，并将创建容器的代码移到了在 SELECTION-SCREEN OUTPUT 事件中。 这样 Docking Container 在程序运行后直接出现。 正如您在图 8 中看到的，网格显示在 SCREEN 的左侧，而在右侧您可以找到虚拟的 PARAMETER。

### 4.Dialgobox container

对话框容器可以使用类`CL_GUI_DIALOGBOX_CONTAINER` 创建，如果你需要显示带有网格的弹出窗口，并且不想花时间创建带有自定义控件的屏幕，则该对话框会很有用。

在这种情况下使用它非常方便，但它也有一个限制，即这里没有可用的 GUI 工具栏。 

```ABAP
REPORT zdemo_ain_c103.
PARAMETERS: dummy AS CHECKBOX.
AT SELECTION-SCREEN OUTPUT.
  select * up to 50 rows form spfli int table @data(flights).
data(dialog) = new cl_gui_dialogbox_container(
  width  = 400
  height = 200
).
data(grid) = new cl_gui_alv_grid( i_parent = dialog ).
grid->set_table_for_first_display(
  exporting
    i_structure_name    =   'SPFLI'
  changing
    it_outtab           =    flights
  exceptions
    invalid_parameter_combination = 1
    program_error                 = 2
    too_many_lines                = 3
    others                        = 4
).
IF sy-subrc = 0
ENDIF.
```

 执行程序后，你可以找到带有网格的弹出屏幕。 如果需要，可以自由的调整其大小或最小化。