---
title: " ABAP 程序锁定 "
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

### 使用锁对象

程序锁于字段锁都是有相同的地方，都需要在程序中先锁，之后要解锁。

`ENQUEUE_ES_PROG` 和 `DEQUEUE_ES_PROG`，这两个 Function 是 SAP 系统存在的用来锁定和解锁程序执行。

#### 加锁

ENQUEUE_ES_PROG：该锁会保持直到 DEQUEUE_ES_PROG 函数调用，或者事务（程序）执行完毕（执行完毕后会隐式调用DEQUEUE_ALL）才会释放。

```ABAP
REPORT ZPROGLOCK_TEST
DATA: lv_name TYPE progname.
lv_name = sy-cprog.
AT SELECTION-SCREEN.
  PERFORM PROGRAM_LOCK.
FORM PROGRAM_LOCK.
  CALL FUNCTION 'ENQUEUE_ES_PROG'
    EXPORTING
      MODE_TRDIR     = 'E'      "锁条目模式：默认是E锁(独占锁)，可选‘S’锁(共享锁)，‘X’锁(专用锁)"
      NAME           = lv_name  "需要锁定的程序名"
      X_NAME         = ' '      "默认为空,可选"
      _SCOPE         = '2'      "表示锁定范围值，默认为‘2’(在update module内有效);‘1‘(程序内有效);‘3’(全部有效)"
      _WAIT          = ' '      "表示如果对象已经被锁定,是否等待后再尝试加锁."
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

#### 解锁

解除锁 DEQUEUE_ES_PROG 的参数与上面的加锁的参数类似。该函数释放由 ENQUEUE_ES_PROG.设置的程序锁。

```ABAP
CALL FUNCTION 'DEQUEUE_ES_PROG'
* EXPORTING
*   MODE_TRDIR       = 'E'
*   NAME             =
*   X_NAME           = ' '
*   _SCOPE           = '3'
*   _SYNCHRON        = ' '
*   _COLLECT         = ' '.
```

### 使用注意问题

1、 ENQUEUE_ES_PROG 函数只是尝试去锁定，如果锁已经被其他程序获取，并不会阻塞，要在调用后通过 sy-subrc 来判断是否获取成功。可以在循环里通过 WAIT UP TO xx SECONDS. 语句来等待锁被获取到。

2、 ABAP工作台开发程序时，不能同时编辑同一个程序，第一个打开程序的用户会上程序锁，程序锁可以使用**SM12**来查看当前的程序锁。该程序在处于编辑状态下，再运行时，也会报错：这是因为程序处于编辑状态获取的锁与在程序里使用代码获取的锁是同一个锁，所以上面代码在获取锁时出错，如果处于只读状态时，则可以正常运行，但也只能一个窗口运行，不能有两个或多个只读状态下的该程序运行。

3、 程序在锁定后，解锁的动作不是必须的，程序退出或者完成后会自动解锁。

4、 程序的锁定只对前台程序有用，如果运行的是后台服务，则需要其他的解决方案。可以考虑使用如下方法：一种方式是将应用服务器配置为同一程序只允许同时运行一个JOB；第二种方式是通过判断表 TBTCO 的 JOB 状态字段来解决。

5、 锁定的功能函数在不同的事件中运行效果不同，请根据具体的需求来确定该在何种事件后锁定。