---
title: " SAP 屏幕搜索帮助 "
date: 2019-10-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 搜索帮助优先级

- 先检查 `PROCESS ON VALUE-REQUEST` 、`AT SELECTION-SCREEN ON VALUE-REQUEST`

- 再检查 `PARAMETERS VALUE CHECK` / `SELECT-OPTIONS MATCHCODE OBJECT XXXX`

- 其次是`Check Table`、然后检查表（或结构 ）字段是否绑定了搜索帮助

- 然后检查 Data element 是否绑定了搜索帮助 ，再检查 Domain 是否存在 Fixed Values

- 最后才是 SAP 中的 DATS、TIMS 搜索帮助

### 参照数据库字段

参照数据库字段，用 SAP 数据字典自带的检索帮助，或者参照字段的定义域实现F4检索帮助。是最简单的方法，参照字段定义即可。

- `PARAMETERS p_matnr TYPE mara-matnr.` 

### VALUE CHECK

- `PARAMETER p_matnr TYPE mara-matnr VALUE CHECK.`

如果选择屏幕字段参考数据元素所对应的 Domain 设置了 **固定值（ Fixed Values ）**或 **值表（ Value Table ）**时，使用 VALUE CHECK 选项后，会验证输入值是否在固定值或值表范围之内。

- 若要使值表检查生效，则首先需要将此 Domain 引用到表字段，再对此表字段通过 Foreign Keys 按钮进行外键分配，并且外键一定是来自值表的主键，最后使用 PARAMETERS 定义屏幕参数时要参照此表字段，否则如果只是直接参照所对应的 Data Element 是不起作用， **即 Value Table 一定要经过转换为 Check Table 后再起作用**。

- 如果要使用 VALUE CHECK 选项，则 Domain 的类型只能是 **C** 或者 **N** 类型 ， 否则运行会抛异常。另外， **如果未使用该选项，但** **F4 Help** **还是会出现** （有固定值或检查表的情况下），但不进行有效性检查。

### 搜索帮助对象

- `PARAMETERS p_bwart(4) MATCHCODE OBJECT zsh_bwart.`

当某个表字段有检查表，并且又有搜索帮助，则数据一般来自源于检查表，而 **F4 的输入输出则由搜索帮助来决定**。 可以通过 SE11 里面创建一个检索帮助ID（search_help），然后在屏幕定义的时候，使用`MATCHCODE OBJECT search_help` 绑定即可。

#### 定义 Search Help

在命令字段中输入事务代码 SE11；选择搜索帮助单选按钮，然后输入自定义搜索帮助的名称。然后点击创建按钮。 

1、在下一个屏幕上，选择 “基本搜索帮助” 选项。

![Search Help](/images/ABAP/SearchHelp1.png)

2、输入搜索帮助的描述。在选择方法字段中，应输入数据库表名称（我们将从该表中选择信息类型编号字段的值）。

![Search Help](/images/ABAP/SearchHelp.png)

3、选择对话框类型：Display values immediately

4、下面的 “参数” 结构中，输入选择条件所需的字段并显示在值帮助上。参数的各种选项包括：

| 属性                 | 描述                                                         |
| -------------------- | ------------------------------------------------------------ |
| **Search help para** | 来自数据源的字段，选择条件所需的字段                         |
| **IMP**              | 输入参数，即将选择屏幕上的字段值传递给搜索帮助               |
| **EXP**              | 输出参数，即将从搜索帮助中传递到报表屏幕上                   |
| **LPOS**             | 控制选择列表中搜索帮助参数或字段出现在匹配列表中的位置，如果为 0 或留空的列则不会显示 |
| **SPOS**             | 控制限制性对话框中的搜索帮助参数或字段显示的位置             |
| **SDIS**             | 使该字段在选择屏幕中 “仅显示”                                |
| **Data element**     | 设置搜索帮助参数的属性。通常由系统填写                       |
| **MOD**              | 选中此框可以分配与系统提供的数据元素不同的数据元素           |
| **Default**          | 以以下三种方式之一指定默认值："文字"(带引号)，参数ID（ZRD）或系统字段(SY-UNAME) |

6、搜索帮助准备就绪后，将其选中并激活。

#### 使用搜索帮助对象

```ABAP
SELECTION-SCREEN BEGIN OF LINE.
SELECTION-SCREEN COMMENT 2(15) text1 .
PARAMETERS p_bwart(4) MATCHCODE OBJECT zsh_bwart.
SELECTION-SCREEN END OF LINE.
INITIALIZATION.
  text1 = 'Reason Code'.
```

![SE11 Serch help](/images/ABAP/SearchHelp2.png)

#### 搜索帮助出口 

`Z_REASON_CODE`

```ABAP
FUNCTION Z_REASON_CODE.
*----------------------------------------------------------------------
*Local Interface:
*  TABLES
*      SHLP_TAB TYPE  SHLP_DESCT
*      RECORD_TAB STRUCTURE  SEAHLPRES
*  CHANGING
*     VALUE(SHLP) TYPE  SHLP_DESCR
*     VALUE(CALLCONTROL) LIKE  DDSHF4CTRL STRUCTURE  DDSHF4CTRL
*----------------------------------------------------------------------
DATA: it_ddshselopt TYPE TABLE OF ddshselopt WITH HEADER LINE,
      it_ddshfprops TYPE TABLE OF ddshfprop WITH HEADER LINE,
      BEGIN OF it_shlpfld OCCURS 0,
        parameter TYPE shlpfield,
        fieldname TYPE fnam_____4,
        END OF it_shlpfld,
      BEGIN OF rang_template OCCURS 0,
        sign   TYPE tvarv_sign,
        option TYPE tvarv_opti,
        low    TYPE rsdsselop_,
        high   TYPE rsdsselop_,
        END OF rang_template,
      " 用于存储从选择屏幕上传进的屏幕字段的选择条件值 "
      ranges_bwart   LIKE TABLE OF rang_template WITH HEADER LINE,
      ranges_grund   LIKE TABLE OF rang_template WITH HEADER LINE,
      ranges_grtxt   LIKE TABLE OF rang_template WITH HEADER LINE.
" 此内表用于存储命中清单数据. 注：字段的名称一定要与搜索参数名一样，但顺序可以不同 "
DATA:BEGIN OF l_it_t157d OCCURS 0,
       bwart TYPE bwart,
       grund TYPE mb_grbew,
       grtxt TYPE grtxt,
      END OF l_it_t157d ,
      l_it_tmp LIKE TABLE OF l_it_t157d WITH HEADER LINE.
  FIELD-SYMBOLS <wa> like  l_it_t157d.

  it_ddshfprops[] = shlp-fieldprop.
  LOOP AT it_ddshfprops WHERE shlplispos <> 0.
    it_shlpfld-parameter = it_ddshfprops-fieldname.
    it_shlpfld-fieldname = it_ddshfprops-fieldname.
    APPEND it_shlpfld.
  ENDLOOP.
  "DISP：在命中清单显示之前调用,表示数据已经查出,下一步就该显示了。"
  CHECK callcontrol-step = 'DISP'. 
  "shlp-selopt存储的是经过映射转换后选择屏幕上字段的值，而不是直接为选择屏幕字段名，而是转映射为Help参数"
  " 名后再存储到 selopt 内表中，屏幕字段到 Help 参数映射是通过 shlp-interface 来映射的。"
  it_ddshselopt[] = shlp-selopt.
  LOOP AT it_ddshselopt WHERE shlpfield = 'BWART'.
    MOVE-CORRESPONDING it_ddshselopt TO ranges_bwart.
    APPEND ranges_bwart.
  ENDLOOP.
  LOOP AT it_ddshselopt WHERE shlpfield = 'GRUND'.
    MOVE-CORRESPONDING it_ddshselopt TO ranges_grund.
    APPEND ranges_grund.
  ENDLOOP.
  LOOP AT it_ddshselopt WHERE shlpfield = 'GRTXT2'.
    MOVE-CORRESPONDING it_ddshselopt TO ranges_grtxt.
    APPEND ranges_grtxt.
  ENDLOOP.
  " 根据屏幕上传进的条件查询数据 "
  SELECT bwart grund  INTO CORRESPONDING FIELDS OF TABLE l_it_t157d
    FROM t157d
   WHERE bwart IN ranges_bwart
     AND grund IN ranges_grund
     AND bwart = 'Z21'.
  SELECT bwart grund grtxt INTO CORRESPONDING FIELDS OF TABLE l_it_tmp
    FROM t157e
    WHERE spras = 'E'
      AND bwart IN ranges_bwart
      AND grund IN ranges_grund
      AND grtxt IN ranges_grtxt
      AND bwart = 'Z21'.
  SORT l_it_tmp BY bwart grund.

  LOOP AT l_it_t157d ASSIGNING <wa>.
    CLEAR l_it_tmp.
    READ TABLE l_it_tmp WITH KEY bwart = <wa>-bwart grund = <wa>-grund BINARY SEARCH.
    <wa>-grtxt = l_it_tmp-grtxt.
  ENDLOOP.

  callcontrol-maxexceed = ''.
  " 该函数的作用是将内表 lt_tab 中的数据转换成 record_tab ，即将某内表中的数据显示在命中清单中 "
  LOOP AT it_shlpfld.
    CALL FUNCTION 'F4UT_PARAMETER_RESULTS_PUT'
      EXPORTING
        parameter   = it_shlpfld-parameter
        fieldname   = it_shlpfld-fieldname
      TABLES
        shlp_tab    = shlp_tab   " Reference to field of Seatinfo "
        record_tab  = record_tab " 结果集 "
        source_tab  = l_it_t157d
      CHANGING
        shlp        = shlp
        callcontrol = callcontrol.
  ENDLOOP.
ENDFUNCTION.
```

### 搜索帮助创建函数

在屏幕的 ON VALUE-REQUEST 事件中可以通过以下几个函数来创建搜索帮助：

- F4IF_FIELD_VALUE_REQUEST：在程序运行时，可以动态的为屏幕上某个字段指定 Search Help。被引用的搜索帮助来自于某个表（结构）字段上绑定的 Search Help
- F4IF_INT_TABLE_VALUE_REQUEST：在程序运行时，将某个内表动态的用作 Search Help 的数据，可实现联动效果

#### FM：F4IF_FIELD_VALUE_REQUEST

运行这个函数就会弹出 F4 帮助界面的值选择窗口，窗口中的值就是 tabname 中字段 fieldname 的所有可选值，当选择某个值后，那么这个值和其相关的属性就会存放到表 return_tab 中。

```ABAP
CALL FUNCTION 'F4IF_FIELD_VALUE_REQUEST'
  EXPORTING
    tabname    = gs_selfields-tabname      "数据字典中的表名"
    fieldname  = gt_Selfields-fieldname    "数据字典中的字段名"
    * value    = selval
  TABLES
    return_tab = return_tab
  EXCEPTIONS
    FIELD_NOT_FOUND    = 1
    NO_HELP_FOR_FIELD  = 2
    INCONSISTENT_HELP  = 3
    NO_VALUES_FOUND    = 4
    OTHERS             = 5.
```

#### FM：F4IF_INT_TABLE_VALUE_REQUEST

在程序运行时，将某个内表动态的用作 **Search help**  的数据来源 ，即使用该函数可以将某个内表转换为 Search help ，可实现联动效果。

单值选择

```ABAP
SELECT-OPTIONS: s_insmk FOR mseg-insmk NO INTERVALS.
AT SELECTION-SCREEN ON VALUE-REQUEST FOR s_insmk-low.
  PERFORM frm_request_f4_insmk USING 'S_INSMK-LOW'.
*&---------------------------------------------------------------------*
*&      Form  FRM_REQUEST_F4_INSMK
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
FORM frm_request_f4_insmk USING pv_field TYPE help_info-dynprofld.
  TYPES: BEGIN OF ty_value_tab,
          insmk  TYPE mseg-insmk,
          insmkt TYPE dd07t-ddtext,
        END OF ty_value_tab.
  DATA: lt_value_tab TYPE TABLE OF ty_value_tab WITH HEADER LINE. 

  DEFINE add_value.
    CLEAR lt_value_tab.
    lt_value_tab-INSMK  = &1.
    lt_value_tab-INSMKT = &2.
    APPEND lt_value_tab.
  END-OF-DEFINITION.
 
  add_value 'U'  text-r03. " Unrestricted
  add_value 'B'  text-r04. " Blocked
  add_value 'Q'  text-r05. " Quality inspection
  add_value 'T'  text-r06. " Transfer stock between storage location
  add_value 'P'  text-r07. " Transfer stock between plant
 
  CALL FUNCTION 'F4IF_INT_TABLE_VALUE_REQUEST'
    EXPORTING
      retfield        = 'INSMK'
      dynpprog        = sy-repid
      dynpnr          = sy-dynnr
      dynprofield     = pv_field
      value_org       = 'S'
    TABLES
      value_tab       = lt_value_tab
    EXCEPTIONS
      parameter_error = 1
      no_values_found = 2
      OTHERS          = 3.
  IF sy-subrc <> 0.
*   Implement suitable error handling here
  ENDIF.
ENDFORM.
```

多个值选择，显示列名设置

```ABAP
PARAMETERS: p_bname LIKE usr02-bname,
            p_class LIKE usr02-class.

AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_bname.
	PERFORM frm_valuereq_bname.
	
FORM frm_valuereq_bname.
  " 需要显示的结果集 "
  DATA: BEGIN OF t_data OCCURS 1,
      data(20),
  END OF t_data.
  " 字段 "
  DATA: lv_retfield TYPE dfies-fieldname.
  DATA: h_field_wa LIKE dfies,
        h_field_tab LIKE dfies OCCURS 0 WITH HEADER LINE,
        h_dselc LIKE dselc OCCURS 0 WITH HEADER LINE.
  SELECT * FROM usr02.
    t_data-data = usr02-bname. APPEND t_data. CLEAR t_data.
    t_data-data = usr02-class. APPEND t_data. CLEAR t_data.
  ENDSELECT.
  "搜索帮助显示列表ALV的列名显示成用户指定的名称"
  PERFORM f_fieldinfo_get USING 'USR02' 'BNAME' CHANGING h_field_wa.
  APPEND h_field_wa TO h_field_tab.
  PERFORM f_fieldinfo_get USING 'USR02' 'CLASS' CHANGING h_field_wa.
  APPEND h_field_wa TO h_field_tab.

  h_dselc-fldname = 'BNAME'.
  h_dselc-dyfldname = 'P_BNAME'.
  APPEND h_dselc.
  h_dselc-fldname = 'CLASS'.
  h_dselc-dyfldname = 'P_CLASS'.
  APPEND h_dselc.
  "将获取到的值绑定到对应屏幕字段"
  lv_retfield = 'BNAME'.
  CALL FUNCTION 'F4IF_INT_TABLE_VALUE_REQUEST'
    EXPORTING
      retfield         = lv_retfield  "可选值表的字段名"
      dynpprog         = sy-repid     "输入框所在主程序"
      dynpnr           = sy-dynnr     "输入框所在屏幕"
      dynprofield      = 'P_BNAME'    "选择屏幕上需要添加F4帮助的字段"
*     multiple_choice  = 'X'          "多项选择，屏幕字段可输入多值时使用"
      value_org        = 'S'          "C:CELL逐行，S:STRUCTURE结构化(在DILOG中必选S)"
*     display          = 'C'          "C:只能显示不能选择"
    TABLES
      value_tab        = t_data       "F4 帮助可选值表"
      field_tab        = h_field_tab  "自定义ALV显示列名"
*    return_tab       = return_tab
      dynprld_mapping  = h_dselc
    EXCEPTIONS
      OTHERS = 0.
ENDFORM. 

FORM f_fieldinfo_get USING fu_tabname fu_fieldname
                     CHANGING fwa_field_tab.
  CALL FUNCTION 'DDIF_FIELDINFO_GET'
    EXPORTING
      TABNAME    = fu_tabname
      FIELDNAME  = fu_fieldname
      LANGU      = sy-langu
      LFIELDNAME = fu_fieldname
    IMPORTING
      DFIES_WA   = fwa_field_tab
    EXCEPTIONS
      NOT_FOUND  = 1
      INTERNAL_ERROR = 2
      OTHERS = 3.
  IF SY-SUBRC <> 0.
    MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
    WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
  ENDIF.
ENDFORM.
```
