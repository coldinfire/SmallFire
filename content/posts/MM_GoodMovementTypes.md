---
title: " SAP 移动类型详解 "
date: 2020-05-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

### SAP 移动类型

SAP 使用 3 个数字来标识 Good Movement Type。

移动类型参考字段 MSEG-BWART，移动类型参考表 T156、T156T。

### MM 中常用的移动类型

| Movement Type | Desc                                                         | 中文描述                                | T-Code     |
| ------------- | ------------------------------------------------------------ | --------------------------------------- | ---------- |
| 101           | Goods receipt for purchase order or order                    | 采购订单或订单的收货                    | MIGO/CO11N |
| 103           | Goods receipt for purchase order into GR blocked stock       | 采购订单收货到收货冻结库存中            | MIGO       |
| 201           | SAP MM Goods issue for a cost center                         | 成本中心的 SAP MM 发货                  | MB1A       |
| 261           | Goods issue for an order                                     | 订单的发货                              | MIGO/MB1A  |
| 301           | Transfer posting plant to plant in one step                  | 一步将过帐工厂转移到工厂                |            |
| 305           | Transfer posting plant to plant in two steps                 | 分两步将过帐工厂转移到工厂              |            |
| 311           | Transfer posting storage location to storage location in one step | SAP MM 一步将过帐存储地点转移到存储地点 |            |
| 313           | Stock transfer storage location to storage location in two steps | 分两步将库存转移到存储地点              |            |

### 收货相关的移动类型

| Movement Type | Desc                                                         | 中文描述                                  | T-Code        |
| ------------- | ------------------------------------------------------------ | ----------------------------------------- | ------------- |
| 101 / 102     | Goods receipt for purchase order / Reversal                  | 采购订单或生产订单的收货/ 冲销            | MIGO  / CO11N |
| 103 / 104     | Goods receipt for purchase order to GR blocked stock / Reversal | 采购订单收货到GR的冻结库存 / 冲销         | MIGO          |
| 105 / 106     | Release from the GR blocked stock for the purchase order / Reversal | 采购订单从GR冻结区释放到非限制库存 / 冲销 | MIGO          |
| 121           | Subsequent adjustment for subcontracting                     | 分包的后续调整                            |               |
| 122 / 123     | Return deliveries to vendor /  Reversal                      | 将交货退还给供应商 / 冲销                 | MIGO          |
| 124 / 125     | Return delivery to vendor from GR blocked stock / Reversal   | 从GR冻结的库存退货给供应商 / 冲销         | MIGO          |
| 161 / 162     | Returns for purchase order / Reversal                        | 采购订单退货 /  冲销                      |               |

### 收、发货相关的移动类型

| Movement Type | Desc                                                      | 中文描述                              |
| ------------- | --------------------------------------------------------- | ------------------------------------- |
| 201 / 201     | Goods issue / receipt for a cost center                   | 有关成本中心的发货 / 收货             |
| 221 / 222     | Goods issue / receipt for a project (WBS)                 | 有关项目的发货 / 收货                 |
| 231 / 232     | Goods issue / receipt for  sales order (without Shipping) | 有关销售订单的发货（不带装运） / 收货 |
| 241 / 242     | Goods issue / receipt for  main asset number              | 有关资产的发货 / 收货                 |
| 251 / 252     | Goods issue / receipt for sale (without customer order)   | 有关销售的发货（无客户订单） MB1A     |
| 261 / 262     | Goods issue / receipt for an order                        | 有关订单的发货 / 收货                 |
| 281 / 292     | Goods issue / receipt for a network                       | network 的发货(PS) / 收货             |
| 291 / 292     | Goods issue / receipt for any account assignment          | 任何科目分配的发货 / 收货             |

### 工厂到工厂、存储地点的转移

- 工厂到工厂的转移 (一步和两步)
- 存储地点转移到其他存储地点，包括 SAP 中检验库存，样品转移和 Block 状态移动

| Movement Type | Desc                                                         | 中文描述                                                     |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 301 / 302     | Plant to plant transfer in one step                          | 工厂到工厂的转移一步完成                                     |
| 303 / 304     | Plant to plant transfer in two steps :  stock removal        | 工厂到工厂的转移分为两个步骤：清除库存                       |
| 305 / 306     | Plant to plant transfer in two steps :  putaway              | 工厂到工厂的转移分两个步骤：上架                             |
| 309 / 310     | Transfer postings from material to material                  | 从物料到物料转移过帐。前提：两物料的库存计量单位必须一致     |
| 311 / 312     | Transfer of storage location to storage location in one step | 一步将存储位置转移到存储位置                                 |
| 313 / 314     | Transfer of storage location to storage location in two steps :  stock removal | 分两步将存储地点转移到存储地点：清除库存                     |
| 315 / 316     | Transfer of storage location to storage location in two steps :  putaway | 分两步将存储地点转移到存储地点：放置                         |
| 321 / 322     | Transfer posting stock in quality inspection - unrestricted-use stock | 质量检验库存到非限制的转帐                                   |
| 323 / 324     | Transfer of storage location to storage location :  inspection stock | 质检库存从库存地点到库存地点的转帐                           |
| 325 / 326     | Transfer of storage location to storage location :  blocked stock | 冻结库存从库存地点到库存地点的转帐                           |
| 331 / 332     | Sample from the inspection stock                             | 对质检库存抽样提取                                           |
| 333 / 334     | Sample from the unrestricted-use stock                       | 对非限制库存抽样提取                                         |
| 335 / 336     | Sample from the blocked stock                                | 对冻结库存抽样提取                                           |
| 340           | Revaluation of batch                                         | 批次重估，可以更改批次的评估类型 (MSC2N)                     |
| 341 / 342     | Status change of a batch (unrestricted-use to restricted)    | 批次的状态更改（非限制使用到限制）                           |
| 343 / 344     | Transfer of blocked stock :  unrestricted-use stock          | 从冻结库存到非限制使用的转帐                                 |
| 349 / 350     | Transfer of blocked stock :  inspection stock                | 从冻结库存到质检库存的转帐                                   |
| 351 / 352     | Goods issue for a stock transport order (without shipping)   | 对库存转储订单(STO)发货，只用于在不带装运的交货单的情况下进行的发货过帐 |

### 特殊库存，转移库存和冻结库存退货

| Movement Types | Desc                                                         | 中文描述                                                     |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 411 / 412      | Transfer of special stock to own stock                       | 特殊库存（E、Q、K）转为自有库存，相应的特殊库存标识必须联合移动类型一起使用 |
| 413 / 414      | Transfer posting to sales order stock                        | 库存地点到销售订单的转帐                                     |
| 415 / 416      | Transfer posting to project stock                            | 库存地点到项目库存的转帐                                     |
| 451 / 452      | Returns from customer (without shipping)                     | 客户退货到冻结库存(不带装运)                                 |
| 453 /454       | Transfer of blocked stock returns to unrestricted-use stock  | 退货冻结库存到非限制使用库存的转帐                           |
| 455 / 456      | Transfer posting storage location to storage location - blocked stock returns | 退货冻结库存从库存地点到库存地点的转帐                       |
| 457 / 458      | Transfer of blocked stock returns to inspection stock        | 从退货冻结库存到质检库存的转帐                               |
| 459 / 460      | Transfer of blocked stock returns to blocked stock           | 从退货冻结库存到冻结库存的转帐                               |

### 不同类型的收货处理

| Movement Types | Desc                                                         | 中文描述                           |
| -------------- | ------------------------------------------------------------ | ---------------------------------- |
| 501 / 502      | Goods receipt without purchase order :  unrestricted-use stock | 无采购订单到非限制使用库存的收货   |
| 503 / 504      | Goods receipt without purchase order :  stock in quality inspection | 无采购订单到质检库存的收货         |
| 505 / 506      | Goods receipt without purchase order :  blocked stock        | 无采购订单到冻结库存的收货         |
| 511            | Free-of-charge delivery from vendor                          | 供应商免费交货                     |
| 521 / 522      | Goods receipt without order :  unrestricted-use stock        | 无生产订单到非限制使用库存的收货   |
| 523 / 524      | Goods receipt without order :  inspection stock              | 无生产订单到质量检验库存的收货     |
| 525 / 525      | Goods receipt without order :  blocked stock                 | 无生产订单到冻结库存的收货         |
| 531 / 532      | Goods receipt of by-products from order                      | 订单中副产品到非限制使用库存的收货 |
| 541 / 542      | Transfer of unrestricted-use stock to subcontracting stock   | 从非限制自有库存到分包库存的转帐   |
| 543 / 544      | Consumption from subcontracting stock                        | 分包库存的消耗                     |
| 545 /546       | Goods receipt of by-products from subcontracting             | 分包中的副产品收货                 |
| 551 / 552      | Scrapping from unrestricted-use stock                        | 从非限制使用的库存中报废（发货）   |
| 553 / 554      | Scrapping from inspection stock                              | 从检验库存中报废（发货）           |
| 555 / 556      | Scrapping from blocked stock                                 | 从冻结的库存中报废（发货）         |
| 557 / 558      | Issue from stock in transit (adjustment posting)             | 从运输中的库存发货（调整过帐）     |
| 561 / 562      | Initial entry of stock balances :  unrestricted-use stock    | 期初库存分录（到非限制使用库存）   |
| 563 / 564      | Initial entry of stock balances :  quality inspection        | 期初库存分录（到质检库存）         |
| 565 / 566      | Initial entry of stock balances :  blocked stock             | 期初库存分录（到冻结库存）         |
| 571 / 572      | Goods receipt for assembly order to unrestricted-use         | 装配订单到非限制使用库存的收货     |
| 573 / 574      | Goods receipt for assembly order to quality inspection       | 装配订单到质检库存的收货           |
| 575 / 576      | Goods receipt for assembly order to blocked stock            | 装配订单到冻结库存的收货           |
| 581 / 582      | Goods receipt of a by-product from network                   | 来自network的副产品收货            |

### 与运输相关的移动类型

主要功能是：

- 商品包装
- 货物的交货处理
- 物品拣选
- 货物通讯
- 计划和监控运输
- 发货过账

| Movement Types | Desc                                                         | 中文描述                                           |
| -------------- | ------------------------------------------------------------ | -------------------------------------------------- |
| 601            | Goods issue for delivery (Shipping)                          | 基于交货的发货(装运)                               |
| 603            | Goods issue for a stock transport order (shipping) with additional item | 为带附加项目的库存转储订单发货(带装运)             |
| 605            | Goods receipt for a stock transport order (shipping) with additional item | 为带附加项目的库存转储订单收货(带装运)             |
| 621            | Transfer of unrestricted-use stock :  returnable packaging with customer (shipping) | 从非限制自有库存到客户可退回包装库存的转帐(带装运) |
| 623            | Goods issue from returnable packaging with customer (shipping) | 从客户可退回包装库存发货/借出(带装运)              |
| 631            | Transfer of unrestricted-use stock :  consignment stock at customer (shipping) | 从非限制自有库存到客户代销库存的转帐(带装运)       |
| 633            | Goods issue from consignment stock at customer (shipping)    | 从客户代销库存发货/消耗(带装运)                    |
| 641            | Goods issue for a stock transport order (shipping)           | 为库存转储订单发货(带装运)                         |
| 643            | Goods issue for a cross-company-code stock transport order   | 为跨公司库存转储订单发货(带装运)                   |
| 645            | Goods issue for a cross-company-code stock transport order performed in one step (shipping) | 为一步式跨公司库存转储订单发货(带装运)             |
| 647            | Goods issue for a stock transport order performed in one step (shipping) | 为一步式工厂间库存转储订单发货(带装运)             |
| 651            | Returns from customer (shipping)                             | 客户退货到退货冻结库存(带装运)                     |
| 653            | Returns from customer (shipping) to unrestricted-use stock   | 客户退货到非限制使用库存(带装运)                   |
| 655            | Returns from customer (shipping) to inspection stock         | 客户退货到质检库存(带装运)                         |
| 657            | Returns from customer (shipping) to blocked stock            | 客户退货到冻结库存(带装运)                         |
| 661            | Returns to vendor using shipping                             | 通过装运退货给供应商                               |
| 671            | Returns for stock transport order via Shipping               | 通过装运对库存转储订单退货                         |
| 673            | Returns for a cross-company-code stock transport order (shipping) | 对跨公司库存转储订单退货(带装运)                   |
| 675            | Returns for a cross-company-code stock transport order (shipping) performed in one step | 对一步式跨公司库存转储订单退货(带装运)             |
| 677            | Returns for stock transport order in one step (Shipping)     | 对一步式库存转储订单退货(带装运)                   |

### SAP 中的库存差异移动类型

在SPA MM(物料管理)和SAP WM(仓库管理)中，与库存差异相关的移动类型。

- 盘盈 = 收货
- 盘亏 = 发货

| Movement Types | Desc                                                     | 中文描述                  |
| -------------- | -------------------------------------------------------- | ------------------------- |
| 701            | Inventory difference in unrestricted-use stock (MM-IM)   | IM非限制使用库存盘点差异  |
| 702            | Inventory difference in quality inspection stock (MM-IM) | IM 质检库存盘点差异       |
| 707            | Inventory difference in blocked stock(MM-IM)             | IM 冻结库存盘点差异       |
| 711            | Inventory difference in unrestricted-use stock (LE-WM)   | WM 非限制使用库存盘点差异 |
| 713            | Inventory difference in quality inspection stock (LE-WM) | WM 质检库存盘点差异       |
| 715            | Inventory difference in block stock returns (LE-WM)      | WM 退货冻结库存盘点差异   |
| 717            | Inventory difference in blocked stock (LE-WM)            | WM 冻结库存盘点差异       |



#### 参考

- [https://blog.csdn.net/kangliujie/article/details/74990211](https://blog.csdn.net/kangliujie/article/details/74990211)

