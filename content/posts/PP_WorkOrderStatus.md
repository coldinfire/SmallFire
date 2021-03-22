---
title: " 获取工单状态 "
date: 2021-03-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - utils
---



### gsdfasf

工单状态获取

```ABAP
SELECT SINGLE kdauf kdpos objnr auart zz_ci_flag 
  INTO (l_kdauf, l_kdpos, l_objnr, l_auart, l_zz_ci_flag)
    FROM aufk
    WHERE aufnr = i_aufnr.
```

获取工单状态

```ABAP
SELECT SINGLE * INTO ls_jest
  FROM jest
  WHERE objnr = l_objnr
  AND stat = 'I0045'
  AND inact = ''.
IF sy-subrc = 0.
ELSE.
```

