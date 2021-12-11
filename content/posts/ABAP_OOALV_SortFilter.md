---
title: " OO ALV 排序和过滤 "
date: 2018-07-15
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - OOALV

---

### OO ALV 设置排序条件

可以为 ALV 数据设置排序条件。 初始化排序功能，在方法 SET_TABLE_FOR_FIRST_DISPLAY 中的参数 IT_SORT 添加由系统标准的排序结构 `LVC_T_SORT` 生成的内表来实现。

```ABAP
FORM prepare_sort_table CHANGING pt_sort TYPE lvc_t_sort .
  DATA ls_sort TYPE lvc_s_sort .
  CLEAR: ls_sort,pt_sort.
  ls_sort-spos = '1' .
  ls_sort-fieldname = 'XXXX' .
  ls_sort-up = 'X' . "A to Z"
  ls_sort-down = space .
  APPEND ls_sort TO pt_sort .
  CLEAR ls_sort.
  ls_sort-spos = '2' .
  ls_sort-fieldname = 'XXXX' .
  ls_sort-up = space .
  ls_sort-down = 'X' . "Z to A"
  APPEND ls_sort TO pt_sort .
  CLEAR ls_sort.
ENDFORM. "prepare_sort_table"
```

#### 排序注意问题

- 如果要排序的任何一个字段不在字段目录中，程序会 Dump。
- 当使用 ALV Grid 对数据进行排序时，默认情况下它会垂直合并具有相同内容的字段。 为了避免所有的列都执行默认情况，可以设置布局结构的 `no_merging = 'X'`。 如果只想对某些列禁用合并，设置该列对应的字段目录行的 no_merging 字段即可。

可以分别使用 `GET_SORT_CRITERIA` 和 `SET_SORT_CRITERIA` 方法获取和设置应用的排序标准。

### Filtering 设置过滤条件

过滤和排序的使用类似。 使用过滤条件时，必须填写参照类型 `LVC_T_FILT` 生成的内表。 填充此类型内表类似于填充 RANGES 变量。

然后把这个内表传递给方法 SET_TABLE_FOR_FIRST_DISPLAY 中的参数 IT_FILTER。

```ABAP
FORM prepare_filter_table CHANGING pt_filt TYPE lvc_t_filt .
  DATA ls_filt TYPE lvc_s_filt .
  CLEAR: ls_filt,pt_filt .
  ls_filt-fieldname = 'FLDATE' .
  ls_filt-sign = 'E' .
  ls_filt-option = 'BT' .
  ls_filt-low = '20180101' .
  ls_filt-high = '20181231' .
  APPEND ls_filt TO pt_filt .
  CLEAR ls_filt.
ENDFORM.  " prepare_filter_table "
```

可以分别使用 `GET_FILTER_CRITERIA` 和 `SET_FILTER_CRITERIA` 方法获取和设置应用的过滤条件。

