---
title: " SAP 搜索帮助 "
date: 2019-10-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  ABAP

tags: 
  - abaputils

---

### 使用搜索帮助对象

#### 定义 Search Help

在命令字段中输入事务代码 SE11；选择搜索帮助单选按钮，然后输入自定义搜索帮助的名称。然后点击创建按钮。 

1、在下一个屏幕上，选择 “基本搜索帮助” 选项。

![Search Help](/images/ABAP/SearchHelp1.png)

2、输入搜索帮助的描述。在选择方法字段中，应输入数据库表名称。该表是我们将从中选择信息类型编号字段的值的表。表 T157D 可用于此目的。

![Search Help](/images/ABAP/SearchHelp.png)

3、选择对话框类型：Display values immediately

4、下面的 “参数” 结构中，输入选择条件所需的字段并显示在值帮助上。参数的各种选项包括：

| 属性                 | 描述                                                         |
| -------------------- | ------------------------------------------------------------ |
| **Search help para** | 来自数据源的字段，选择条件所需的字段                         |
| **IMP**              | 选中此框以指示该字段是输入字段，即将传递给搜索帮助           |
| **EXP**              | 选中此框以指示该字段是输出字段，即将从搜索帮助传递到屏幕     |
| **LPOS**             | 控制选择列表中搜索帮助参数或字段出现在匹配列表中的位置       |
| **SPOS**             | 控制限制性对话框中的搜索帮助参数或字段显示的位置             |
| **SDIS**             | 使该字段在选择屏幕中 “仅显示”                                |
| **Data element**     | 设置搜索帮助参数的属性。通常由 系统填写                      |
| **MOD**              | 选中此框可以分配与系统提供的数据元素不同的数据元素           |
| **Default**          | 以以下三种方式之一指定默认值："文字"(带引号)，参数ID（ZRD）或系统字段(SY-UNAME) |

6、搜索帮助准备就绪后，将其选中并激活。

#### 示例代码：Z_MIN_Z21

```JS
SELECTION-SCREEN BEGIN OF LINE.
SELECTION-SCREEN COMMENT 2(15) text1 .
PARAMETERS: p_bwart(4) MATCHCODE OBJECT z_min_z21.
SELECTION-SCREEN END OF LINE.
INITIALIZATION.
  text1 = 'Reason Code'.
```

![SE11 Serch help](/images/ABAP/SearchHelp2.png)

#### 搜索帮助出口 

`Z_REASON_CODE_MIN_Z21`

```ABAP
FUNCTION Z_REASON_CODE_MIN_Z21.
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

### FM：F4IF_INT_TABLE_VALUE_REQUEST

在程序运行时，将某个内表动态的用作 **Search help**  的数据来源 ，即使用该函数可以将某个内表转换为 Search help ，可实现联动效果。

```ABAP
parameters: p_bname LIKE usr02-bname,
            p_class LIKE usr02-class.
AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_bname.
	PERFORM frm_valuereq_bwart.
FORM frm_valuereq_bwart.
" 需要显示的结果集 "
DATA: BEGIN OF t_data OCCURS 1,
    data(20),
END OF t_data.
" 字段 "
DATA: lwa_dfies TYPE dfies,
      h_field_wa LIKE dfies,
      h_field_tab LIKE dfies occurs 0 with header line,
      h_dselc LIKE dselc occurs 0 with header line.
SELECT * FROM usr02.
  t_data = usr02-bname. APPEND t_data.
  t_data = usr02-class. APPEND t_data.
ENDSELECT.
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
CALL FUNCTION 'F4IF_INT_TABLE_VALUE_REQUEST'
  EXPORTING
    retfield = 'P_BWART' "搜索帮助表返回到选择屏幕的字段的参数"
    dynpprog = sy-repid
    dynpnr   = sy-dynnr
    dynprofield = 'P_BWART' "选择屏幕上需要添加F4帮助的字段"
  * multiple_choice = ''
    value_org = 'S'      "默认：C"
  TABLES
    value_tab = t_data   "F4 帮助值的表"
    field_tab = h_field_tab
  * return_tab = return_tab
    DYNPFLD_MAPPING = h_dselc
  EXCEPTIONS
    OTHERS = 0.
ENDFORM. 

FORM f_fieldinfo_get USING fu_tabname fu_fieldname
                     CHANGING fwa_field_tab.
  CALL FUNCTION 'DDIF_FIELDINFO_GET'
    EXPORTING
      TABNAME = fu_tabname
      FIELDNAME = fu_fieldname
      LANGU     = sy-langu
      LFIELDNAME = fu_fieldname
    IMPORTING
      DFIES_WA = fwa_field_tab
    EXCEPTIONS
      NOT_FOUND = 1
      INTERNAL_ERROR = 2
      OTHERS = 3.
  IF SY-SUBRC <> 0.
    MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
    WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
  ENDIF.
ENDFORM.
```

### 搜索帮助优先级

- 先 PROCESS ON VALUE-REQUEST 、AT SELECTION-SCREEN ON VALUE-REQUEST

- 再 PARAMETERS/ SELECT-OPTIONS MATCHCODE OBJECT XXXX

- 先  Check Table、再表（或 结构 ）字段是否绑定了 **搜索帮助**

- 先 Data element 是否绑定了帮助 ，再 Domain 是否存在 Fixed values

- 最后才是DATS、TIMS.