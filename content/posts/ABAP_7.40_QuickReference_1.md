---
title: " ABAP 7.40 Quick Reference Part1 "
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

注意 @DATA(ITAB) 语句的强大，有了它，我们在访问数据库的时候，直接写 SELECT 就好了，不需要构建各式各样的內表和表类型了。注意在使用 FOR ALL ENTRIES 语句的时候，管理的内表前面要加上 @ 。

#### Data statement

```ABAP
*Before 7.40
DATA text TYPE string. 
text = 'ABC'.
*With 7.40
DATA(text) = 'ABC'.
```

#### Loop at into work area

```ABAP
*Before 7.40
DATA wa like LINE OF itab. 
LOOP AT itab INTO wa.     
  ...... 
ENDLOOP.
*With 7.40
LOOP AT itab INTO DATA(wa).
  ......
ENDLOOP.
```

#### Call method with parameter

```ABAP
*Before 7.40
DATA a1 TYPE char10.
DATA a2 TYPE char10.
oref->method( IMPORTING p1 = a1
              IMPORTING p2 = a2 ).
*With 7.40
oref->method( IMPORTING p1 = DATA(a1)
              IMPORTING p2 = DATA(a2) ). 
```

#### Loop at assigning

```ABAP
*Before 7.40
FIELD-SYMBOLS: <line> type any.
LOOP AT itab ASSIGNING <line>.
  ...... 
ENDLOOP.
*With 7.40
LOOP AT itab ASSIGNING FIELD-SYMBOL(<line>).   
  .......
ENDLOOP.
```

#### Read assigning

```ABAP
*Before 7.40
FIELD-SYMBOLS: <line> type any.
READ TABLE itab ASSIGNING <line>.
*With 7.40
READ TABLE itab ASSIGNING FIELD-SYMBOL(<line>).
```

#### Select into table

```ABAP
*Before 7.40
DATA itab TYPE TABLE OF dbtab.
DATA lv_fld1 TYPE char20.
SELECT * FROM dbtab INTO TABLE itab WHERE fld1 = lv_fld1.
*With 7.40
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

```ABAP
*Before 7.40
SELECT SINGLE f1 f2 FROM scarr INTO (lv_f1, lv_f2) WHERE carrid = id.
*With 7.40
SELECT SINGLE f1 AS my_f1, F2 AS my_f2 
FROM dbtab INTO DATA(ls_structure) WHERE carrid = @id.
WRITE: / ls_structure-my_f1, ls_structure-my_f2.
```

### Table Expressions

`itab [ ... ]`相当于`READ TABLE itab...`。

如果对应没找到，会抛出 CX_SY_ITAB_LINE_NOT_FOUND 异常，系统变量 SY-SUBRC 不会记录成功与否。

可使用 line_exists(itab[...]) 。

#### Read table index

```ABAP
*Before 7.40
READ TABLE itab INDEX idx INTO wa.
*With 7.40
DATA(wa) = itab[ idx ].
```

#### Read table using key

```ABAP
*Before 7.40
READ TABLE itab INTO wa INDEX idx USING KEY key.
*With 7.40
DATA(wa) = itab[ KEY key INDEX idx ].
```

#### Read table with key

```ABAP
*Before 7.40
READ TABLE itab INTO wa WITH KEY col1 = lv_col1 col2 = lv_col2 .
*With 7.40
DATA(wa) = itab[ col1 = lv_col1 col2 = lv_col2 ].
```

#### Read table with key components

```ABAP
*Before 7.40
READ TABLE itab INTO wa WITH TABLE KEY key COMPONENTS col1 = lv_col1 
                                                      col2 = lv_col2 .
*With 7.40
DATA(wa) = itab[ KEY key col1 = lv_col1 col2 = lv_col2 ].
```

#### Does record exist

```ABAP
*Before 7.40
READ TABLE itab … TRANSPORTING NO FIELDS. 
IF sy-subrc = 0.
  ...... 
ENDIF.
*With 7.40
IF line_exists( itab[ … ] ).
  ...... 
ENDIF. 
```

#### Get table index

```ABAP
*Before 7.40
DATA idx type sy-tabix. 
READ TABLE itab ... TRANSPORTING NO FIELDS.
IF sy-subrc = 0.
  idx = sy-tabix. 
ENDIF.
*With 7.40
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

```ABAP
DATA text   TYPE c LENGTH 255.
DATA helper TYPE string.
DATA xstr   TYPE xstring.
helper = text.
xstr = cl_abap_codepage=>convert_to( source = helper ).
```

With 7.40

```ABAP
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

