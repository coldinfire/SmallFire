---
title: " ABAP 事件终止 "
date: 2018-06-09
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 事件终止

#### RETURN

用来退出当前执行的程序块，而不仅仅是退出循环。如FORM，METHOD，报表事件块

#### STOP 

- INITIALIZATION 中 STOP 会导致跳转到 AT SELECTION-SCREEN OUTPUT 事件块

- 如果 STOP 在 AT SELECTION-SCREEN OUTPUT 块里，则只是退出当前块，STOP 后面语句不执行而已

- 如果 STOP 在循环中，FORM、METHOD 直接从被调用的点退出所在事件块

#### EXIT

INITIALIZATION 中的 EXIT 会跳转到 AT SELECTION-SCREEN OUTPUT 事件

- 如果 EXIT 在 AT SELECTION-SCREEN OUTPUT 块里，则只是退出当前块，EXIT 后面语句不执行而已

- 如果 EXIT 在循环中，只是跳出当前循环而已；循环之外的模块则与 RETURN 类似

#### CHECK

- CHECK 只是跳出当前事件块，继续下个事件块的处理，相当于方法的 RETURN

- CHECK 在循环中，只是跳出循环（DO,WHILE,LOOP）类似于 CONTINUE，在循环外则跳出当前执行的程序块

#### LEAVE 

- `LEAVE PROGRAM.`：退出整个程序

- `LEAVE TO TRANSACTION <tcode>.`：跳转到另外的 TCode

- `LEAVE LIST-PROCESSING.`：从 List Processor 回到 Dialog processor

- `LEAVE TO LIST-PROCESSING. `：控制权从 Dialog processor 转交到 List processor

- LEAVE {SCREEN | {TO SCREEN dynnr}}

#### REJECT

用在逻辑数据库 GET event blocks 中，可以从循环或则一个 FORM 中直接跳出所在的 GET 事件块。