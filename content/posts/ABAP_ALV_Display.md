---
title: " 报表开发<ALV 显示设置> "
date: 2018-06-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

##  ALV 显示

### ALV 显示相关的 FM

**REUSE_ALV_LIST_DISPLAY**：List 格式的 ALV 模式为固定格式，应用于较严格的标准报表。

**REUSE_ALV_GRID_DISPLAY**：Grid 格式的 ALV 报表有栏位选择按钮功能，允许用户直接输出格式，操作更为灵活。

**REUSE_ALV_GRID_DISPLAY_LVC**：以 LVC 结尾的 Grid 格式的 ALV 报表，此函数中引用到的类型大部分都不再从类型池 slis 中来引用，而是直接引用字典中已定义好的表或结构类型，这种函数与面向对象的 CL_GUI_ALV_GRID 生成的 ALV 参数类型上基本相同，所以以后一般如果使用函数方式来产生 ALV，推荐使用 REUSE_ALV_GRID_DISPLAY_LVC 函数，因为这样方便修改面向对象方式的 ALV。

### FIELDCATALOG 创建的函数

如果 ALV 所要展示的列过多时，建议先在数据字典系统中创建相应的 Structure，这样可免去对输出列表头信息的繁琐编辑处理，代码行也会缩短。

**REUSE_ALV_FIELDCATALOG_MERGE**：根据定义的结构生成 FIELDCAT 字段结构信息。

**LVC_FIELDCATALOG_MERGE**：针对 REUSE_ALV_GRID_DISPLAY_LVC 函数，生成 FIELDCAT 字段结构信信息。

- 对于 **SLIS** 开头的内表或结构，可以在类型池 SLIS 中查看详细信息；

- **LVC** 开头的可以在数据字典（**SE11**）中查看结构参数。

### 字段目录和布局

在 ALV 开发中有两个重要的对象： **LAYOUT** 和 **FIELDCATALOG** 。

- LAYOUT：主要用于设置 ALV 的输出格式，如输出字段的颜色、表格中的线条等，为 ALV 输出的可选项。
- FIELDCATALOG：主要用于 ALV 结构定义，包括具体字段的名称、类型、格式等属性，与 Layout 不一样的是输出格式只针对某个字段。为 ALV 输出的必选项。

```ABAP
TYPE-POOLS:SLIS.
"REUSE_ALV_GRID_DISPLAY"
DATA: gt_fieldcat TYPE slis_t_fieldcat_alv,
      gs_fieldcat TYPE slis_fieldcat_alv,
      layout   TYPE slis_layout_alv.
"REUSE_ALV_GRID_DISPLAY_LVC"
DATA: gt_lvc_fieldcat TYPE lvc_t_fcat,
      gs_lvc_fieldcat TYPE lvc_s_fcat,
      layout   TYPE lvc_s_layo.
```

## Field Catalog 字段

### SLIS Field Catalog 常用参数列表

| 字段名称                    | 描述                                                  | 输入值                                 |
| --------------------------- | ----------------------------------------------------- | -------------------------------------- |
| ROW_POS(10)                 | 行输出位置                                            | 1….n                                   |
| COL_POS(10)                 | 列输出位置                                            | 1….n                                   |
| **FIELDNAME**(30)           | 针对输出内表列进行设置，只有设置了的列才会显示在ALV中 | 内表中定义的字段名                     |
| **TABNAME** (30)            | Fieldname字段对应的内表名称                           | 内表名称                               |
| **CURRENCY**(5)             | 货币单位                                              | 表 TCURX 中的货币名称                  |
| **CFIELDNAME**(30)          | 货币单位字段名 Currency unit field name               | 当前输出内表中的货币单位字段的字段名称 |
| **CTABNAME**(30)            | 货币单位表名 Currency unit table name                 | CFIELDNAME字段值对应的内表名称         |
| **QUANTITY**(3)             | 计量单位                                              |                                        |
| **QFIELDNAME**(30)          | 参考计量单位的字段名称                                | 当前输出内表中的计量单位字段名         |
| **QTABNAME**(30)            | 计量单位表名                                          | QFIELDNAME对应的输出内表名             |
| **REF_TABNAME**             | 参考表名称                                            | 内部表字段的参考字段名称               |
| **REF_FIELDNAME**           | 参考字段名称，单元格生成F4帮助                        | 内部表字段的参考字段名称               |
| **SELTEXT_L/M/S**(40/20/10) | 设置字段名称描述长/中/短                              | 字段描述                               |
| **DDICTXT**(1)              | 直接指定列标题描述格式，指定后则保持固定不变          | 取值为S、M、L                          |
| DDIC_OUTPUTLEN              | 数据字典输出长度                                      |                                        |
| **NO_OUT**(1)               | 当前列隐藏输出                                        | X-隐藏，space-不隐藏                   |
| **KEY**(1)                  | 将定义字段设置为KEY值                                 | X-设置，space-不设置                   |
| ICON(1)                     | 将定义字段以ICON的形式显示,INCLUDE  list              | X-设置，space-不设置                   |
| CHECKBOX(1)                 | 将定义字段以CHECKBOX的形式显示                        | X-设置，space-不设置                   |
| EDIT(1)                     | 是否可编辑                                            | X-可编辑，space-不可编辑               |
| INPUT(1)                    | 输入                                                  | X-设置，space-不设置                   |
| OUTPUTLEN(6)                | 列的字符输出宽度                                      | 1….n                                   |
| OFFSET(6)                   | 设置偏移量                                            |                                        |
| **TEXT_FIELDNAME**          | 文本字段名称                                          |                                        |
| **REPTEXT_DDIC**            | 与数据元素的主标题类似                                |                                        |
| FIX_COLUMN(1)               | 列固定不滚动，颜色不会发生变化                        | X-设置，space-不设置                   |
| JUST(1)                     | 定义字段对齐方式                                      | 取值为R、L、C                          |
| **TECH**(1)                 | 置为技术列的列将不会再显示出来                        | X-设置，space-不设置                   |
| LZERO(1)                    | 输出前导零，仅NUMC类型字段有效                        | X-设置，space-不设置                   |
| NO_SIGN(1)                  | 将定义字段符号设置为不显示                            | X-设置，space-不设置                   |
| NO_ZERO(1)                  | 如果取值为零，则输出为空                              | X-设置，space-不设置                   |
| NO_SUM(1)                   | 设置当前列输出时不自动求和                            | X-设置，space-不设置                   |
| HOTSPOT(1)                  | 设置字段是否有热点，热点字段显示有下划线              | X-设置，FCode “**&IC1**“               |
| **ROUND**(I)                | 四舍五入保留位数                                      |                                        |
| DECIMALS_OUT(6)             | 控制小数点的位数                                      |                                        |
| EMPHASIZE                   | 设置字段的颜色(列颜色)                                | C(1-7)(0-1)(0-1)                       |
| NO_CONVEXT(1)               | 设置转换规则，对应于Domain中的转换规则                | X-不设置，space-设置                   |
| LOWERCASE(1)                | 是否允许小写字母                                      | X-允许，space-不允许                   |

### 定义字段目录

#### 手动生成：宏定义

通过宏来设置 FIELDCATALOG 字段属性。

```ABAP
DEFINE fieldcat.
  clear gs_fieldcat.
  gs_fieldcat-fieldname = &1.
  gs_fieldcat-seltext_l = &2.
  gs_fieldcat-seltext_m = &2.
  gs_fieldcat-seltext_s = &2.
  gs_fieldcat-col_pos = &3.
  gs_fieldcat-ref_tabname ='LSPFLI'.
  APPEND gs_fieldcat TO gt_fieldcat.
END-OF-DEFINITION.
fieldcat 'CARRID' '航线承运人' SY-TABIX.
```

#### 半自动创建：参考数据字典中的结构/透明表

调用 FM：REUSE_ALV_FIELDCATALOG_MERGE 根据参数指定的结构或则透明表生成对应的 Field Catalog。

使用数据字典中的结构、透明表、视图时

- 直接使用，生成和结构、透明表、视图中定义的字段相同类型的  Field Catalog。

使用自定义结构或内表时

- 必须保证该结构或内表中的每个字段的定义只能使用 LIKE 操作符。
- 使用 TYPE 操作符时，该字段在使用 REUSE_ALV_FIELDCATALOG_MERGE 函数时将被忽略(不参照字典类型直接定义类型的除外)。

```ABAP
TYPE-POOLS: SLIS.
DATA:gt_fieldcat TYPE TYPE slis_t_fieldcat_alv,
     gs_fieldcat TYPE slis_fieldcat_alv.
CLEAR gt_fieldcat.
CALL function 'REUSE_ALV_FIELDCATALOG_MERGE'
  EXPORTING
    I_PROGRAM_NAME         =  SY-REPID
    I_INTERNAL_TABNAME     = 'INTERNAL_TABNAME' "显示输出的内表名，要大写"
" 如果定义的显示输出内表是参照的数据字典中的structure, table, view时，才需要指定 Structure"
    I_STRUCTURE_NAME       = 'ZALV_TB'  "ALV需要显示的字段结构"
    I_CLIENT_NEVER_DISPLAY = 'X'
  "I_BYPASSING_BUFFER     = I_BYPASSING_BUFFER"
  "I_BUFFER_ACTIVE        = ' '"
   CHANGING
      ct_fieldcat          = gt_fieldcat  "对应ALV显示的字段结构"
   EXCEPTIONS
      inconsistent_interface  = 1
      program_error           = 2
      others                  = 3.
* 若对某些字段有特殊设置，可循环内表按照要求更改参数值。
LOOP AT gt_fieldcat INTO gs_fieldcat.
  CASE gs_fieldcat-fieldname.
    WHEN 'MATNR'.
      ......
      MODIFY gt_fieldcat FROM gs_fieldcat.
      ......
    WHEN OTHERS.
  ENDCASE.
  CLEAR gs_fieldcat.
ENDLOOP.
```

### LVC_S_FCAT: 字段参数

大部分和 **SLIS** 中定义的一致，下面列出部分不一致的字段。

| 字段名称                | 描述                                       |
| ----------------------- | ------------------------------------------ |
| REF_FIELD               | 参考字段名称，配合REF_TABLE使用            |
| REF_TABLE               | 参考表名称，配合REF_FIELD使用              |
| COLTEXT                 | ALV control: 列标题                        |
| REPTEXT                 | 字段标题                                   |
| SCRTEXT_L/M/S(40/20/10) | 设置字段名称描述长/中/短                   |
| DRDN_HNDL               | 自然数，下拉框的句柄                       |
| DRDN_FIELD              | ALV 控制，内表表字段的字段名称，下拉的字段 |
| F4AVAILABL              | X：此列有搜索帮助                          |

#### 手动生成： FIELDCAT 字段结构

```ABAP
DEFINE fieldcat.
   CLEAR gs_lvc_fieldcat.
   gs_lvc_fieldcat-FIELDNAME = &1.
   gs_lvc_fieldcat-SCRTEXT_L = &2.
   gs_lvc_fieldcat-SCRTEXT_M = &2.
   gs_lvc_fieldcat-SCRTEXT_S = &2.
   gs_lvc_fieldcat-COL_POS = &3.
   APPEND gs_lvc_fieldcat TO gt_lvc_fieldcat.
END-OF-DEFINITION.
fieldcat 'CARRID' '航线承运人' SY-TABIX.
```

#### 半自动创建：直接参考数据字典中的现有透明表

既可以根据定义的内表，也可以根据数据字典存在的结构或表，视图创建。

```ABAP
DATA:gt_lvc_fieldcat TYPE LVC_T_FCAT,
     gs_lvc_fieldcat TYPE LVC_S_FCAT.
CLEAR gt_lvc_fieldcat.
CALL function 'LVC_FIELDCATALOG_MERGE'
  EXPORTING
    "I_BUFFER_ACTIVE        = I_BUFFER_ACTIVE"
    I_STRUCTURE_NAME       = 'ZALV_ST'  "ALV需要显示的字段结构"
    I_CLIENT_NEVER_DISPLAY = 'X'
    "I_BYPASSING_BUFFER     = I_BYPASSING_BUFFER"
    "I_INTERNAL_TABNAME     = 'INTERNAL_TABNAME' 
  CHANGING
    ct_fieldcat            = gt_lvc_fieldcat  "对应ALV显示的字段结构"
  EXCEPTIONS
    inconsistent_interface = 1
    program_error          = 2.
```

## Layout 字段

### SLIS Layout 常用参数列表

| 字段名称                 | 描述                                         | 输入值                              |
| ------------------------ | -------------------------------------------- | ----------------------------------- |
| **ZEBRA**(1)             | 使ALV表格按斑马线间隔条纹方式显示            | X-设置，space-不设置                |
| **EDIT**(1)              | 设置ALV单元格整体为可编辑模式                | X-可编辑，space-不可编辑            |
| NO_VLINE(1)              | 设置列间网格线                               | X-不显示，space-显示                |
| NO_HLINE(1)              | 设置行间网格线                               | X-不显示，space-显示                |
| NO_COLHEAD(1)            | 设置不显示标题                               | X-不显示，space-显示                |
| NO_HOTSPOT(1)            | 标题不设置热点                               | X-不设置，space-设置                |
| NO_INPUT(1)              | 输出的ALV不能编辑，只显示数据                | X-不允许，space-允许                |
| F2CODE                   | 设置触发弹出详细信息窗口的功能码             | '&ETA'：双击时触发的funcode         |
| NO_KEYFIX(1)             | 关键字不固定，可以随滚动条滚动               | X-不固定，space-固定                |
| **NUMC_SUM**(1)          | 设置仅NUMC类型字段进行总计                   | X-仅Numc类型，space-不仅Numc类型    |
| **COLWIDTH_OPTIMIZE**(1) | 优化列宽设置                                 | X-优化                              |
| NO_ULINE_HS(1)           | 输出ALV表不显示水平格线                      | X-不显示，space-显示                |
| MIN_LINESIZE(sy-linsz)   | ALV列表的最小宽度                            | 取值10到250                         |
| MAX_LINESIZE(sy-linsz)   | ALV列表的最大宽度                            | 取值80到1020，默认250               |
| **Lights_fieldname**     | 输出内表中定义的字段名，该列用来显示状态灯   | 1：red，2：yellow，3：green         |
| Lights_tabname           | 输出字段的参考内表名称                       |                                     |
| Lights_rollname          | 数据元素的名称，在灯字段按F1触发             |                                     |
| Lights_condense(1)       | 对输出的内表分类汇总的时候，小计行显示状态灯 | X-小计行显示                        |
| **BOX_FIELDNAME**        | 设置数据内表中哪列以选择按钮形式显示         |                                     |
| BOX_TABNAME              | BOX_FIELDNAME参考内表名称                    |                                     |
| **KEY_HOTSPOT** (1)      | 关键字段作为热点，可单击                     |                                     |
| HOTSPOT_FIELDNAME        | 热点字段设置，可单击                         |                                     |
| DETAIL_POPUP(1)          | 行项目明细弹窗形式                           | X-显示，space-不显示;对list_alv有效 |
| DETAIL_INITIAL_LINES(1)  | 明细中同时显示初始化行                       | X-同时显示，space-不显示            |
| DETAIL_TITLEBAR          | 明细窗口标题文本                             |                                     |
| NO_SUMCHOICE(1)          | 不能进行选择总计                             | X-设置                              |
| NO_TOTALLINE(1)          | 不能总计，但可以小计                         | X-设置                              |
| NO_SUBCHOICE(1)          | 不能选择小计，但可以总计                     | X-设置                              |
| NO_SUBTOTALS(1)          | 不能小计，但可以总计                         | X-设置                              |
| NO_UNIT_SPLITTING(1)     | 有单位字段，不进行总计                       | X-设置                              |
| TOTALS_BOFORE_ITEMS(1)   | 总计行将会显示在最前面                       | X-设置                              |
| TOTALS_ONLY(1)           | 仅显示合计                                   | X-设置                              |
| TOTALS_TEXT(60)          | 合计，第一列显示的文本                       |                                     |
| SUBTOTALS_TEXT(60)       | 总计和小计行，第一列显示的文本               |                                     |
| BOX_FIELDNAME            | 指定数据内表中哪列以选择按钮形式显示         | slis_fieldname                      |
| CONFIRMATION_PROMPT(1)   | 当退出ALV报表展示界面时，是否需要提示用户    | X-提示，space-不提示                |
| DETAIL_POPUP(1)          | 右键中有 Detail 菜单，是否弹出详细信息窗口   | X-弹出，space-不弹出                |
| INFO_FIELDNAME           | 设置ALV输出报表行颜色                        | 颜色字段                            |
| COLTAB_FIELDNAME         | 设置单元格颜色                               | 颜色表字段                          |

**INFO_FIELDNAME**：用于设置 ALV 输出报表每一行的颜色，其参数为输出内表的字段名称，要注意的是使用该属性需要同时在内表中定义一个与该参数所定义字段名相同的字段。

- LAYOUT-INFO_FIELDNAME = 'COLOR'. 倘若其数据输出内表名为LT_OUT，则需要在该内表增加一字段“COLOR”，并为其内表每行设置颜色值，颜色参数范围C000~C999。例如：LT_OUT-COLOR = 'C012'.

### LVC常用参数列表

| 字段名称          | 描述                                 | 输入值                                       |
| ----------------- | ------------------------------------ | -------------------------------------------- |
| **ZEBRA**(1)      | 使ALV表格按斑马线间隔条码方式显示    | X-设置，space-不设置                         |
| **EDIT**(1)       | 设置ALV为可编辑模式                  | X-可编辑，space-不可编辑                     |
| **CWIDTH_OPT**(1) | 自动优化列宽                         | X-优化，space-不自动优化                     |
| NO_KEYFIX(1)      | 关键字不固定，可以随滚动条滚动       | X-不固定，space-固定                         |
| NO_HGRIDLN(1)     | 是否隐藏水平网格线                   | X-不显示，space-显示                         |
| NO_VGRIDLN(1)     | 是否隐藏垂直网格线                   | X-不显示，space-显示                         |
| NO_HEADERS(1)     | 隐藏列抬头                           | X-不显示，space-显示                         |
| NO_MERGING(1)     | 禁用单元格合并                       | X-禁用，space-不禁用                         |
| NO_F4(1)          | 屏蔽F4搜索帮助                       | X-不显示，space-显示                         |
| STYLEFNAME        | 用来传输格表，以便把单元格显示为按钮 | LVC_FNAME                                    |
| **SEL_MODE**(1)   | 选择模式                             | A-B-单选，C多选不包含单元格，D多选包含单元格 |
| KEYHOT(1)         | 关键列作为热点                       | X-设置，space-不设置                         |
| NUMC_TOTAL(1)     | 禁止 NUMC 字段总计                   | X-禁止，space-不禁止                         |
| NO_TOTLINE(1)     | 不输出总计行                         | X-不输出，space-输出                         |
| NO_UTSPLIT(1)     | 按单元拆分总计行                     | X-不拆分，space-拆分                         |
| INFO_FNAME        | 简单行颜色代码的字段名称             | 内表对应颜色字段名                           |
| CTAB_FNAME        | 复杂单元格颜色编码的字段名称         | 内表对应颜色字段表                           |
| NO_ROWMARK        | 禁用行选择                           |                                              |
| NO_TOOLBAR        | 隐藏工具栏                           | X-隐藏，sapce-不隐藏                         |
| GRID_TITLE        | 标题栏文本                           |                                              |

### I_SAVE 保存布局选项字段的作用

| Value          | Descript                         |              |                           |
| -------------- | -------------------------------- | ------------ | ------------------------- |
| I_SAVE = SPACE | 无法保存 Layout                  | I_SAVE = 'U' | 只能保存用户定义的 Layout |
| I_SAVE = 'A'   | 用户定义和全局 Layout 都可以保存 | I_SAVE = 'X' | 只能保存全局 Layout       |

### [颜色设置](http://blog.sina.com.cn/s/blog_3f2c03e30100mk1s.html)

颜色设置中有优先级顺序，单元格--->行--->列。

#### 颜色编码

颜色值定义为 4 位字符型

- 第 1 位固定为字母 C (Color)

- 第 2 位为颜色编码，由 0~7 表示，不同的数字表示不同的颜色。

  | Value | Color            | Desc                     |
  | ----- | ---------------- | ------------------------ |
  | 0     | Background color | Default                  |
  | 1     | Gray-blue        | Headers                  |
  | 2     | Light gray       | List bodies              |
  | 3     | Yellow           | Totals                   |
  | 4     | Blue-gray        | Key columns              |
  | 5     | Green            | Positive threshold value |
  | 6     | Red              | Negative threshold value |
  | 7     | Orange           | Control levels           |

- 第 3 位表示输出文字是否高亮显示，由 0~1 表示。为 1 时表示高亮显示。

- 第 4 位表示颜色反转。测试了一下，基本上 0~9 颜色都差不多，唯一就是当取值为 1 时，底色又回到了灰色（且只是在第 3 位为 0 时才有此效果）。

#### 列颜色

可以通过字段目录的 emphasize 控制字段来控制某列的颜色，这个字段同样是 4 位的 CHAR 型，传入上述的颜色编码。

- `gs_fieldcat-emphasize = 'C510'.`

如果该列被设置为关键列，就是  LS_FCAT-KEY = 'X' ，那么颜色设置就不会起作用。

#### 行颜色

为某行设置颜色是有点复杂的，需要在 ALV 的数据内表中增加一个字段保存颜色值，这个字段不需要在字段目录中存在。而且该字段也要是 4 位的CHAR 型(rowcolor)，符合颜色编码的定义。

ALV 中的每行数据颜色是通过 Layout 的 `INFO_NAME`来控制的，可以设置这个字段来告诉 ALV 颜色字段是哪个。刷新以后才起作用。

- `layout-info_name = 'ROWCOLOR'.`

#### 单元格颜色

设置单元格和设置行的颜色本质上没有什么大的区别，但是定位单元格需要 2 个参数。我们需要在数据内表中插入一个表类型的字段，这样我们的数据内表就变成了 DEEP 结构了。

表类型字段的类型为 `LVC_T_SCOL`。

- FNAME：指定需要设置的是哪个字段，如果为空则会设置整行
- COLOR：用来设置颜色值
- NOKEYCOL：设置为关键列的一些字段，我们的颜色设置可能被覆盖。通过这个字段的设置，可以避免被关键列覆盖

同样，ALV 在布局中有个字段 `CTAB_FNAME` 告诉我们，数据内表中，哪个字段是用来设置单元格的颜色的。

- `layout-ctab_name = 'COLORTABLE'.`

### ALV 可编辑

**整体可编辑**：layout-edit = 'X' 

**某列可编辑**：gs_fieldcat-edit = 'X'

**单元格可编辑**：需要通过 OO 方式实现

## 设置工具导航栏 GUI Status

#### Copy SAP 标准 GUI Status

- SE80 -> SALV -> STANDARD -> Copy到自定义程序的GUI Status

![GUI](/images/ABAP/CopyGUI.png)

GUI Status 参数设置共包括3个部分：

1. 菜单栏(Menu Bar)：用于设置主菜单选项。

2. 应用工具条(Application ToolBar)：用于设置应用工具栏按钮，包括按钮名称、按钮描述、及按钮所对的ICON图标。

3. 功能键(Function Key)：为按钮分配功能键代码，包括系统标题按钮(如返回、退出、关闭等)及通过Application ToolBar所定义的客制化按钮。

####  在ALV函数中使用 GUI Status

```ABAP
call function 'REUSE_ALV_GRID_DISPLAY_LVC'
  exporting
    i_callback_program       = sy-repid 
    i_callback_pf_status_set = 'PF_STATUS_SET'
    i_callback_user_command  = 'USER_COMMAND'
    is_layout_lvc            = layout
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

#### 自定义状态栏

```ABAP
FORM pf_status_set using extab TYPE slis_t_extab.
  data: ls_slis_extab type slis_extab.
  "排除按钮不显示"
  ls_slis_extab-fcode = '&ABC'.
  APPEND ls_slis_extab TO extab.
  ......
  SET TITLEBAR 'TITLE' WITH text-t01.          "输入标题栏名称"
  SET PF-STATUS <STATUS_NAME> EXCLUDING extab. "输入自定义按钮工具的名称,并排除指定按钮"
ENDFORM.                    "pf_status_set"
```

#### 按钮处理 

```ABAP
Form user_command using p_ucomm type sy-ucomm
                     i_selfield type slis_selfield.
  data:ls_stable type lvc_s_stbl.
  data:lr_grid type ref to cl_gui_alv_grid.
  call function 'GET_GLOBALS_FROM_SLVC_FULLSCR'
    importing
      e_grid = lr_grid.
  call method lr_grid->check_changed_data.
 CALL METHOD LR_GRID->REFRESH_TABLE_DISPLAY.
  i_selfield-refresh = 'X'.  "自动刷新"
  case p_ucomm.
    when 'ZPRT'.
      read table gt_disp into gs_disp with key check_box = 'X'.
      if sy-subrc = 0.
        perform frm_print_data.
      else.
        message e398(00) display like 'E'.
        return.
      endif.
    when '&IC1'. "对操作代码进行相应处理"
	  IF i_selfield-fieldname EQ 'FieldName' AND i_selfield-value NE space.
      	DATA: ls_upload LIKE LINE OF gt_upload.
 		READ TABLE gt_upload INTO ls_upload INDEX rs_selfield-tabindex.
  		CHECK sy-subrc EQ 0 AND ls_upload-aufnr NE space.
        "传入输入参数值并调用其他TCode"
 	    SET PARAMETER ID 'ANR' FIELD ls_upload-aufnr.
  	    CALL TRANSACTION 'CO03' AND SKIP FIRST SCREEN. "跳过第一屏屏幕"
      ENDIF.
  endcase.
 call method lr_grid->refresh_table_display.
ENDFORM.                    "user_command"
```

#### 自定义按钮

对于定义的按钮，我们可以通过系统变量SY-UCOMM来获取它的功能代码。

```ABAP
AT USER-COMMAND.  "当单击某个按钮时，触发该事件"
  CASE sy-ucomm.  "获取所操作按钮的功能代码(FUNCTION Code)"
```
-   调用显示，应用于START-OF-SELECTION事件`SET PF-STATUS <STATUS_NAME>.`  
    

​	不显示某些按钮：`SET PF-STATUS <STATUS_NAME> EXCLUDING <extab>.`

### 文本增强

- 事物码：CMOD -> 转到 -> 文本增强 -> 关键字 -> 更改

