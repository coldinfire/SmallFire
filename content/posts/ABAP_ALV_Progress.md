---
title: " ALV 添加执行进度功能 "
date: 2020-03-01
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
  - abaputils

---

### 添加进度条功能

为了更加清晰的了解到程序执行进度，可以在程序中添加显示进度条功能。

```ABAP
DATA:BEGIN OF gt_result OCCURS 0,
    sel(1),
    matnr         TYPE mara-matnr,
    zz_edi_grp    TYPE mara-zz_edi_grp,
    werks         TYPE mseg-werks,
    eknam         TYPE eknam,
    maktx         TYPE maktx,
    num           TYPE sy-tabix,
    row           TYPE sy-tabix,
    icon          TYPE icon-id,
    mess          TYPE string,
    flag(1),
  END OF gt_result.
DATA: lt_result LIKE STANDARD TABLE OF gt_result WITH HEADER LINE.  
DATA: l_perc      TYPE int4,
      l_perc_cnt  TYPE int4,
      l_perc_i    TYPE int4,
      l_perc_stxt TYPE string,
      l_sperc(3)  TYPE c.  
READ TABLE gt_result WITH KEY sel = 'X'.
IF sy-subrc <> 0.
  MESSAGE 'Please select at leaset one line.' TYPE 'E' .
ENDIF.  
lt_result[] = gt_result[].
DELETE lt_result[] WHERE sel  <> 'X'.

l_perc = 0.
l_perc_i = 0.
l_perc_cnt = LINES( lt_result ).  "DESCRIBE TABLE itab LINES n "
LOOP AT  lt_result.
  l_perc_i = l_perc_i + 1.
  l_perc_stxt = ''.
  l_perc = l_perc_i * 100 / l_perc_cnt.
  l_sperc = l_perc.
  CONCATENATE 'Processing: ' l_sperc '% …… ' lt_result-matnr '@ plant:' lt_result-werks
		INTO l_perc_stxt.
  CALL FUNCTION 'SAPGUI_PROGRESS_INDICATOR'
    EXPORTING
      percentage = l_perc
      text       = l_perc_stxt.
     ........
ENDLOOPG.
```

