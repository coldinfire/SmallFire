---
title: " ABAP 数据类型和变量 "
date: 2018-05-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbasis

---

### ABAP 数据类型

数据类型在 ABAP 程序中用于定义具体的变量类型。

#### 基本数据类型

ABAP 基本数据类型是系统内部定义的数据类型。

| Type | Description              | Length             | Type    | Description            | Length                 |
| :--- | :----------------------- | :----------------- | :------ | :--------------------- | :--------------------- |
| C    | 文本字段（字母数字字符） | 定义时指定字符个数 | I       | 整数(非整数会四舍五入) | 4字节                  |
| N    | 数字文本字段（数字字符） | 定义时指定字符个数 | F       | 浮点类型               | 8字节                  |
| D    | 日期(YYYYMMDD)           | 8字符              | P       | 组合数值类型(1-16)     | 定义时指定长度，小数位 |
| T    | 时间(HHMMSS)             | 6字符              | string  | 变长字符串             | 长度不固定             |
| X    | 十六进制                 | 定义时指定字节数   | xstring | 变长字节序列类型       | 长度不固定             |

#### 本地数据类型

在 ABAP 程序中，使用 `TYPES` 定义本地数据类型。定义局部数据类型时可以使用基本数据类型、局部数据类型、全局数据类型。

```ABAP
TYPES: BEGIN OF str_demo,
    index TYPE i,
    matnr TYPE matnr,
    werks TYPE werks_d,
    message TYPE string,
  END OF str_demo.
DATA: demo TYPE str_dmeo.
```

#### 全局数据类型

ABAP 数据字典数据类型和系统变量，无需声明可在系统中直接使用。

### ABAP 变量

#### 常用系统变量 

系统变量表 `SYST` (ABAP System Fields)。ABAP 系统变量可供所有程序访问，字段值由运行时环境填充。

| 变量     | 描述                   | 变量           | 描述                  |
| :------- | :--------------------- | :------------- | :-------------------- |
| SY-UNAME | 用户登录名             | SY-TCODE       | 当前执行的事物码      |
| SY-SUBRC | 表示系统执行成功与否   | SY-TMAXL       | 内表总行数            |
| SY-REPID | 当前程序名             | SY-BATCH       | 程序是否后台 JOB 执行 |
| SY-DATUM | 当前系统日期           | SY-HOST        | 服务器名称            |
| SY-UZEIT | 当前系统时间           | SY-DYNNR       | 屏幕的编号            |
| SY-INDEX | DO-ENDDO 中是有效的    | SY-DBCNT       | DB 操作处理过的表行号 |
| SY-TABIX | LOOP索引，Read内表索引 | SY-LANGU       | 当前登陆的语言        |
| SY-MSGID | Message Class          | SY-MSGV1/2/3/4 | Message Variable      |
| SY-MSGTY | Message Type           | SY-MSGNO       | Message Number        |
| SPACE    | 空白字符串             | SY-ZONLO       | 当前时区              |

#### 自定义变量类型

可以使用 DATA 语句定义数据变量，变量名最长可定义 30 位，变量的类型可以是基本类型，局部类型，全局类型。

`DATA var_name(len) TYPE var_type [VALUE value] [DECIMALS n].`

参考字段定义变量

`DATA <var1> like <var2>.`  & `DATA <var1> TYPE <var2>.`

- 透明表、结构、数据字典：既是类型又是对象，可用 TYPE 和 LIKE
- 只能使用 LIKE 引用另一自定义变量的类型，不可以使用 TYPE

`DATA <var> LIKE LINE OF xxx.`

- 后面参照内表，表示该变量具有和参照内表一样的结构，可当做工作区使用

`DATA <var> LIKE TABLE OF xxx.`

- 后面参照结构，表示该变量是一个和参照结构一样的内表，这个内表和后面参照的结构一样

#### 参考变量

声明引用变量的语法是:

`DATA <ref> TYPE REF TO <type> VALUE IS INITIAL. `

- REF TO 附加声明一个引用变量
- REF TO 之后的规范指定了引用变量的静态类型
- 静态类型限制引用变量的对象集合
- 引用变量的动态类型是它当前引用的数据类型或类
- 静态类型总是更加通用或与动态类型相同
- TYPE 添加用于创建绑定引用类型和起始值，并且只能在 VALUE 添加后指定 IS INITIAL

### 变量赋值

#### 定义时赋值

定义变量时可以通过 VALUE 语句赋初始值。

- 单引号 '' 和 Grave `` 的区别：grave 能够识别字符串中包含的所有空格

#### 程序中赋值

`MOVE var1 TO var2.` & `WRITE var1 TO var2.`

- 变量 2 只能是字符串类型：C、N、D、T

`MOVE-CORRESPONDING`语句经常用于不同结构体之间赋值，其特点是**找到名字相同的字段进行赋值。**

### 定义常量、宏

#### 常量定义

`CONSTANTS var_name(len) TYPE <var_type> VALUE <value>.`

`CONSTANTS var_name(len) LIKE <var> VALUE <value>.`

#### 宏定义 

如果需要在程序中多次重复使用同一组语句，可以将它们包含在宏中。最常使用的场景是 ALV 的字段目录构造。

- 宏定义应在程序中使用宏之前发生
- 宏是基于占位符设计的，宏定义中占位符的最大数量为 9

```ABAP
DEFINE <macro_name>. 
  <statements> 
END-OF-DEFINITION. 
<macro_name> [<param1> <param2>....].
```

### ABAP 运算符&函数

#### 二元运算符

ABAP 的操作符需要跟操作数之间至少一个空格分开。

| 运算符 | 描述 | 运算符 | 描述 | 运算符 | 描述           | 运算符 | 描述         |
| :----- | :--- | :----- | :--- | :----- | :------------- | :----- | :----------- |
| +      | 加法 | *      | 乘法 | **     | 乘幂           | MOD    | 取模，求余数 |
| -      | 减法 | /      | 除法 | DIV    | 整除，忽略余数 |        |              |

#### 逻辑运算符

| 运算符   | 描述     | 运算符   | 描述     | 运算符   | 描述   |
| :------- | :------- | :------- | :------- | :------- | :----- |
| GE \| >= | 大于等于 | LE \| <= | 小于等于 | EQ \| =  | 等于   |
| GT \| >  | 大于     | LT \| <  | 小于     | NE \| <> | 不等于 |

#### 函数使用

| 函数     | 描述                    | 函数     | 描述                     |
| :------- | :---------------------- | :------- | :----------------------- |
| CEIL(N)  | 返回大于数值N的最小整数 | TRUNC(N) | 返回数值N的整数部分      |
| FLOOR(N) | 返回小于数值N的最大整数 | FRAC(N)  | 返回数值N的小数部分      |
| ABS(N)   | 求绝对值                | SIGN(N)  | N>0 = 1；N<0 = -1；N=0=0 |

###  DESCRIBE 使用

#### DESCRIBE FIELD

对一个 Elementary data 的属性进行描述，可以通过这条语句知道某一个 data 的类型、长度、小数点、输出长度等信息。

```ABAP
DESCRIBE FIELD dobj 
  [TYPE typ [COMPONENTS com]] 
  [LENGTH ilen IN {BYTE|CHARACTER} MODE] 
  [DECIMALS dec] 
  [OUTPUT-LENGTH olen] 
  [HELP-ID hlp] 
  [EDIT MASK mask].
```

#### DESCRIBE TABLE

`DESCRIBE TABLE itab [KIND knd] [LINES lin] [OCCURS n].`

不同的选项返回表类型、行数、初始化大小。另外，系统字段 `sy-tfill` 和 `sy-tleng` 保存着内表行数量和以字节计的表行长度。

KIND：T 标准表；S 排序表；H 哈希表

LINES：内表行数，返回值为 i 类型。可以使用函数 LINES(itab) 计算内表行数

OCCURS：内表所需要的初始化内存大小

#### DESCRIBE DISTANCE

`DESCRIBE DISTANCE BETWEEN dobj1 AND dobj2 INTO dst IN {BYTE|CHARACTER} MODE.`

- dobj1 and dobj2 两个变量地址起始位置的距离






