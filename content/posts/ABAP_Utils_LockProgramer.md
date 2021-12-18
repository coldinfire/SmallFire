---
title: " SAP 程序锁定 "
date: 2021-09-07
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils
---



在 SAP 中对于一些重要的程序，可能会要求处理并发的情况，在有用户操作的情况下不允许其他用户进行操作，以此来保证数据及程序的安全。

### 使用技术

`ENQUEUE_ES_PROG` 和 `DEQUEUE_ES_PROG`，这两个 Function 是 SAP 系统存在的用来锁定和解锁程序执行。

```ABAP
REPORT ZPROGLOCK_TEST
DATA: lv_name TYPE progname VALUE sy-cprog.
AT SELECTION-SCREEN.
  PERFORM PROGRAM_LOCK.
FORM PROGRAM_LOCK.
  CALL FUNCTION 'ENQUEUE_ES_PROG'
    EXPORTING
      MODE_TRDIR     = 'E'  "锁条目模式：默认是E锁(独占锁)，可选‘S’锁(共享锁)，‘X’锁(专用锁)"
      NAME           = lv_name  "需要锁定的程序名"
      X_NAME         = ' '  "默认为空,可选"
      _SCOPE         = '2'  "表示锁定范围值，默认为‘2’(在update module内有效);‘1‘(程序内有效);‘3’(全部有效)"
      _WAIT          = ' '  "表示如果对象已经被锁定,是否等待后再尝试加锁."
                            "最大的等待时间有系统参数 ENQUE/DELAY_MAX控制"
      _COLLECT       = ' '      
    EXCEPTIONS
      FOREIGN_LOCK   = 1
      SYSTEM_FAILURE = 2
      OTHERS         = 3.
  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
       WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENDIF.
ENDFORM. 
```

`_COLLECT` 参数：表示是否收集后进行统一提交，COLLECT 是一种缓存与批处理方法，即如果指定了 Collect，加锁信息会放到 Lock Container 中，Lock Container 实际上是一个 Function Group 控制的内存区域。如果程序中加了很多锁，锁信息会先放到内存中，这样可以减少对 SAP 锁管理系统访问。若使 Lock Container 中的锁生效，需执行 FLUSH_ENQUEUE 这个 Function，将锁信息更新到锁管理系统中，此时加锁操作生效。使用函数 RESET_ENQUEUE 可以清除 Lock Container 中的锁信息 。

-  解除锁 DEQUEUE_ES_PROG 的参数与上面的加锁的参数类似。
