---
title: "ABAP 程序间调用"
date: 2018-06-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbasis

---

### 同步调用

#### 中断执行

调用程序被打断，当被调用程序执行完毕之后，调用程序继续执行。

- `CALL FUNCTION [function].`
- `SUBMIT [program] AND RETURN.`
- `CALL TRANSACTION [TCode].`

#### CALL FUNCTION [Fucntion]

使用CALL FUNCTION 'AAA'调用FM时，相应的FUNCTION GROUP 被加载到调用程序所在的 internal session。当FM执行完毕后，接着执行调用程序。FUNCTION GROUP 和其 GLOBAL DATA 会一直保存在这个 internal session 直到调用程序结束。当调用程序再次调用这个 FM 的时候，不会再次加载相应的 FUNCTION GROUP。这个 FUNCTON GROUP 的 GLOBAL DATA 和第一次调用它时的内容是一样的。

#### SUBMIT [program] AND RETURN

使用 SUBMIT [program] AND RETURN 或者 CALL TRANSACTION [tcode] 的时候，实际是插入了一个新的internal session，当被调用的程序执行完毕之后，新插入的 internal session 会被删除，继续执行调用程序。可以使用 leave program 语句来结束程序。

- 如果省略 AND RETURN 选项，主调程序的所有数据与所有级别的 List 都会从 internal session 中删除。在被调程序执行完后，会返回到主调程序启动的地方。

- 如果带 AND RETURN 选项，系统将会保持主调程序的所有数，并在被调程序执行完后返回到主调程序调用处（SUBMIT…AND RETURN 语句调用处），然后系统会继续执行主调程序 SUBMIT…AND RETURN 后面的语句。

#### CALL TRANSACTION、LEAVE TO TRANSACTION

1、 如果调用后不需要返回到主调程序，则可以使用下面这种方式：

- LEAVE TO TRANSACTION |()[AND SKIP FIRST SCREEN].

该语句会结束当前主调程序去执行事务码，并且会将主调程序从 internal sessions 中删除，而被调用 Tcode 将会在该 external session 中新开一个 internal session 再运行，并且被调程序执行后，并不会回到主调程序调用处继续往下执行，而是系统返回到区域菜单，从该菜单开始调用堆栈中的原始程序。

2、 如果调用后还要返回到主调程序，则使用下面这种方式：

- SET PARAMETER ID '[Screen Parameter ID]' FIELD [field_value].

- CALL TRANSACTION |()  [AND SKIP FIRST SCREEN] [USING <bdc_tab >].

系统会重新开启一个 internal session, 当被调程序结束后，被调 Tcode 所在的这个 internal session 会被 delete 掉，然后返回到主调程序调用处，继续运行主调程序后面的语句
<bdc_tab> 用在 BDC 调用输入参数传递。

3、 退出程序

- LEAVE PROGRAM.

退出整个程序，并删除所在内部会话、包括加载的程序、实例、数据。

#### 顺序执行

- `SUBMIT [program].`
- `LEAVE TO TRANSACTION [TCode].`

使用 SUBMIT 语句之后，调用程序从所在的 internal session 中被删除了，被调用的程序被加载到这个 internal session. 

使用 `LEAVE TO TRANSACTION <tcode>` 之后，当前 external session 中的所有 internal session 会被删除，并产生一个新的 internal session，被调用的 tcode 会加载到这个新的 internal session 中。特别要注意的是，使用这个语句之后，ABAP MEMORY 会被初始化，意思就是你不可以通过 ABAP MEMORY 向被调用的 tcode 传值。

### 异步调用

```LS
CALL FUNCTION 'AAA' 
  STARTING NEW TASK <taskname> 
  PERFORMING <subroutine> ON END OF TASK
  EXPORTING
 .....
```

使用上面语句之后，AAA 和调用其的程序会并行运行。可以在 [subroutine] 中使用 RECEIVE RESULTS FROM FUNCTION 'AAA' 语句来获得 FUNCTION 运行的结果。值得注意的是，用 STARTING NEW TASK 形式的 FM 的 processing type 必须要是 REMOTE-CAPABLE MODULE.

 

- [ABAP Submit 实现程序间互相调用](https://coldinfire.github.io/2018/ABAP_Submit/)
- [ABAP CALL TRANSACTION 详解](https://coldinfire.github.io/2018/ABAP_BDC/)
- [ABAP CALL FUNCTION 详解](https://coldinfire.github.io/2018/ABAP_RFC/)