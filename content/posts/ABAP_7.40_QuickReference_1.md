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

## Inline Declarations - 内联声明

` @DATA(...)` 语句非常的强大，有了它我们在访问数据库的时候，直接写 SELECT 就可以了，不需要再去构建各式各样的內表和表类型了。注意在使用 FOR ALL ENTRIES 语句的时候，管理的内表前面要加上 @ 。

内联声明方便了我们的使用，在内表中它会自动根据读取的内表类型定义相应的工作区类型。但是使用这种方法注意作用域问题。

- 指针声明符：`FIELD-SYMBOL(...)`

### Data statement

```ABAP
*Before 7.40
DATA text TYPE string.
text = 'ABC'.
*With 7.40
DATA(text) = 'ABC'.
```

### Select into table

```ABAP
*Before 7.40
DATA itab TYPE TABLE OF mara.
DATA lv_matnr TYPE matnr.
SELECT * FROM mara INTO TABLE itab WHERE matnr = lv_matnr.
*With 7.40
SELECT mara~matnr,marc~werks
  FROM mara INNER JOIN marc ON mara~matnr = marc~matnr
  FOR ALL ENTRIES IN @itab
  WHERE mara~matnr = @itab-matnr
  INTO TABLE @DATA(lt_data).
```

### Select single 

```ABAP
*Before 7.40
SELECT SINGLE f1 f2 FROM scarr INTO (lv_f1, lv_f2) WHERE carrid = p_id.
*With 7.40
SELECT SINGLE f1 AS lv_f1, F2 AS lv_f2 
  FROM dbtab INTO @DATA(wa) WHERE carrid = @p_id.
WRITE: / wa-lv_f1, wa-lv_f2.
```

### Loop at into work area

```ABAP
*Before 7.40
DATA wa like LINE OF itab. 
LOOP AT itab INTO wa. ... ENDLOOP.
*With 7.40
LOOP AT itab INTO DATA(wa). ... ENDLOOP.
```

### Call method with parameter

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

### Loop at assigning

```ABAP
*Before 7.40
FIELD-SYMBOLS: <line> type any.
LOOP AT itab ASSIGNING <line>. ... ENDLOOP.
*With 7.40
LOOP AT itab ASSIGNING FIELD-SYMBOL(<line>). ... ENDLOOP.
```

### Read assigning

```ABAP
*Before 7.40
FIELD-SYMBOLS: <line> type any.
READ TABLE itab ASSIGNING <line>.
*With 7.40
READ TABLE itab ASSIGNING FIELD-SYMBOL(<line>).
```

## 内表操作

### Table Expressions

`itab [ ... ]`相当于`READ TABLE itab ...`。

如果对应条件没找到数据，会抛出 CX_SY_ITAB_LINE_NOT_FOUND 异常，系统变量 SY-SUBRC 不会记录成功与否。可使用 `line_exists(itab[...])` 判断内表是否存在满足条件的数据 。

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
READ TABLE itab INTO wa WITH KEY col1 = lv_col1 col2 = lv_col2.
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
READ TABLE itab ... TRANSPORTING NO FIELDS. 
IF sy-subrc = 0. ...  ENDIF.
*With 7.40
IF line_exists( itab[ … ] ). ... ENDIF. 
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

### Value Operator Value - Value 作为赋值语句

新语法中，可以使用 VALUE 作为赋值语句，主要用来为内表、结构、变量等对象赋值。

在 VALUE 子句中，字段可以分开赋值，也可以使用结构整体赋值；为内表赋值时，需要用小括号将一行的数据包在一起。

#### Definition

Variables：`VALUE dtype|# (  ).`  构造一个任意类型的初始值。

Structures：`VALUE dtype|# ( [BASE dobj] compl = a1 comp2 = a2 ... ).`构造一个任意类型的结构体的初始值。

Tables：`VALUE dtype|# ( [BASE itab] ( line1-com1 = dobj1 ) ( line2... ) ... )` 构造一个任意类型的内表的初始值。

- `dtype` ：想要转换成的类型（显式）

- `#` ：编译器必须使用上下文来决定要转换为的类型（隐式）
- 如果 dtype 是个表，则必须指定 key 值，或者声明为 empty key

#### Example for structures

```ABAP
TYPES:BEGIN OF ty_columns1,   "Simple structure"
  cols1 TYPE i,
  cols2 TYPE i,
END OF ty_columns1.
TYPES: BEGIN OF ty_columns2, "Nested structure"
  coln1 TYPE i,
  coln2 TYPE ty_columns1,
END OF ty_columns2.
DATA:struc_nest TYPE ty_columns2.
struc_nest = VALUE ty_columns2( coln1 = 1 coln2-cols1 = 1 coln2-cols2 = 2 ).
"或则使用"
struc_nest = VALUE ty_columns2( coln1 = 1 coln2 = VALUE #( cols1 = 1 cols2 = 2 ) ).
```

#### Example for internal tables

- 内表不能带表头

```ABAP
DATA itab TYPE TABLE OF i WITH EMPTY KEY.
*赋值
itab = VALUE #( ( 1 ) ( 2 ) ( 3 ) ).
*覆盖
itab = VALUE #( ( 4 ) ( 5 ) ( 6 ) ).
*追加
itab = VALUE #( BASE itab ( 1 ) ( 2 ) ( 3 ) ).
cl_demo_output=>display( itab ).
* Structured line type (RANGES table)
DATA rang_itab TYPE RANGE OF i.
rang_itab = VALUE #( 
              sign = 'I'  
              option = 'BT' ( low = 1  high = 10 )
                            ( low = 21 high = 30 )
                            ( low = 41 high = 50 )
              option = 'GE' ( low = 61 )  ).
cl_demo_output=>display( rang_itab ).
```

### For operator

在内表赋值语句中，可以使用 FOR 语句从其他内表中批量引入数据并处理；可以认为是加强版的 LOOP AT 语句，与 REDUCE、VALUE 关键字配合使用。

- 使用 FOR 语句时，需要为内表定义临时工作区，如 wa / `<fs>`，仅允许在当前语句中使用，赋值过程中会使用到该工作区。
- 但在 WHERE 条件里，只能直接使用内表的字段名，需要注意的是，WHERE 后面接的条件语句必须使用小括号包起来。
- INDEX INTO 定义的临时变量可用来记录当前操作行的序列，作用与 LOOP 语句中的系统变量 SY-TABIX 类似。

#### Definition

```ABAP
...FOR wa|<fs> IN itab [INDEX INTO idx] [COND].
...VALUE itab( FOR i = ... [THEN expr] UNTIL|WHERE log_ext ... )
...REDUCE dtype( INIT xxx FOR ... NEXT ... )
```

#### Examples

```ABAP
TYPES: BEGIN OF ty_ship,
  tknum TYPE tknum,     "Shipment Number"
  name  TYPE ernam,     "Name of Person who Created the Object"
  city  TYPE ort01,     "Starting city"
  route TYPE route,     "Shipment route"
END OF ty_ship.
TYPES: ty_ships TYPE SORTED TABLE OF ty_ship WITH UNIQUE KEY tknum.
TYPES: ty_citys TYPE STANDARD TABLE OF ort01. 
```

Example 1：用来自 GT_SHIPS 的 city 填充内部表 GT_CITYS。

```ABAP
*Before 7.40
DATA: gt_ships type ty_ships,
      gs_ship  TYPE ty_ship,
      gt_citys TYPE ty_citys,
      gs_city  TYPE ort01.
LOOP AT gt_ships INTO gs_ship.
  gs_city =  gs_ship–city.
  APPEND gs_city TO gt_citys.
  CLEAR:gs_city, gs_ship.
ENDLOOP.
*With 7.40
DATA(gt_citys) = VALUE ty_citys( FOR ls_ship IN gt_ships ( ls_ship-city ) ).
```

- 注意：ls_ship 似乎尚未声明，但已隐式声明，作用范围在 VALUE ( ) 里面。


Example 2：使用来自 GT_SHIPS 且路线为 R0001 的 city 填充内部表 GT_CITYS。

```ABAP
*Before 7.40
DATA: gt_citys TYPE ty_citys,
      gs_ship  TYPE ty_ship,
      gs_city  TYPE ort01.
LOOP AT gt_ships INTO gs_ship WHERE route = ‘R0001’.
  gs_city =  gs_ship–city.
  APPEND gs_city TO gt_citys.
  CLEAR:gs_city, gs_ship.
ENDLOOP.
*With 7.40
DATA(gt_citys) = VALUE ty_citys( FOR ls_ship IN gt_ships 
                   WHERE ( route = 'R0001' ) ( ls_ship-city ) ).
```

#### FOR with THEN and UNTIL|WHILE

`FOR i = … [THEN expr] UNTIL|WHILE log_exp`

```ABAP
TYPES: BEGIN OF ty_line,
    col1 TYPE i,
    col2 TYPE i,
    col3 TYPE i,
  END OF ty_line,
  ty_tab TYPE STANDARD TABLE OF ty_line WITH EMPTY KEY.
*Before 7.40
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
*With 7.40
DATA(gt_itab) = VALUE ty_tab( FOR j = 11 THEN j + 10 UNTIL j > 40
                              ( col1 = j col2 = j + 1 col3 = j + 2 ) 
                            ).
```

#### REDUCE 结合 FOR 使用

```ABAP
TYPES: BEGIN OF ty_result,
    sum   TYPE i,
    max   TYPE i,
    min   TYPE i,
    count TYPE i,
    avg   TYPE p DECIMALS 2,
  END OF ty_result.
DATA(result) = REDUCE ty_result( 
      INIT res = VALUE ty_result( max = 0 min = 99999 )
      FOR <fs> IN flights
      NEXT res-sum = res-sum + <fs>-distance
           res-count = res-count + 1
           res-max = nmax( val1 = res-max val2 = <fs>-distance )
           res-min = nmin( val1 = res-min val2 = <fs>-distance )
   ).
result-avg = result-sum / result-count.
```

### Reduction operator - REDUCE

#### Definition

```ABAP
...REDUCE dtype(
  INIT result = start_value
  FOR for_exp1
  FOR for_exp2
  ...
  NEXT ...
       result = iterated_value
       ...
).
```

虽然 VALUE 和 NEW 表达式可以包含 FOR 表达式，但 REDUCE 必须包含至少一个 FOR 表达式。可以在 REDUCE 中使用各种 FOR 表达式

- 使用 IN 迭代内部表
- 使用 UNTIL 或 WHILE 进行条件迭代

#### Example

```ABAP
DATA gt_itab TYPE STANDARD TABLE OF i WITH EMPTY KEY.
gt_itab = VALUE #( FOR j = 1 WHILE j <= 10 ( j ) ).
* 7.40
DATA: lv_lines TYPE i,
      ls_itab TYPE i.
LOOP AT gt_itab INTO ls_itab.
  lv_lines = lv_lines + 1.
ENDLOOP.
*With 7.40
"统计行数"
DATA(lv_line) = REDUCE i( INIT x = 0 FOR wa IN gt_itab NEXT x = x + 1 ).
"求和"
DATA(lv_sum) = REDUCE i( INIT x = 0 FOR wa IN gt_itab NEXT x = x + wa ).
```

#### 使用类引用

write 方法返回对实例对象的引用。

```ABAP
TYPES outref TYPE REF TO if_demo_output.
DATA(output) = REDUCE outref( INIT out  = cl_demo_output=>new( )
                                   text = `Count up:`
                              FOR n = 1 UNTIL n > 11
                              NEXT out = out->write( text )
                                   text = |{ n }| ).
output->display( ).
```

### Move Corresponding Operator

#### MOVE-CORRESPONDING

Structures：`MOVE-CORRESPONDING [EXACT] struc1 TO struc2 [EXPANDING NESTED TABLE].`

Internal tables：`MOVE-CORRESPONDING [EXACT] itab1 TO itab2 [EXPANDING NESTED TABLE] [KEPPING TARGET LINES].`

#### Corresponding

语法：`CORRESPONDING dtype|#( [BASE ( base )] struct|itab [mapping t1 = s1|except {t1 t2}] ).`

- MAPPING：允许使用名称不同的组件映射字段以符合数据传输的条件
- EXCEPT：允许列出必须从数据传输中排除的字段

#### Example Code

```ABAP
*With 7.40
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
cl_demo_output=>display( ls_line1 ).
DATA(ls_line2) = VALUE line2( col1 = 4 col2 = 5 col3 = 6 ).
cl_demo_output=>display( ls_line2 ).
"CORRESPONDING"
ls_line2 = CORRESPONDING #( ls_line1 ).
cl_demo_output=>display( ls_line2 ).
"CORRESPONDING BASE"
"使用ls_line2的现有内容作为基础并被ls_line1中的匹配列覆盖"
ls_line2 = VALUE line2( col1 = 4 col2 = 5 col3 = 6 ).
ls_line2 = CORRESPONDING #( BASE ( ls_line2 ) ls_line1 ).
cl_demo_output=>display( ls_line2 ).
ls_line2 = VALUE line2( col1 = 4 col2 = 5 col3 = 6 ).
DATA(ls_line3) = CORRESPONDING line2( BASE ( ls_line2 ) ls_line1 ).
cl_demo_output=>display( ls_line3 ).
"MOVE-CORRESPONDING"
MOVE-CORRESPONDING ls_line2 TO ls_line3.
cl_demo_output=>display( ls_line3 ).
```

### Filter

基于一个表中的记录过滤另一个表中的记录。

#### Definition

`FILTER ptype|#( itab [EXCEPT] [IN ftab] [USING KEY keyname]
           WHERE c1 op f1 [AND c2 op f2 […]] )`

- EXCEPT：将返回完全相反的记录。

#### Template

```ABAP
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

