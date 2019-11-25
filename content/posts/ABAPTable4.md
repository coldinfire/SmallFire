---
title: "报表开发<ALV显示设置>"
date: 2018-06-28
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---



##  ALV常使用的FM

<1> LVC_FIELDCATALOG_MERGE：根据内表结构返回FIELDCAT字段结构信息.

<2> REUSE_ALV_GRID_DISPLAY/REUSE_ALV_GRID_DISPLAY_LVC：输出ALV报表，定义其为GRID模式还是LIST模式。参数结构一样，只是在一些细节方面不一样。

```JS
类型池：TYPE-POOLS:SLIS.
REUSE_ALV_GRID_DISPLAY:
	DATA: gt_fieldcat TYPE slis_t_fieldcat_alv,
	 	  wa_fieldcat TYPE slis_fieldcat_alv,
	 	  gt_layout   TYPE slis_layout_alv.
	 	  
REUSE_ALV_GRID_DISPLAY_LVC:
	DATA: gt_fieldcat TYPE lvc_t_fcat,
	 	  wa_fieldcat TYPE lvc_s_fcat,
	 	  gt_layout   TYPE lvc_s_layo.
```



在ALV开发中有两个重要的对象：**LAYOUT**和**FIELDCAT,**它们同属于类型池**SLIS**。

- **LAYOUT**主要用于设置ALV的输出格式，如输出字段的颜色、表格中的线条等；

- **FIELDCAT**主要用于ALV结构定义，包括具体字段的名称、类型、格式等属性.
  常使用的开发类:

## Field Catalog 字段

```js
TYPE-POOLS: SLIS.
DATA:gt_fieldcat TYPE TABLE OF LVC_S_FCAT,
     gs_fieldcat TYPE LVC_S_FCAT.
```

FIELDCAT主要用于ALV结构定义，包括具体字段的名称、类型、格式等属性.

**常用参数列表**

```JS
☞ KEY：将定义字段设置为KEY值。
☞ ICON：将定义字段以ICON的形式显示。
☞ CHECKBOX：将定义字段以CHECKBOX的形式显示。
☞ JUST：定义字段对齐方式(R)RIGHT、(L)LEFT、(C)CENTER。
☞ IZERO：将定义字段以前导"0"的形式显示。
☞ NO_SIGN：将定义字段符号设置为不显示。
☞ NO_ZERO：如果取值为零，则为空，既不输出零。
☞ EMPHASIZE：设置字段的颜色。(列颜色)
☞ DO_SUM：对字段进行汇总。
☞ SELTEXT_L/M/S：设置字段名称描述长/中/短。
☞ DDICTXT：设置字段显示字符串。
☞ HOTSPOT：设置字段是否有热点(热点字段显示有下划线)。
☞ NO_OUT: 隐藏不需要的字段(NO_OUT = 'X')。
☞ EDIT(1) type c: 是否可编辑
☞ COL_POS like sy-cucol: 列输出位置
☞ FIX_COLUMN(1) type c: 列固定不滚动，颜色不会发生变化
☞ convexit : 设置转换规则，对应于Domain中的转换规则	

fieldname  type slis_fieldname    (列显示的设置)
cfieldname type slis_fieldname	(金额字段所参照的货币单位字段名)
ctabname   type slis_tabname	  (金额字段所参照的货币单位表名)
qfieldname type slis_fieldname	(数量字段所参照的货币单位字段名)
qtabname   type slis_tabname	  (数量字段所参照的货币单位表名)
```

**自定义FIELDCAT字段结构：**

​	定义宏来设置FIELDAT属性 &1 &2 &3分别为参数
​    

```JS
DATA: lt_alv_fieldcat TYPE SLIS_T_FIELDCAT_ALV WITH HEADER LINE. 
DEFINE fieldcatset.
   lt_alv_fieldcat-REF_TABNAME ='LSPFLI'.
   lt_alv_fieldcat-FIELDNAME = &1.
   lt_alv_fieldcat-SELTEXT_L = &2.
   lt_alv_fieldcat-COL_POS = &3.
   APPEND lt_alv_fieldcat.
   CLEAR lt_alv_fieldcat.
END-OF-DEFINITION.
  fieldcatset 'CARRID' '航线承运人' SY-TABIX.
```

**半自动创建：**

```JS
TYPE-POOLS: SLIS.
DATA:gt_fieldcat TYPE TABLE OF LVC_S_FCAT,
     gs_fieldcat TYPE LVC_S_FCAT.

CALL function 'LVC_FIELDCATALOG_MERGE'
 exporting
  "I_BUFFER_ACTIVE        = I_BUFFER_ACTIVE
  I_structure_name       = 'ZALV_FEED' "ALV需要显示的字段结构
  I_CLIENT_NEVER_DISPLAY = 'X'
  "I_BYPASSING_BUFFER     = I_BYPASSING_BUFFER
  "I_INTERNAL_TABNAME     = I_INTERNAL_TABNAME
 changing
      ct_fieldcat         = gt_fieldcat  "对应ALV显示的字段结构
 exceptions
  inconsistent_interface = 1
  program_error          = 2
  others                 = 3.

若对gt_fieldcat中某些字段有特殊设置，可LOOP内表按照要求更改参数值，Modify此内表。
 LOOP AT gt_fieldcat INTO gs_fieldcat.
    CASE gs_fieldcat-fieldname.
      WHEN 'MATNR'.
        ......
        MODIFY gt_fieldcat FROM gs_fieldcat.
        ......
    ENDCASE.
  ENDLOOP.
```

## Layout 字段

主要用于设置ALV的输出格式，如输出字段的颜色、表格中的线条等；

**常用参数列表**

```JS
☞ COLTAB_fieldname type slis_fieldname: 设置单元格颜色
☞ EDIT：设置ALV是否为可编辑模式。
☞ COLWIDTH_OPTIMIZE(CWITH_OPT)：将ALV字段宽度设置为最优化，按实际输出内容宽度自动匹配。
☞ NO_VLINE：输出ALV表格不显示垂直格式。
☞ NO_ULINE_HS：输出ALV表格不显示水平格线。
☞ INFO_FIELDNAME：设置颜色属性。
☞ KEY_HOTSPOT：设置关键字段热点。
☞ NO_COLNAME：是否显示字段名。
☞ ZEBRA：使ALV表格按斑马线间隔条纹方式显示，以便显示效果更有美观。
☞ BOX_FIELDNAME：设置ALV表格是否显示选择按钮字段。
☞ f2code  like   sy-ucomn. gs_layout-f2code='&ETA'[双击时触发的funcode]
☞ INFO_FIELDNAME：用于设置ALV输出报表每一行的颜色，其参数为输出内表的字段名称，
   要注意的是使用该属性需要同时在内表中定义一个与该参数所定义字段名相同的字段，例如：
   LAYOUT-INFO_FIELDNAME = 'COLOR'.倘若其数据输出内表名为LT_OUT,则需要在该内表增加一字段
   “COLOR”，并为其内表每行复制，颜色参数范围C000~C999，例如：LT_OUT-COLOR = 'C012'.
```
**【颜色】**

​	[颜色设置](http://blog.sina.com.cn/s/blog_3f2c03e30100mk1s.html)

- 行颜色:gs_layout-<Color_fieldname> = 'COLOR'

  - ALV 中的每行数据颜色是通过 LayOut 来控制的。需要在显示输出内表结构中增加一列字段，用来存储数据行的颜色

  - 颜色值定义为 4 位字符，首位固定为字母 “C”，第 2 位为颜色，由 0~7 表示，不同的数字表示不同的颜色属性，如：

    0 = background color     1 = Gray-blue     2 = Light gray    3 = yellow   4 = blue-gray        5 = green       6 = red      7 = orange

    第 3 位表示输出文字是否高亮显示，由 0~1 表示，为 1 时表示高亮显示。第 4 位测试了一下，基本上 0~9 颜色都差不多，唯一就是当取值为 1 时，底色又回到了灰色（且只是在第 3 位为 0 时才有此效果）。

- 列颜色:gt_fieldcat-emphasize = 'C510'.[1：C固定，2：颜色值0~7,3：高亮0、1(X)，4：颜色反转，0、1]

- 单元格颜色:gs_layout-<coltab_fieldname>='COLORTABLE'.

**【可编辑】**

​	整体可编辑：gs_layout-edit = 'X' 

​	某列可编辑：gt_fieldcat-edit = 'X'

​	单元格可编辑：需要通过OO方式实现

## 设置工具导航栏 GUI Status

#### Copy SAP标准GUI Status

- SE80 -> SALV -> STANDARD -> Copy到自定义程序的GUI Status

![GUI](/images/ABAP/CopyGUI.png)

GUI Status参数设置共包括3个部分：

1. 菜单栏(Menu Bar)：用于设置主菜单选项。

2. 应用工具条(Application ToolBar)：用于设置应用工具栏按钮，包括按钮名称、按钮描述、及按钮所对的ICON图标。

3. 功能键(Function Key)：为按钮分配功能键代码，包括系统标题按钮(如返回、退出、关闭等)及通过Application ToolBar所定义的客制化按钮。

 **在ALV函数中使用**

```jsp
call function 'REUSE_ALV_GRID_DISPLAY_LVC'
  exporting
    i_callback_program       = sy-repid 
    i_callback_pf_status_set = 'FRM_SET_STATUS'
    i_callback_user_command  = 'FRM_USER_COMMAND'
    is_layout_lvc            = lw_layout
    it_fieldcat_lvc          = gt_fieldcat
    i_default                = 'X'
    i_save                   = 'X'
  tables
    t_outtab                 = gt_alv_data
  exceptions
    program_error            = 1.
      
GUI TITLE设置：
   GUI TITLE 用于定义Report标题栏内容.
 定义：
   Create-->GUI Titles：可以输入&符号作为Title,当程序运行时对其填充动态文本。
 在程序中调用：
   SET TITLEBAR 'TITLE_BAR' WITH SY-DATUM 'IFENER' 'BAR TEST'."设置TITLEBAR，并赋参数列表 
```

**自定义状态栏**

```JS
form frm_set_status using excluding .
  form frm_set_status using extab type slis_t_extab.
  data: ls_slis_extab type slis_extab.
  "排除按钮不显示
  ls_slis_extab-fcode = '&ABC'.
  APPEND ls_slis_extab TO extab.
  ......
  SET TITLEBAR 'TITLE' WITH text-t01. "输入标题栏名称
  SET PF-STATUS <STATUS_NAME> EXCLUDING extab. "输入自定义按钮工具的名称,并排除指定按钮
endform.                    "frm_set_status
```

**按钮处理** 

```JS
Form frm_set_command using p_ucomm type sy-ucomm
                           i_selfield type slis_selfield.
  data:ls_stable type lvc_s_stbl.
  data:lr_grid type ref to cl_gui_alv_grid.

  call function 'GET_GLOBALS_FROM_SLVC_FULLSCR'
    importing
      e_grid = lr_grid.
  call method lr_grid->check_changed_data.

 CALL METHOD LR_GRID->REFRESH_TABLE_DISPLAY.
  i_selfield-refresh = 'X'.  "自动刷新

  case p_ucomm.
    when 'ZPRT'.
      read table gt_disp into gs_disp with key check_box = 'X'.
      if sy-subrc = 0.
        perform frm_print_data.
      else.
        message e398(00) display like 'E'.
        return.
      endif.
    when '&IC1'. "对操作代码进行相应处理
	  IF i_selfield-fieldname EQ 'FieldName' AND i_selfield-value NE space.
      	DATA: ls_upload LIKE LINE OF gt_upload.
 		READ TABLE gt_upload INTO ls_upload INDEX rs_selfield-tabindex.
  		CHECK sy-subrc EQ 0 AND ls_upload-aufnr NE space.
        "传入输入参数值并调用其他TCode
 	    SET PARAMETER ID 'ANR' FIELD ls_upload-aufnr.
  	    CALL TRANSACTION 'CO03' AND SKIP FIRST SCREEN."跳过第一屏屏幕
      ENDIF.
  endcase.
 call method lr_grid->refresh_table_display.
endform.
```



**自定义按钮**

对于定义的按钮，我们可以通过系统变量SY-UCOMM来获取它的功能代码。

```js
AT USER-COMMAND.   "当单击某个按钮时，触发该事件
  CASE sy-ucomm.  "获取所操作按钮的功能代码(FUNCTION Code)
```
-   调用显示，应用于START-OF-SELECTION事件
    

​	`SET PF-STATUS <STATUS_NAME>.
   `  

​	不显示某些按钮：`SET PF-STATUS <STATUS_NAME> EXCLUDING <extab>.`

### 文本增强

- T-CODE:CMOD->转到->文本增强->关键字->更改