---
title: "报表开发(六) OO ALV"

date: 2018-09-25T17:20:58+08:00

draft: false

author: Small Fire

isCJKLanguage: true

categories: 

  - ABAP

tags: 

  - 报表开发
---



## OO ALV

```
相关类：
  CL_GUI_CUSTOM_CONTAINER   ：用户自己定义控件区域
  CL_GUI_DOCKING_CONTAINER  : 动态创建容器，不需要在创建时绑定到预先绘制好的自定义控件中
  CL_GUI_SPLITTER_CONTAINER ：可在同一屏幕创建多个ALV显示
  CL_GUI_ALV_GRID           ：ALV控制类

  DATA : obj_wcl_container TYPE REF TO cl_gui_custom_container, "控制容器类
         obj_wcl_alv TYPE REF TO cl_gui_alv_grid . "ALV控制类
  DATA GS_LAYOUT1 TYPE LVC_S_LAYO. "布局结构
  DATA GT_FIELDCAT TYPE LVC_T_FCAT. "存放字段目录的内表
  DATA GS_FIELDCAT TYPE LVC_S_FCAT.
  DATA lt_exclude TYPE ui_functions. "alv不需要的图标按钮
1、控制区域、容器、Grid关系
   先在屏幕绘制一个用户自定义控件区域，然后以自定义区域为基础创建 CL_GUI_CUSTOM_CONTAINER
 容器实例,最后以此容器实例来创建 CL_GUI_ALV_GRID实例。
    DATA : obj_wcl_container TYPE REF TO cl_gui_custom_container, "控制容器类
           obj_wcl_alv TYPE REF TO cl_gui_alv_grid . "ALV控制类
    IF obj_wcl_alv IS INITIAL.
       CREATE OBJECT obj_wcl_container
          EXPORTING
             container_name  =  'OBJ_WCL_CONTAINER'.
       CREATE OBJECT obj_wcl_alv
          EXPORTING
             i_parent  = obj_wcl_container.
2、给ALV对象注册事件
  (1) HANDLE_TOOLBAR:这个事件用于给ALV加自定义工具条按钮。
  (2) HANDLE_CLICK:用于给ALV点击其中一行后处理代码段。
  (3) HANDLE_COMMAND:事件用于接收用户按了自定义按钮后，触发的代码段。
  (4) HANDLE_DOUBLE_CLICK：双击事件
  (5) 
  事件的使用：
    <1> 定义事件方法
    <2> 指定事件的执行方法代码
    <3> 事件变量实例化
    <4> 把事件指定到ALV控制中(注册事件)

​```
CREATE OBJECT event_receiver.
SET HANDLER event_receiver -> handle_double_click FOR obj_wcl_alv.
​```

3、CL_GUI_ALV_GRID重要方法
  *-----显示ALV 
     CALL METHOD obj_wcl_alv->set_table_for_first_display 
        EXPORTING 
           is_variant                    = ls_variant   :指定布局变式
           is_layout                     = ls_layout 
           i_save                        = 'A'          :保存表格布局
           it_toolbar_excluding          = lt_exclude 
        CHANGING 
           it_outtab                     = gt_list[]    :需要显示的内表数据
           it_fieldcatalog               = lt_fieldcat  :结构字段
        EXCEPTIONS 
           invalid_parameter_combination = 1 
           program_error                 = 2 
           too_many_lines                = 3 
           OTHERS                        = 4.

   ENDFORM.                    " FRM_ALV_DISPLAY

  XXX -> REFRESH_TABLE_DISPLAY.
    IS_STABLE : 刷新的稳定性，就是滚动条保持不动
    I_SOFT_REFRESH:  软刷新，临时给ALV创建的合计，排序，等保持不变
```

