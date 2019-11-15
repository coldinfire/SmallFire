---
title: "ABAP字符串处理"
date: 2019-09-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils
  - abapbasis

---





​		字符串的处理在程序中的使用十分常见，在这里结合自己日常的使用对ABAP的字符串常用操作进行总结，以便后续使用。

**1.获取字符串长度**

- `var1  = strlen(str);`将str作为字符数据处理，计算出其字符长度

  ```JS
  DATA: l_len type i,
  	    str1(20) VALUE '1234'.
  	l_len = strlen(str1).
  WRITE: / len.  
  ```

**2.拼接字符串**

- `CONCATENATE <str1> ... <strn> into <var> [SEPARATED BY <s>][RESPECTING BLANKS]`
- 将多个字符串拼接到指定的变量Var中，注意Var的长度
- C,D,N,T类型的前导空格会保留，尾部空格会去掉，对String类型的所有空格都会保留
- `[SEPARATED BY <s>]`:根据该间隔符号(S)进行拼接(SPACE)
- respecting：针对C,D,N,T数据，表示尾部空格会保留

**3.压缩字符串**

- `CONDENCE <str> [NO-GAPS]`:去除字段str中的前后空格，并将字段中多个空格使用一个空格替换；如果加上NO-GAPS则去除所有空格。

**4.截取字符串**

- subtext = str+0(4)：从最左边取出4个字符
- subtext = str+2(4)：从第2个字符开始取出4个字符
- str+0(1) = 'X'：变更指定位置的值

**5.查找字符串**

- `SEARCH <var> FOR <str> <options>.`:在var中搜索子字符串，成功SY-SUBRC=0
- str:可以使用正则表达式，也可以使用普通字符串

**6.替换字符串**

- `REPLACE <str1> WITH <str2> INTO <var> [LENGTH <l>].`
- 将str1在var中的字段替换为str2
- 如果指定长度，则只将str1中指定长度的字符替换为str2中的内容
- 如果替换后内容总长度超过var定义的长度，会出现截断现象

**7.移动字符串内容**

- `SHIFT <var> [BY <n> PLACES] [<mode>].`

- 将var移动n个位置，

- MODE:LEFT(默认),RIGHT,CIRCULAR

  

- `SHIFT <var> LEFT DELETING LEADING <str>.`

- `SHIFT <var> RIGHT DELETING TRAILING <str>.`

- 如果左边第一个字符串或则右边第一个字符串出现在str中，将var移动相应的位置

**8.比较字符串**

判断是否包含特定值

- IF field CN '0123456789'.
- IF field CN 'ABCDEFG*' 
- IF field CN 'abcdefg*'
- IF field CN '/' .....

| **CN：Contains Not Only (包含，不仅包含)** | **CO：Contains Only（仅包含）**           |
| ------------------------------------------ | :---------------------------------------- |
| **CS：Contains String (包含字符串)**       | **NS：Contains No String (不包含字符串)** |
| **NP：No Pattern （不包含记号）**          | **NA：Contains Not Only(不包含任何)**     |
| **CA：Contains Any（包含任何）**           | **CP：Covers Pattern (包含记号)**         |

9.**拆分字符串**：

​	SPLIT dobj AT sep INTO {res1 res2......resn}. ：将字符串的值分配给具体变量

​	SPLIT s_source AT sep INTO TABLE itab：将字符串的值分配给一内表。

**10.大小写转换：**

​	TRANSLATE c TO UPPER|LOWER CASE.：将字符串转换为大|小写

**11.字符前拼接空格**

​	在Grid ALV上也显示空格：

```JS
IF <fw_output>-name2+0(1) eq space AND <fw_output>-name2 IS NOT INITIAL.
  h_white = cl_abap_conv_in_ce=>uccpi( 160 ).
  REPLACE ALL OCCURRENCES OF REGEX '\s' IN <fw_output>-name2 WITH h_white.
ENDIF.
```

