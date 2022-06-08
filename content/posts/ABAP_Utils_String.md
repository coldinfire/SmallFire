---
title: " ABAP 字符串处理 "
date: 2019-09-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

字符串的处理在程序中的使用十分常见，在这里结合自己日常的使用对ABAP的字符串常用操作进行总结，以便后续使用。

#### 1.获取字符串长度

`var1  = strlen( str );`将 str 作为字符数据处理，计算出其字符长度。

```ABAP
DATA:l_len type i,
     str1(20) VALUE '1234'.
l_len = strlen(str1).
WRITE: len.  
```

#### 2.拼接字符串

将多个字符串拼接到指定的变量 Var 中，注意 Var 的长度。C、D、N、T数据类型的前导空格会保留，尾部空格会去掉，对 String 类型的所有空格都会保留。

`CONCATENATE <str1> ... <strn> into <var> [SEPARATED BY <s>][RESPECTING BLANKS].`

- `[SEPARATED BY <s>]`：根据指定间隔符号 S 进行拼接，SPACE
- `[RESPECTING xx]`：针对C、D、N、T 数据，表示尾部空格会保留

#### 3.压缩字符串

去除字段中的前后空格，并将字段中多个空格使用一个空格替换。

`CONDENCE <str> [NO-GAPS].`

- NO-GAPS：去除字段中的所有空格

#### 4.截取字符串

- subtext = str+0(4)：从最左边取出4个字符
- subtext = str+2(4)：从第2个字符开始取出4个字符
- str+2(1) = 'X'：变更字符串指定位置的值

#### 5.查找字符串

在字符串中搜索子字符串，成功则 SY-SUBRC = 0。

`SEARCH <var> FOR <str> <options>.`

- str：可以使用正则表达式，也可以使用普通字符串

#### 6.替换字符串

- `REPLACE <str1> WITH <str2> INTO <var> [LENGTH <l>].`
- 将str1在var中的字段替换为str2
- 如果指定长度，则只将str1中指定长度的字符替换为str2中的内容
- 如果替换后内容总长度超过var定义的长度，会出现截断现象

#### 7.SHIFT 截断字符串

将字符串移动 n 个位置。去除字符串后面 0 时，可能会出现有空格的情况，需要压缩空格。

`SHIFT <var> [BY <n> PLACES] [<mode>].`

- [BY n PLACES]：按照给定位置数移动字符串，n为0或负数时字符串保存不变

- MODE：指定字符串截断的方向，LEFT(默认)、RIGHT、CIRCULAR(把左边的字符放到右边)
- `SHIFT <var> LEFT DELETING LEADING <str>.`
- `SHIFT <var> RIGHT DELETING TRAILING <str>.`
- 如果左边第一个字符串或则右边第一个字符串出现在 str 中，将字段移动相应的位置

#### 8.拆分字符串

将字符串的值分配给具体变量：`SPLIT dobj AT sep INTO res1 res2 ... resn.` 

```ABAP
DATA: str1 TYPE string,
      str2 TYPE string,
      str3 TYPE string,
      itab TYPE TABLE OF string,
      text TYPE string.
text = `What a drag it is getting old`.
SPLIT text AT space INTO: str1 str2 str3,TABLE itab. 
```

将字符串的值分配给一内表`SPLIT s_source AT sep INTO TABLE itab.`

```ABAP
DATA : lv_string TYPE string .
TYPES: BEGIN OF ty_string,
    str(25) TYPE c,
  END OF ty_string.
DATA lt_string TYPE TABLE OF ty_string.
DATA wa_string TYPE ty_string.
lv_string = 'SPLIT ME AT SPACE'.
SPLIT lv_string AT ' ' INTO TABLE lt_string .
LOOP AT lt_string INTO wa_string.
  WRITE :/ wa_string-str.
ENDLOOP.
```

#### 9.比较字符串

判断是否包含特定值

- IF field CN '0123456789'.
- IF field CO 'ABCDEFG*' 
- IF field CN 'abcdefg*'
- IF field CN '/' .....

| 表达式 | 描述                                           | 表达式 | 描述                              |
| :----- | :--------------------------------------------- | :----- | :-------------------------------- |
| **CO** | Contains Only(仅包含，A是否仅由B中字符组成)    | **CN** | Contains Not Only (不仅包含)      |
| **CS** | Contains String (包含字符串，A是否包含字符串B) | **NS** | Contains No String (不包含字符串) |
| **CP** | Covers Pattern (包含模式，A是否包含B中的模式)  | **NP** | No Pattern(不包含记号)            |
| **CA** | Contains Any(包含任何，A是否至少包含一个B)     | **NA** | Contains Not Only(不包含任何)     |

#### 10.大小写转换

​	TRANSLATE c TO [UPPER] [LOWER] CASE.：将字符串转换为大|小写

#### 11.字符前拼接空格

在 Grid ALV 上也显示空格：

```ABAP
IF <fw_output>-name2+0(1) eq space AND <fw_output>-name2 IS NOT INITIAL.
  h_white = cl_abap_conv_in_ce=>uccpi( 160 ).
  REPLACE ALL OCCURRENCES OF REGEX '\s' IN <fw_output>-name2 WITH h_white.
ENDIF.
```

#### 12.判断字符是否为纯数字

```ABAP
IF cl_abap_matcher=>matches(
     pattern = '^(-?[1-9]\d*(\.\d*[1-9])?)|(-?0\.\d*[1-9])$'
     text = '文本' ) = abap_true.
    "文本包含全为数字"     
ENDIF.
```

