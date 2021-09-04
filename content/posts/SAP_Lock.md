---
title: "SAP 锁机制"
date: 2018-07-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### SAP 为什么要设置锁

#### 1.保持数据的一致性

同时多个用户操作同一数据，防止数据出现不一致性而采用了锁机制。一般 sap 会在操作数据前设置锁，防止第二个用户进行修改操作，当操作结束后系统在释放锁。

#### 2.仅仅用 Database 锁是不够的

数据库管理系统物理锁定了要修改的行记录，其他用户要等到数据库锁释放才能访问这个记录。

在 SAP 系统中，当一个新屏幕显示的时候会释放掉 Database 锁，因为屏幕的改变会触发一个隐式的 DB COMMIT。如果数据是从好几个屏幕收集来的话，而且在这段时间内这些数据会分别被锁定，仅仅用 Database 锁就不够了。

SAP 系统在应用服务器层面有一个全局的 LOCK TABLE，可以用来设置逻辑锁来锁定相关的表条目，并有 ENQUEUE 工作进程来管理这些锁。SAP 锁是一种逻辑意义上的锁，有可能你锁定的表条目在 DATABASE 上根本就不存在。

### 锁对象和其对应的 FM

当激活锁对象的时候，系统会自动创建两个 FM，`ENQUEUE_<锁对象名>` 和 `DEQUEUE_<锁对象名>`，分别用来锁定和解锁。

- 通用数据库表锁函数：ENQUEUE_E_TABLE、DEQUEUE_E_TABLE、DEQUEUE_ALL

在 SE11 里创建锁对象，自定义的锁对象都必须以 EZ 或者 EY 开头来命名。一个锁对象里只包含一个 PRIMARY TABLE，可以包含若干个 SECONDARY TABLE，锁的模式有三种：E(Exclusive lock)，S(Shared lock)，X(Exclusive but not cumulative lock)。LOCK PARAMETERS 里填写你要根据哪些字段来锁定表条目。

- 模式 E：当更改数据的时候设置为此模式。

- 模式 S：本身不需要更改数据，但是希望显示的数据不被别人更改。

- 模式 X：和 E 类似，但是不允许累加，完全独占。

如果你在一个程序里成功对一个锁对象加锁之后，如果模式为 E，其他用户不能再对这个锁对象加 E、X、S 模式的任意一种锁；

如果你在一个程序里成功对一个锁对象加锁之后，如果模式为 X，其他用户不能再对这个锁对象加 E、X、S 模式的任意一种锁；

如果你在一个程序里成功对一个锁对象加锁之后，如果模式为 S，其他用户不能再对这个锁对象加 E、X 模式的锁，但是可以加 S 模式的锁；

如果你在一个程序里成功对一个锁对象加锁之后，如果模式为 E，在这个程序，你还可以再对这个锁对象加 E、S 模式的锁，X 模式的不可以。

如果你在一个程序里成功对一个锁对象加锁之后，如果模式为 X，在这个程序，你不可以再对这个锁对象加 E、X、S 模式的锁。

如果你在一个程序里成功对一个锁对象加锁之后，如果模式为 S，在这个程序，你还可以再对这个锁对象加 S 模式的锁，如果没有别的用户对其加 S 模式的锁，那么你还可以对其加 E 模式的锁。X 模式的不可以。

### 锁定和解锁

- `ENQUEUE_<lock object的名字> 对象` EZZSOPR0032 要求的锁定
- `DEQUEUE_<lock object的名字>` 释放对象 EZZSOPR0032 的锁定

当用逻辑锁来锁定表条目的时候，系统会自动向 LOCK TABLE 中写入记录。

当调用设置锁的 FM 时，LOCK PARAMETERS 如果没有指明，系统会锁定整个表。当然，LOCK PARAMETER：CLIENT 有点特殊，如果不指定，默认是 SY-MANDT；如果指定相应的 CLIENT，会锁定对应 CLIENT 上的相应的表记录；如果设置为 SPACE，则锁定涉及所有的 CLIENT。

当逻辑锁设置失败后，一般会有两种例外。一个是 EXCEPTION：FOREIGN_LOCK，意思是已经被锁定了；另一个是 EXCEPTION：SYSTEM_FAILURE。

有些情况下，程序中设置成功的逻辑锁会隐式的自己解锁。比如说程序结束发生的时候（MESSAGE TYPE 为 A 或者 X 的时候），使用语句 LEAVE PROGRAM，LEAVE TO TRANSACTION，或者在命令行输入 /n 回车以后。

在程序的结束可以用 DEQUEUE FUNCTION MODULE 来解锁（当然如果你不写这个，程序结束的时候也会自动的解锁），这个时候，系统会自动从 LOCK TABLE 把相应的记录删除。使用 DEQUEUE FUNCTION MODULE 来解锁的时候，不会产生 EXCEPTION。要解开你在程序中创建的所有的逻辑锁，可以用 FM：DEQUEUE_ALL.

### 上锁的一般步骤

先上锁，上锁成功之后，从数据库取数据，然后更改数据，接着更新到数据库，最后解锁。按照这个步骤，才能保证更改完全运行在锁的保护机制下。

### 相关表和相关函数

#### 相关表

DD25L：组合标题 (方式，MC 目标，锁定目标)(纪录了锁主表)

DD25T：视图和锁定对象的短文本

DD26S：视图的基本表和外来码关系 (纪录了所有和锁相关的表)

DD27S：合计 (视图，MC 对象，锁定对象) 字段

#### 相关函数

`RS_DD_ENQU_EDIT`

`RS_DD_ENQU_ADD`

`DEQUEUE_ALL`：释放当前 LUW 的所有锁

### 通过断点找程序所用到的锁

用 SE38 打开程序 LSENAF01，并定位到 send_enqueue 子过程，在该过程中的任一语句设置断点。完成断点设置后，则去执行标准 T-Code, 系统就会在程序调用锁时自动停止在断点处，这时就可以通过调用堆栈获取加锁函数 (ENQUEUE_XXXXXX), 其中 "XXXXXX" 就是锁名称，可以通过 SE11 查看锁信息。 

### 如何对表加锁

```JS
CALL FUNCTION 'ENQUEUE_E_TABLE'
  EXPORTING
    MODE_RSTABLE ='E'
    TABNAME ='Table Name'
    *VARKEY =
	*X_TABNAME = ' '
	*X_VARKEY = ' '
	*_SCOPE = '2'
	*_WAIT = ' '
	*_COLLECT = ' '
  EXCEPTIONS
    FOREIGN_LOCK = 1
    SYSTEM_FAILURE = 2
    OTHERS = 3.
IF sy-subrc = 0.
  WRITE: 'Lock table successfully!'.
ELSE.
  WRITE: 'Failed'.
ENDIF.
```

