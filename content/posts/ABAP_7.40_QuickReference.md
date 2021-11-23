---
title: " ABAP 7.40 Quick Reference "
date: 2020-07-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbasis

---

### Inline Declarations

注意@DATA(ITAB)语句的强大，有了它，我们在访问数据库的时候，直接写SELECT就好了，不需要构建各式各样的內表和表类型了。注意在使用FOR ALL ENTRIES 语句的时候，管理的內表前面要加上@。

#### Data statement

```html
// Before 7.40
DATA text TYPE string. 
text = ABC.
// With 7.40
DATA(text) = ABC.
```

#### Loop at into work area

```html
// Before 7.40
DATA wa like LINE OF itab. 
LOOP AT itab INTO wa.     
  ...... 
ENDLOOP.
// With 7.40
LOOP AT itab INTO DATA(wa).
  ......
ENDLOOP.
```

#### Call method

```html
// Before 7.40
DATA a1 TYPE char10.
DATA a2 TYPE char10.
oref->meth( IMPORTING p1 = a1
            IMPORTING p2 = a2 ).
// With 7.40
oref->meth(  IMPORTING p1 = DATA(a1)
             IMPORTING p2 = DATA(a2) ). 
```

#### Loop at assigning

```html
// Before 7.40
FIELD-SYMBOLS: <line> type any.
LOOP AT itab ASSIGNING <line>.
  ...... 
ENDLOOP.
// With 7.40
LOOP AT itab ASSIGNING FIELD-SYMBOL(<line>).   
  .......
ENDLOOP.
```

#### Read assigning

```html
// Before 7.40
FIELD-SYMBOLS: <line> type any.
READ TABLE itab ASSIGNING <line>.
// With 7.40
READ TABLE itab ASSIGNING FIELD-SYMBOL(<line>).
```

#### Select into table

```html
// Before 7.40
DATA itab TYPE TABLE OF dbtab.
DATA lv_fld1 TYPE char20.
SELECT * FROM dbtab INTO TABLE itab WHERE fld1 = lv_fld1.
// With 7.40
SELECT but000~partner,
       but000~name_org1,
       but000~bu_group,
       lfa1~nodel
  FROM but000 INNER JOIN lfa1 ON but000~partner = lfa1~lifnr
  FOR ALL ENTRIES IN @gt_partner
  WHERE but000~partner = @gt_partner-partner
  INTO TABLE @DATA(lt_but).
```

#### Select single 

```html
// Before 7.40
SELECT SINGLE f1 f2 FROM scarr INTO (lv_f1, lv_f2) WHERE carrid = id.
// With 7.40
SELECT SINGLE f1 AS my_f1, F2 AS my_f2 
FROM dbtab INTO DATA(ls_structure) WHERE carrid = @id.
WRITE: / ls_structure-my_f1, ls_structure-my_f2.
```

### Table Expressions

`itab [ ... ]`相当于`READ TABLE itab...`。

如果对应没找到，会抛出 CX_SY_ITAB_LINE_NOT_FOUND 异常，系统变量 SY-SUBRC 不会记录成功与否。

可使用 line_exists(itab[...]) 。

#### Read table index

```html
// Before 7.40
READ TABLE itab INDEX idx INTO wa.
// With 7.40
 wa = itab[ idx ].
```

#### Read table using key

```html
// Before 7.40
READ TABLE itab INDEX idx USING KEY key INTO wa.
// With 7.40
wa = itab[ KEY key INDEX idx ].
```

#### Read table with key

```html
// Before 7.40
READ TABLE itab WITH KEY col1 = lv_col1 col2 = lv_col2 INTO wa.
// With 7.40
wa = itab[ col1 = lv_col1 col2 = lv_col2 ].
```

#### Read table with key components

```html
// Before 7.40
READ TABLE itab WITH TABLE KEY key COMPONENTS col1 = lv_col1 col2 = lv_col2 INTO wa.
// With 7.40
wa = itab[ KEY key col1 = lv_col1 col2 = lv_col2 ].
```

#### Does record exist

```html
// Before 7.40
READ TABLE itab … TRANSPORTING NO FIELDS. 
IF sy-subrc = 0.
  ...... 
ENDIF.
// With 7.40
IF line_exists( itab[ … ] ).
  ...... 
ENDIF. 
```

#### Get table index

```html
// Before 7.40
DATA idx type sy-tabix. 
READ TABLE itab ... TRANSPORTING NO FIELDS. 
idx = sy-tabix.
// With 7.40
DATA(idx) = line_index( itab[ ... ] ).
```

### Conversion Operator CONV

#### Definition

CONV dtype|# ( ... )

`dtype` : Type you want to convert to (explicit) 

`#` : Compiler must use the context to decide the type to convert to (implicit)

#### Example

Method "cl_abap_codepage=>convert_to" expects a string.

Before 7.40

```html
DATA text   TYPE c LENGTH 255.
DATA helper TYPE string.
DATA xstr   TYPE xstring.
helper = text.
xstr = cl_abap_codepage=>convert_to( source = helper ).
```

With 7.40

```html
DATA text TYPE c LENGTH 255.
DATA(xstr) = cl_abap_codepage=>convert_to( source = CONV string( text ) ).
"或则使用"
DATA(xstr) = cl_abap_codepage=>convert_to( source = CONV #( text ) ).
```

### Value Operator Value

#### Definition

`Variables`: VALUE dtype|# (  ).

`Structures`: VALUE dtype|# ( [BASE dobj] compl = a1 comp2 = a2 ... )

`Tables`: VALUE dtype|# ( [BASE itab] ( line1-com1 = dobj1 ) ( line2... ) ... ) ...)

#### Example for structures

```html
TYPES:BEGIN OF ty_columns1, “Simple structure"
    cols1 TYPE i,
    cols2 TYPE i,
  END OF ty_columns1.
TYPES: BEGIN OF ty_columnns2, "Nested structure"
    coln1 TYPE i,
    coln2 TYPE ty_columns1,
  END OF ty_columns2.
DATA: struc_simple TYPE ty_columns1,
      struc_nest TYPE ty_columns2.
struct_nest = VALUE t_struct(coln1 = 1 
                             coln2-cols1 = 1
                             coln2-cols2 = 2 ).
"或则使用"
struct_nest = VALUE t_struct(coln1 = 1 
                             coln2 = VALUE #( cols1 = 1 cols2 = 2 ) ).
```

#### Example for internal tables

```html
// Elementary line type:
TYPES t_itab TYPE TABLE OF i WITH EMPTY KEY.
DATA itab TYPE t_itab.
itab = VALUE #( ( 1 ) ( 2 ) ( 3 ) ).
// 覆盖
itab = VALUE #( ( 4 ) ( 5 ) ( 6 ) ).
// 追加
itab = VALUE #( BASE itab ( 1 ) ( 2 ) ( 3 ) ).
// Structured line type (RANGES table):
DATA itab TYPE RANGE OF i.
itab = VALUE #( sign = 'I'  
              option = 'BT' ( low = 1  high = 10 )
                            ( low = 21 high = 30 )
                            ( low = 41 high = 50 )
              option = 'GE' ( low = 61 )  ).
```

### For operator

#### Definition

`FOR wa|<fs> IN itab [INDEX INTO idx] [COND]. 	`

`VALUE itab( FOR i = ... [THEN expr] UNTIL|WHERE log_ext ... )`

`REDUCE type (INIT FOR ... NEXT ...)`

#### Explanation

This effectively causes a loop at itab. For each loop the row read is assigned to a work area (wa) or field-symbol(`<fs>`).

This wa or `<fs>` is local to the expression i.e. if declared in a  subrourine the variable wa or `<fs>` is a local variable of that subroutine. Index like SY-TABIX in loop.

```html
TYPES: BEGIN OF ty_ship,
           tknum TYPE tknum,     “Shipment Number
           name  TYPE ernam,     “Name of Person who Created the Object
           city  TYPE ort01,     “Starting city
           route TYPE route,     “Shipment route
       END OF ty_ship.
TYPES: ty_ships TYPE SORTED TABLE OF ty_ship WITH UNIQUE KEY tknum.
TYPES: ty_citys TYPE STANDARD TABLE OF ort01 WITH EMPTY KEY. 
DATA: gt_ships type ty_ships.
```

Example1

```html
// Before 7.40
DATA: gt_citys TYPE ty_citys,
      gs_ship  TYPE ty_ship,
      gs_city  TYPE ort01.
LOOP AT gt_ships INTO gs_ship.
  gs_city =  gs_ship–city.
  APPEND gs_city TO gt_citys.
ENDLOOP.
// With 7.40
DATA(gt_citys) = VALUE ty_citys( FOR ls_ship IN gt_ships ( ls_ship-city ) ).
```

注意：ls_ship似乎尚未声明，但已隐式声明。

Example2

```html
// Before 7.40
DATA: gt_citys TYPE ty_citys,
      gs_ship  TYPE ty_ship,
      gs_city  TYPE ort01.
LOOP AT gt_ships INTO gs_ship WHERE route = ‘R0001’.
  gs_city =  gs_ship–city.
  APPEND gs_city TO gt_citys.
ENDLOOP.
// With 7.40
DATA(gt_citys) = VALUE ty_citys( FOR ls_ship IN gt_ships WHERE ( route = 'R0001' )
                                                               ( ls_ship-city ) ).
```

**FOR with THEN and UNTIL|WHILE**

`FOR i = … [THEN expr] UNTIL|WHILE log_exp`

```html
TYPES:
  BEGIN OF ty_line,
    col1 TYPE i,
    col2 TYPE i,
    col3 TYPE i,
  END OF ty_line,
  ty_tab TYPE STANDARD TABLE OF ty_line WITH EMPTY KEY.
// Before 7.40
DATA: gt_itab TYPE ty_tab,
      j TYPE i.
FIELD-SYMBOLS <ls_tab> TYPE ty_line.
j = 1.
DO.
  j = j + 10.
  IF j > 40. EXIT. ENDIF.
  APPEND INITIAL LINE TO gt_itab ASSIGNING <ls_tab>.
  <ls_tab>–col1 = j.
  <ls_tab>–col2 = j + 1.
  <ls_tab>–col3 = j + 2.
ENDDO.
// With 7.40
DATA(gt_itab) = VALUE ty_tab( FOR j = 11 THEN j + 10 UNTIL j > 40
                            ( col1 = j col2 = j + 1 col3 = j + 2  ) ).
```

### Reduction operator REDUCE

#### Definition

```html
REDUCE type(
  INIT result = start_value
  FOR for_exp1
  FOR for_exp2
  ......
  NEXT …
  result = iterated_value
… )
```

虽然VALUE和NEW表达式可以包含FOR表达式，但REDUCE必须包含至少一个FOR表达式。您可以在REDUCE中使用各种FOR表达式

- With IN for iterating internal tables
- With UNTIL or WHILE for conditional iterations

#### Example1

```html
// Before 7.40
DATA: lv_lines TYPE i.
LOOP AT gt_itab INTO ls_itab where F1 = ‘XYZ’.
  lv_lines = lv_lines + 1.
ENDLOOP.
// With 7.40 
DATA(lv_lines) = Reduce i( INIT x = 0 FOR wa IN gt_itab
                           WHERE( F1 = 'XUZ' ) NEXT x = x + 1 )
```

#### Example2

```html
DATA gt_itab TYPE STANDARD TABLE OF i WITH EMPTY KEY.
gt_itab = VALUE #( FOR j = 1 WHILE j <= 10 ( j ) ).
// Before 7.40
DATA: lv_lines TYPE i.
LOOP AT gt_itab INTO ls_itab where F1 = ‘XYZ’.
  lv_lines = lv_lines + 1.
ENDLOOP.
// With 7.40 
DATA(lv_sum) = REDUCE i( INIT x = 0 FOR wa IN itab NEXT x = x + wa ).
```

### Conditional operators COND and SWITCH

#### Definition

```html
COND dtype|#( WHEN log_exp1 THEN result1
[ WHEN log_exp2 THEN result2 ]
...
[ ELSE resultn ] ) .

SWITCH dtype|#( operand WHEN const1 THEN result1
[ WHEN const2 THEN result2 ]
...
[ ELSE resultn ] ) .
```

#### Example for COND

```html
DATA(time) = COND string(
    WHEN sy-timlo < ‘120000’ THEN |{ sy-timlo TIME = ISO } AM|
    WHEN sy-timlo > ‘120000’ THEN
      |{ CONV t( sy-timlo – 12 * 3600 ) TIME = ISO } PM|
    WHEN sy-timlo = ‘120000’ THEN |High Noon|
    ELSE
      THROW cx_cant_be( ) 
).
```

#### Example for SWITCH

```html
DATA(text) = NEW class( )->meth(
  SWITCH #( sy-langu
    WHEN 'D' THEN 'DE'
    WHEN 'E' THEN 'EN'
    ELSE THROW cx_langu_not_supported(
    )
  )
).
```

### Move Corresponding Operator

#### Definition

`CORRESPONDING type( [BASE ( base )] struct|itab [mapping|except] )`

#### Example Code

```html
// With 7.40
TYPES: BEGIN OF line1,
  col1 TYPE i, 
  col2 TYPE i,
END OF line1.
TYPES: BEGIN OF line2, 
  col1 TYPE i, 
  col2 TYPE i, 
  col3 TYPE i, 
END OF line2.
DATA(ls_line1) = VALUE line1( col1 = 1 col2 = 2 ).
WRITE: / ‘ls_line1 =’ ,15 ls_line1–col1, ls_line1–col2.
DATA(ls_line2) = VALUE line2( col1 = 4 col2 = 5 col3 = 6 ).
WRITE: / ‘ls_line2 =’ ,15 ls_line2–col1, ls_line2–col2, ls_line2–col3.
SKIP 2.

ls_line2 = CORRESPONDING #( ls_line1 ).
WRITE: / ‘ls_line2 = CORRESPONDING #( ls_line1 )’,70 ‘Result is ls_line2 = ‘     
        ,ls_line2–col1, ls_line2–col2, ls_line2–col3.
SKIP.

ls_line2 = VALUE line2( col1 = 4 col2 = 5 col3 = 6 ).   “Restore ls_line2
ls_line2 = CORRESPONDING #( BASE ( ls_line2 ) ls_line1 ).
WRITE: / ‘ls_line2 = CORRESPONDING #( BASE ( ls_line2 ) ls_line1 )’
       , 70 ‘Result is ls_line2 = ‘, ls_line2–col1, ls_line2–col2, ls_line2–col3.
SKIP.

ls_line2 = VALUE line2( col1 = 4 col2 = 5 col3 = 6 ).   “Restore ls_line2
DATA(ls_line3) = CORRESPONDING line2( BASE ( ls_line2 ) ls_line1 ).
WRITE: / ‘DATA(ls_line3) = CORRESPONDING line2( BASE ( ls_line2 ) ls_line1 )’
       , 70 ‘Result is ls_line3 = ‘ , ls_line3–col1, ls_line3–col2, ls_line3–col3.
```

### Strings

#### String Templates

字符串模板由两个字符"|"括起来并创建一个字符串。 

文字文本由不在大括号"{}"中的所有字符组成。

大括号可以包含：

- 数据对象
- 计算表达式
- 构造函数表达式
- 表表达式
- 预定义功能，或 功能方法和方法链

```html
// Before 7.40
DATA itab TYPE TABLE OF scarr.
DATA wa LIKE LINE OF itab.
DATA output TYPE string.
SELECT * FROM scarr INTO TABLE itab.
READ TABLE itab WITH KEY carrid = ‘LH’ INTO wa.
CONCATENATE ‘Carrier:’ wa-carrname INTO output SEPARATED BY space.
cl_demo_output=>display( output ).
// With 7.40
SELECT * FROM scarr INTO TABLE @DATA(lt_scarr).
cl_demo_output=>display( |Carrier: { lt_scarr[ carrid = ‘LH’ ]–carrname }|  ).
```

#### Conacatenation

```html
// Before 7.40
DATA lv_output TYPE string.
CONCATENATE ‘Hello’ ‘world’ INTO lv_output SEPARATED BY space.
// With 7.40
DATA(lv_out) = |Hello| & | | & |world|.
```

#### Width/Alignment/Padding

```html
// With 7.40
WRITE / |{ 'Left'   WIDTH = 20 ALIGN = LEFT   PAD = '0' }|.
WRITE / |{ 'Center' WIDTH = 20 ALIGN = CENTER PAD = '0' }|.
WRITE / |{ 'Right'  WIDTH = 20 ALIGN = RIGHT  PAD = '0' }|.
```

#### Case

```html
WRITE / |{ ‘Text’ CASE = (cl_abap_format=>c_raw) }|.
WRITE / |{ ‘Text’ CASE = (cl_abap_format=>c_upper) }|.
WRITE / |{ ‘Text’ CASE = (cl_abap_format=>c_lower) }|.
```

#### ALPHA conversion

```html
DATA(lv_vbeln) = ‘0000012345’.
WRITE / |{ lv_vbeln  ALPHA = OUT }|.     
// OR use "ALPHA = IN" to go in other direction
```

#### Date conversion

```html
DATA(pa_date) = sy-datum.
WRITE / |{ pa_date DATE = ISO }|.           “Date Format YYYY-MM-DD
WRITE / |{ pa_date DATE = User }|.          “As per user settings
WRITE / |{ pa_date DATE = Environment }|.   “Formatting setting of language environment
```

### Loop at group by

#### Definition

```html
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

```html
TYPES: BEGIN OF ty_employee,
  name TYPE char30,
  role    TYPE char30,
  age    TYPE i,
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

```html
// Before 7.40
DATA: ls_lfa1  TYPE lfa1,
      lv_name1 TYPE lfa1-name1.
ls_lfa1  = my_class=>get_lfa1( ).
lv_name1 = ls_lfa1-name1.
// With 7.40
DATA(lv_name1) = my_class=>get_lfa1( )-name1.
```

#### Methods that return a type BOOLEAN

类型BOOLEAN不是真正的布尔值，而是具有允许值"X"，"-"和"blank"的char1。使用“ FLAG”或“ WDY_BOOLEAN”类型也可以。

```html
// Before 7.40
IF my_class=>return_boolean( ) = abap_true.
  ...
ENDIF.
// With 7.40
IF my_class=>return_boolean( ).
  ...
ENDIF.
```

#### New Operator

```html
// Before 7.40
DATA: lo_delivs TYPE REF TO zcl_sd_delivs,
      lo_deliv  TYPE REF TO zcl_sd_deliv.
CREATE OBJECT lo_delivs.
CREATE OBJECT lo_deliv.
lo_deliv = lo_delivs->get_deliv( lv_vbeln ).
// With 7.40
DATA(lo_deliv) = new zcl_sd_delivs->get_deliv( lv_vbeln ).
```

### Filter

基于一个表中的记录过滤另一个表中的记录。

#### Definition

```html
FILTER ptype|#( itab [EXCEPT] [IN ftab] [USING KEY keyname]
           WHERE c1 op f1 [AND c2 op f2 […]] )
```

#### Solution

```html
TYPES: BEGIN OF ty_filter,
    cityfrom TYPE spfli–cityfrom,
    cityto   TYPE spfli–cityto,
    f3       TYPE i,
END OF ty_filter,
ty_filter_tab TYPE HASHED TABLE OF ty_filter WITH UNIQUE KEY cityfrom cityto.
DATA: lt_splfi TYPE STANDARD TABLE OF spfli.
SELECT * FROM spfli APPENDING TABLE lt_splfi.
DATA(lt_filter) = VALUE ty_filter_tab( f3 = 2
    ( cityfrom = ‘NEW YORK’  cityto  = ‘SAN FRANCISCO’ )
    ( cityfrom = ‘FRANKFURT’ cityto  = ‘NEW YORK’ )  ).
DATA(lt_myrecs) = FILTER #( lt_splfi IN lt_filter
                  WHERE cityfrom = cityfrom 
                  AND cityto = cityto ).
“Output filtered records
LOOP AT lt_myrecs ASSIGNING FIELD–SYMBOL(<ls_rec>).
  WRITE: / <ls_rec>–carrid,8 <ls_rec>–cityfrom,30
           <ls_rec>–cityto,45 <ls_rec>–deptime.
ENDLOOP.
```

