---
title: "ABAP程序间调用"
date: 2018-06-05T17:20:58+08:00
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abap

---

### 同步调用

在一个程序中调用其他ABAP程序。有两种方式

#### 中断执行

调用程序被打断，当被调用程序执行完毕之后，调用程序继续执行。

- CALL FUNCTION [function]

- SUBMIT [program] AND RETURN

- CALL TRANSACTION [TCode]

​    使用CALL FUNCTION 'AAA'调用FM时，相应的FUNCTION GROUP 被加载到调用程序所在的 internal session。当FM执行完毕后，接着执行调用程序。FUNCTION GROUP 和其 GLOBAL DATA 会一直保存在这个 internal session 直到调用程序结束。当调用程序再次调用这个 FM 的时候，不会再次加载相应的 FUNCTION GROUP。这个 FUNCTON GROUP 的 GLOBAL DATA 和第一次调用它时的内容是一样的。

    使用 SUBMIT [program] AND RETURN 或者 CALL TRANSACTION [tcode] 的时候，实际是插入了一个新的 internal session，当被调用的程序执行完毕之后，新插入的 internal session 会被删除，继续执行调用程序。可以使用 leave program 语句来结束程序。

#### 顺序执行

- SUBMIT [program]
- LEAVE TO TRANSACTION [TCode]

​    使用 SUBMIT 语句之后，调用程序从所在的 internal session 中被删除了，被调用的程序被加载到这个 internal session. 

​    使用 LEAVE TO TRANSACTION <tcode> 之后，当前 external session 中的所有 internal session 会被删除，并产生一个新的 internal session，被调用的 tcode 会加载到这个新的 internal session 中。特别要注意的是，使用这个语句之后，ABAP MEMORY 会被初始化，意思就是你不可以通过 ABAP MEMORY 向被调用的 tcode 传值。

### 异步调用

```LS
CALL FUNCTION 'AAA' 
  STARTING NEW TASK <taskname> 
  PERFORMING <subroutine> ON END OF TASK
  EXPORTING
 .....
```

​    使用上面语句之后，AAA 和调用其的程序会并行运行。可以在 [subroutine] 中使用 RECEIVE RESULTS FROM FUNCTION 'AAA' 语句来获得 FUNCTION 运行的结果。值得注意的是，用 STARTING NEW TASK 形式的 FM 的 processing type 必须要是 REMOTE-CAPABLE MODULE.

 