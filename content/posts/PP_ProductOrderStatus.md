---
title: " SAP 生产订单状态 "
date: 2019-08-25
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - PP

---

### SAP 系统的常见订单状态如下

- **CRTD (创建)：** 标识生产订单刚刚创建，此时禁止做后续发料和报工确认等操作；
- **PREL (部分下达)：** 当生产订单部分下达时，如仅下达部分工序时出现此状态；
- **REL    (已下达)：** 当生产任务已经明确可下发生产时，将生产订单转换为已下达状态，这个状态后可以继续后续业务操作，如打印订单、发料、报完工等操作；
- **MANC (未检查物料可用性)：** 生产订单未进行零部件物料的可用性检查；
- **SETC (结算规则维护)：** 生产订单已维护结算规则；
- **NTUP(日期未更新)：** 生产订单日期人工更改后未重新进行日期计划；
- **MSPT (物料短缺)：** 生产订单的零部件物料在进行可用性检查后发现存在短缺；
- **MACM (已承诺的物料)：** 生产订单的零部件物料在进行可用性检查后确认完全可用；
- **GMPS (已过帐的货物移动)：** 生产订单已经进行过发料；
- **PCNF (部分确认)：** 生产订单只进行了部分完工确认，比如说订单需求 10 个，只进行了 5 个生产，或者订单有 2 道工序，只完成了第一道工序；
- **CNF (已确认) ：** 生产订单已全部完工确认；
- **PDLV (部分交货)：** 生产订单只有部分产品入库；
- **DLV (交货) ：** 生产订单已经完全交货入库，这意味着生产订单业务全部完成。CO 看到 DLV 状态或 TECO 状态时将对订单进行完工结算。
- **VCAL (差异计算) ：** 生产订单进行过差异运算；
- **TECO (技术完成) ：** 在生产过程中，会出现订单未完成但是不再继续生产的情况，这时就可以打上技术完结标识，此时订单对零部件的需求同时删除。在很多项目中，为了简便处理，会对所有完成的订单进行技术完结处理 (注：不再继续生产也是一种完成)。CO 看到 DLV 状态或 TECO 状态时将对订单进行完工结算；
- **RESA (进行结果分析)：** 生产订单进行过结算；
- **CLSD (关闭)：** 生产订单做账务关闭，不允许对订单发生任何过账，通常情况下，财务月末对订单进行结算后，如果确认不会再有追加发料等业务发生，则应该将订单进行关闭；
- **DLID (删除) ：** 对生产订单做删除标识，数据仍然存在数据库中，状态可恢复。如果想彻底删除，需对订单进行归档处理。

#### 随着业务的变化生产订单的状态也随之变化

| 业务进展       | 对应状态             | 描述                                     |
| -------------- | -------------------- | ---------------------------------------- |
| 创建生产订单   | CTRD                 | 建立                                     |
| 下达生产订单   | REL                  | 已释放                                   |
| 对生产订单投料 | REL、GMPS            | 已释放、已过账的货物移动                 |
| 完全报工       | REL、GMPS、CNF       | 已释放、已过账的货物移动、已确认         |
| 完全入库       | REL、GMPS、CNF、DLV  | 已释放、已过账的货物移动、已确认、交货   |
| 技术关闭       | TECO、GMPS、CNF、DLV | 技术关闭、已过账的货物移动、已确认、交货 |

### 函数使用

**STATUS_READ**：获取输入工单的状态

```ABAP
DATA:l_aufnr type aufnr.
DATA:it_jest type standard table jest.
DATA:it_tj02t type standard table tj02t.
CALL FUNCTION 'STATUS_READ'     
  EXPORTING
    OBJNR            = l_aufnr
    ONLY_ACTIVE      = 'X'
  TABLES
    STATUS           = it_jest
  EXCEPTIONS
    OBJECT_NOT_FOUND = 1
    others           = 2.
IF SY-SUBRC <> 0.
ENDIF.
" To get the texts of statuses "
IF NOT it_jest IS INITIAL.
  SELECT istat txt04 FROM TJ02T
    INTO TABLE it_tj02t
    FOR ALL ENTRIES IN it_jest
   WHERE istat = it_jest-stat
     AND spras = sy-langu.
ENDIF.
```

**STATUS_TEXT_EDIT**：获取和 CO03 显示的状态一样的数据

```ABAP
CALL FUNCTION 'STATUS_TEXT_EDIT'
  EXPORTING
    flg_user_stat = 'X'
    objnr = caufvd-objnr
    only_active = 'X'
    spras = sy-langu
  IMPORTING
    anw_stat_existing = caufvd-astex
    line = t_hresb-sttxt "Status Result"
    user_line = t_hresb-asttx
  EXCEPTIONS
    object_not_found = 01.
```

**STATUS_CHECK**：检查生产订单状态，看是否有某种状态

```ABAP
CALL FUNCTION 'STATUS_CHECK'
  EXPORTING
    OBJNR = CAUFVD-OBJNR
    STATUS = STATUS_PRINT
  EXCEPTIONS
    OBJECT_NOT_FOUND = 01
    STATUS_NOT_ACTIVE = 02.
```

### 直接从表获取生产订单状态

1、根据生产订单从 AUFK 获取 Object Number（AUFK-OBJNR）

2、根据 AUFK-OBJNR = JEST-OBJNR 获取对象状态（JEST-STAT，JEST-INACT）

3、根据 JEST-STAT 在 TJ02T 中获取对应的对象状态描述（TJ02T-TXT04）

4、根据 JEST-STAT 在 TJ02 中获取对象状态的是否显示状态（TJ02-NODIS）

![Order Status](/images/PP/OrderStatus.jpg)

#### DEMO

```ABAP
DATA:jest type standard table jest.
SELECT SINGLE * FROM aufk WHERE aufnr = it_table-aufnr. 
" 判定工单状态 "
" 判定工单是否 RELASE "
CLEAR: jest.
SELECT SINGLE * FROM jest
  WHERE objnr = aufk-objnr
  AND ( stat = 'I0002' OR  " RELEASE "
	    stat = 'I0042')    " Partial RELEASE "
  AND inact = space.
IF sy-subrc <> 0.
  CONTINUE.
ENDIF.
" 判定工单状态 "
CLEAR: jest.
SELECT SINGLE * FROM jest
  WHERE objnr = aufk-objnr
  AND ( stat = 'I0045' OR " TECO "
        stat = 'I0013' OR " DELETE "
        stat = 'I0076' OR " DELETE FLAG "
        stat = 'I0046' OR " CLSD "
        stat = 'I0012 ')  " DLV "
  AND inact = space.
IF sy-subrc = 0.
  CONTINUE.
ENDIF.
```
