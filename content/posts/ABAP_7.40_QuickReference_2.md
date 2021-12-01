---
title: " ABAP 7.40 Quick Reference Part2 "
date: 2020-07-17
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbasis

---

### Conditional operators - COND & SWITCH

根据逻辑表达式或情况的不同确定指定变量的结果。THEN 后面都是返回的结果。

#### Definition

```ABAP
"COND"
COND dtype|#( WHEN log_exp1 THEN result1
  [ WHEN log_exp2 THEN result2 ]
  ...
  [ ELSE resultn ] ) .
"SWITCH"
SWITCH dtype|#( operand WHEN const1 THEN result1
  [ WHEN const2 THEN result2 ]
  ...
  [ ELSE resultn ] ) .
```

#### Example for COND

```ABAP
DATA(time) = COND string(
    WHEN sy-timlo < '120000' THEN |{ sy-timlo TIME = ISO } AM|
    WHEN sy-timlo > '120000' THEN
      |{ CONV t( sy-timlo - 12 * 3600 ) TIME = ISO } PM|
    WHEN sy-timlo = '120000' THEN |High Noon|
    ELSE
      THROW cx_cant_be( ) 
).
```

#### Example for SWITCH

```ABAP
DATA(text) = SWITCH #( sy-langu
    WHEN 'D' THEN 'DE'
    WHEN 'E' THEN 'EN'
    ELSE 
      THROW cx_langu_not_supported( ) 
).
```

### Strings

#### String Templates

字符串模板由两个字符"|"括起来并创建一个字符串。 

文字文本由不在大括号"{ }"中的所有字符组成。

大括号可以包含：

- 数据对象
- 计算表达式
- 构造函数表达式
- 表表达式
- 预定义功能，或 功能方法和方法链

```ABAP
*Before 7.40
DATA: itab TYPE TABLE OF scarr,
      wa LIKE LINE OF itab.
DATA output TYPE string.
SELECT * FROM scarr INTO TABLE itab.
READ TABLE itab WITH KEY carrid = 'LH' INTO wa.
IF sy-subrc = 0.
  CONCATENATE ‘Carrier:’ wa-carrname INTO output SEPARATED BY space.
ENDIF.
cl_demo_output=>display( output ).
*With 7.40
SELECT * FROM scarr INTO TABLE @DATA(lt_scarr).
cl_demo_output=>display( |Carrier: { lt_scarr[ carrid = ‘LH’ ]–carrname }| ).
```

#### Conacatenation

```ABAP
*Before 7.40
DATA lv_output TYPE string.
CONCATENATE ‘Hello’ ‘world’ INTO lv_output SEPARATED BY space.
*With 7.40
DATA(lv_out) = |Hello| & | | & |world|.
```

#### Width/Alignment/Padding

```ABAP
*With 7.40
WRITE / |{ 'Left'   WIDTH = 20 ALIGN = LEFT   PAD = '0' }|.
WRITE / |{ 'Center' WIDTH = 20 ALIGN = CENTER PAD = '0' }|.
WRITE / |{ 'Right'  WIDTH = 20 ALIGN = RIGHT  PAD = '0' }|.
```

#### Case

```ABAP
*With 7.40
WRITE / |{ 'Text' CASE = (cl_abap_format=>c_raw) }|.
WRITE / |{ 'Text' CASE = (cl_abap_format=>c_upper) }|.
WRITE / |{ 'Text' CASE = (cl_abap_format=>c_lower) }|.
```

#### ALPHA conversion

```ABAP
*With 7.40
DATA(lv_vbeln) = ‘0000012345’.
WRITE / |{ lv_vbeln  ALPHA = OUT }|. 
WRITE / |{ lv_vbeln  ALPHA = IN }|.
```

#### Date conversion

```ABAP
*With 7.40
DATA(pa_date) = sy-datum.
WRITE / |{ pa_date DATE = ISO }|.         "Date Format YYYY-MM-DD"
WRITE / |{ pa_date DATE = User }|.        "As per user settings"
WRITE / |{ pa_date DATE = Environment }|. "Formatting setting of language environment"
*environment对应的值是CL_ABAP_FORMAT=>D_ENVIRONMENT
```

### Conversion Operator - CONV

#### Definition

CONV dtype|# ( ... )

#### Example

方法 cl_abap_codepage=>convert_to 需要一个字符串。

```ABAP
*Before 7.40
DATA text   TYPE c LENGTH 255.
DATA helper TYPE string.
DATA xstr   TYPE xstring.
helper = text.
xstr = cl_abap_codepage=>convert_to( source = helper ).
*With 7.40
DATA text TYPE c LENGTH 255.
DATA(xstr) = cl_abap_codepage=>convert_to( source = CONV string( text ) ).
"或则使用"
DATA(xstr) = cl_abap_codepage=>convert_to( source = CONV #( text ) ).
```

### Loop at group by

用于内表的分类处理，相当于 SQL 语句的 group by.

#### Definition

```ABAP
LOOP AT itab result [cond] GROUP BY key ( key1 = dobj1 key2 = dobj2 ...
    [gs = GROUP SIZE] [gi = GROUP INDEX] )
    [ASCENDING|DESCENDING [AS TEXT]]
    [WITHOUT MEMBERS]
    [{INTO group}|{ASSIGNING <group>}]
      ...
    [LOOP AT GROUP group|<group>
      ...
    ENDLOOP.]
      ...
ENDLOOP.
```

外循环将对每个键执行一次迭代。 因此，如果 3 个记录与键匹配，则这 3 个记录将只有一个迭代。 结构“ group”不寻常，因为可以使用“LOOP AT GROUP”语句进行循环。 这将遍历该组的 3 个记录（成员）。 结构 “group” 还包含当前键以及组的大小和组的索引（如果已为 GROUP SIZE 和 GROUP INDEX 分配了字段名）。 通过示例可以最好地理解这一点。

#### Example

```ABAP
TYPES: BEGIN OF ty_employee,
  name TYPE char30,
  role TYPE char30,
  age  TYPE i,
END OF ty_employee,
ty_employee_t TYPE STANDARD TABLE OF ty_employee WITH KEY name.
DATA(gt_employee) = VALUE ty_employee_t(
( name = ‘John‘   role = ‘ABAP guru‘      age = 34 )
( name = ‘Alice‘  role = ‘FI Consultant‘  age = 42 )
( name = ‘Barry‘  role = ‘ABAP guru‘      age = 54 )
( name = ‘Mary‘   role = ‘FI Consultant‘  age = 37 )
( name = ‘Arthur‘ role = ‘ABAP guru‘      age = 34 )
( name = ‘Mandy‘  role = ‘SD Consultant‘  age = 64 ) ).
DATA: gv_tot_age TYPE i,gv_avg_age TYPE decfloat34.
“Loop with grouping on Role
LOOP AT gt_employee INTO DATA(ls_employee)
  GROUP BY ( role  = ls_employee-role
             size  = GROUP SIZE
             index = GROUP INDEX )
  ASCENDING ASSIGNING FIELD-SYMBOL(<group>).
  CLEAR: gv_tot_age.
  “Output info at group level
  WRITE: / |Group: { <group>-index } Role: { <group>-role WIDTH = 15 }|
              & |Number in this role: { <group>-size }|.
   “Loop at members of the group
   LOOP AT GROUP <group> ASSIGNING FIELD-SYMBOL(<ls_member>).
      gv_tot_age = gv_tot_age + <ls_member>-age.
      WRITE: /13 <ls_member>-name.
   ENDLOOP.
   “Average age
   gv_avg_age = gv_tot_age / <group>-size.
   WRITE: / |Average age: { gv_avg_age }|.
   SKIP.
ENDLOOP.
```

Output

```htnl
Group: 1 Role: ABAP guru  Number in this role: 3
         John
         Barry
         Arthur
Average age: 40.66666666666666666666666666666667
Group: 2 Role: FI Consultant Number in this role: 2
         Alice
         Mary
Average age: 39.5
Group: 3 Role: SD Consultant Number in this role: 1
         Mandy
Average age: 64
```

### Classes/Methods

#### Referencing fields within returned structures

```ABAP
*Before 7.40
DATA: ls_lfa1  TYPE lfa1,
      lv_name1 TYPE lfa1-name1.
ls_lfa1  = my_class=>get_lfa1( ).
lv_name1 = ls_lfa1-name1.
*With 7.40
DATA(lv_name1) = my_class=>get_lfa1( )-name1.
```

#### Methods that return a type BOOLEAN

类型 BOOLEAN 不是真正的布尔值，而是具有允许值 X、- 、blank 的 char1。使用 FLAG 或 WDY_BOOLEAN 类型也可以。

```ABAP
*Before 7.40
IF my_class=>return_boolean( ) = abap_true.
  ...
ENDIF.
*With 7.40
IF my_class=>return_boolean( ).
  ...
ENDIF.
```

#### New Operator

NEW 可以创建匿名的数据对象或者类的实例。

#### Definition

`... NEW dtype( value ) ...`：创建一个类型为 dtype 的匿名数据对象，然后传值给创建的对象。

`... NEW class( p1 = a1 p2 = a2 ... ) ...`：创建一个名为 class 类的实例，并且传参到实例的构造函数。

`... NEW #( ... ) ...`：根据操作数类型创建一个匿名数据对象或者一个类的实例。

#### Example

```ABAP
*Before 7.40
DATA: lo_delivs TYPE REF TO zcl_sd_delivs,
      lo_deliv  TYPE REF TO zcl_sd_deliv.
CREATE OBJECT lo_delivs.
CREATE OBJECT lo_deliv.
DATA: lv_vbeln TYPE vbeln VALUE '00123488'.
lo_deliv = lo_delivs->get_deliv( lv_vbeln ).
*With 7.40
DATA(lo_deliv) = new zcl_sd_delivs->get_deliv( lv_vbeln ).
```

