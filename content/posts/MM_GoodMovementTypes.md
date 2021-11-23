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

SAP Good Movement Types 使用 3 个数字来标识 SAP Good Movement 的类型。

移动类型参考字段 MSEG-BWART，移动类型参考表T156，文本表是T156T。

### SAP MM中重要的移动类型

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

### SAP 和收货相关的移动类型

| Movement Type | Desc                                                         | 中文描述                              | T-Code        |
| ------------- | ------------------------------------------------------------ | ------------------------------------- | ------------- |
| 101 / 102     | Goods receipt for purchase order / Reversal                  | 采购订单的收货 / 冲销                 | MIGO  / CO11N |
| 103 / 104     | Goods receipt for purchase order to GR blocked stock / Reversal | 采购订单收货到收货的冻结库存 / 冲销   | MIGO          |
| 105 / 106     | Release from the GR blocked stock for the purchase order / Reversal | 从采购订单的收货冻结中释放库存 / 冲销 | MIGO          |
| 121           | Subsequent adjustment for subcontracting                     | 分包的后续调整                        |               |
| 122 / 123     | Return deliveries to vendor /  Reversal                      | 将交货退还给供应商 / 冲销             | MIGO          |
| 124 / 125     | Return delivery to vendor from GR blocked stock / Reversal   | 从收货冻结的库存退货给供应商 / 冲销   | MIGO          |
| 161 / 162     | Returns for purchase order / Reversal                        | 采购订单退货 /  冲销                  |               |

### SAP 和收、发货相关的移动类型

| Movement Type | Desc                                                 | 中文描述                          |
| ------------- | ---------------------------------------------------- | --------------------------------- |
| 201 / 201     | Goods issue / receipt for a cost center              | 有关成本中心的发货 / 收货         |
| 221 / 222     | Goods issue / receipt for a project (WBS)            | 有关项目的发货 / 收货             |
| 231 / 232     | Goods issue / receipt for  sales order               | 有关销售订单的发货 / 收货         |
| 241 / 242     | Goods issue / receipt for  main asset number         | 有关资产的发货 / 收货             |
| 251 / 252     | Goods issue / receipt for sale (without sales order) | 有关销售的发货（无销售订单） MB1A |
| 261 / 262     | Goods issue / receipt for an order                   | 有关订单的发货 / 收货             |
| 281 / 292     | Goods issue / receipt for a network                  | network的发货(PS) / 收货          |
| 291 / 292     | Goods issue / receipt for any account assignment     | 任何科目分配的发货 / 收货         |

### 工厂到工厂、存储地点的转移

- 工厂到工厂的转移 (一步和两步)
- 存储地点转移到其他存储地点，包括 SAP 中检验库存，样品转移和 Block 状态移动

| Movement Type | Desc                                                         | 中文描述                                 |
| ------------- | ------------------------------------------------------------ | ---------------------------------------- |
| 301 / 302     | Plant to plant transfer in one step                          | 工厂到工厂的转移一步完成                 |
| 303 / 304     | Plant to plant transfer in two steps :  stock removal        | 工厂到工厂的转移分为两个步骤：清除库存   |
| 305 / 306     | Plant to plant transfer in two steps :  putaway              | 工厂到工厂的转移分两个步骤：上架         |
| 309 / 310     | Transfer postings from material to material                  | 从物料到物料转移过帐                     |
| 311 / 312     | Transfer of storage location to storage location in one step | 一步将存储位置转移到存储位置             |
| 313 / 314     | Transfer of storage location to storage location in two steps :  stock removal | 分两步将存储地点转移到存储地点：清除库存 |
| 315 / 316     | Transfer of storage location to storage location in two steps :  putaway | 分两步将存储地点转移到存储地点：放置     |
| 321 / 322     | Transfer of inspection stock :  unrestricted-use stock       | 检验库存的转移：非限制使用的库存         |
| 323 / 324     | Transfer of storage location to storage location :  inspection stock | 将存储地点转移到存储地点：检验库存       |
| 325 / 326     | Transfer of storage location to storage location :  blocked stock | 将存储地点转移到存储地点：冻结库存       |
| 331 / 332     | Sample from the inspection stock                             | 检验库存中的样品                         |
| 333 / 334     | Sample from the unrestricted-use stock                       | 非限制使用库存中的样本                   |
| 335 / 336     | Sample from the blocked stock                                | 冻结库存中的样本                         |
| 341 / 342     | Status change of a batch (unrestricted-use to restricted)    | 批次的状态更改（非限制使用到限制）       |
| 343 / 344     | Transfer of blocked stock :  unrestricted-use stock          | 冻结库存的转移：非限制使用的库存         |
| 349 / 350     | Transfer of blocked stock :  inspection stock                | 冻结库存的转移：检验库存                 |
| 351 / 352     | Goods issue for a stock transport order (without shipping)   | 库存运输订单的发货（不发货）             |

### 特殊库存，转移库存和冻结库存退货

| Movement Types | Desc                                                         | 中文描述                                   |
| -------------- | ------------------------------------------------------------ | ------------------------------------------ |
| 411 / 412      | Transfer of special stock to own stock (only for sales order stock) | 特殊库存转为自有库存（仅用于销售订单库存） |
| 413 / 414      | Transfer posting to sales order stock                        | 将过帐转移到销售订单库存                   |
| 451 / 452      | Returns from customer (without shipping)                     | 客户退货（不发货）                         |
| 453 /454       | Transfer of blocked stock returns to unrestricted-use stock  | 将冻结的库存退货转移到非限制使用的库存     |
| 455 / 456      | Returns stock transfer                                       | 返回库存转移                               |
| 457 / 458      | Transfer of blocked stock returns to inspection stock        | 将冻结的退货库存转移到检验库存             |
| 459 / 460      | Transfer of blocked stock returns to blocked stock           | 将冻结的库存退货转移到冻结的库存           |

### 不同类型的收货处理

| Movement Types | Desc                                                         | 中文描述                             |
| -------------- | ------------------------------------------------------------ | ------------------------------------ |
| 501 / 502      | Goods receipt without purchase order :  unrestricted-use stock | 无采购订单的收货：非限制使用的库存   |
| 503 / 504      | Goods receipt without purchase order :  stock in quality inspection | 无采购订单的收货：检验库存           |
| 505 / 506      | Goods receipt without purchase order :  blocked stock        | 无采购订单的收货：冻结库存           |
| 521 / 522      | Goods receipt without order :  unrestricted-use stock        | 无订单收货：非限制使用的库存         |
| 523 / 524      | Goods receipt without order :  inspection stock              | 无订单收货：检验库存                 |
| 525 / 525      | Goods receipt without order :  blocked stock                 | 无订单收货：冻结库存                 |
| 531 / 532      | Goods receipt of by-products from order                      | 订单中副产品的收货                   |
| 541 / 542      | Transfer of unrestricted-use stock to subcontracting stock   | 将非限制使用的库存转移到分包库存     |
| 543 / 544      | Consumption from subcontracting stock                        | 分包库存的消耗                       |
| 545 /546       | Goods receipt of by-products from subcontracting             | 分包的副产品收货                     |
| 551 / 552      | Scrapping from unrestricted-use stock                        | 从非限制使用的库存中报废             |
| 553 / 554      | Scrapping from inspection stock                              | 从检验库存中报废                     |
| 555 / 556      | Scrapping from blocked stock                                 | 从冻结的库存中报废                   |
| 557 / 558      | Issue from stock in transit (adjustment posting)             | 从运输中的库存发货（调整过帐）       |
| 561 / 562      | Initial entry of stock balances :  unrestricted-use stock    | 库存余额的初始输入：非限制使用的库存 |
| 563 / 564      | Initial entry of stock balances :  quality inspection        | 库存余额的初始输入：质量检验         |
| 565 / 566      | Initial entry of stock balances :  blocked stock             | 库存余额的初始输入：冻结库存         |
| 581 / 582      | Goods receipt of a by-product from network                   | 来自network的副产品收货              |

### 与运输相关的SAP移动类型

主要功能是：

- 商品包装
- 货物的交货处理
- 物品拣选
- 货物通讯
- 计划和监控运输
- 发货过账

| Movement Types | Desc                                                         | 中文描述                                           |
| -------------- | ------------------------------------------------------------ | -------------------------------------------------- |
| 601            | Goods issue for delivery                                     | 基于发货的发货                                     |
| 603            | Goods issue for a stock transport order (shipping) with additional item | 带有其他物料的库存运输订单（装运）的发货           |
| 605            | Goods receipt for a stock transport order (shipping) with additional item | 带有其他项目的库存运输订单（装运）的收货           |
| 621            | Transfer of unrestricted-use stock :  returnable packaging with customer (shipping) | 无限制使用的库存的转移：与客户可回收的包装（装运） |
| 623            | Goods issue from returnable packaging with customer (shipping) | 与客户进行可回收包装的发货（装运）                 |
| 631            | Transfer of unrestricted-use stock :  consignment stock at customer (shipping) | 非限制使用库存的转移：客户的寄售库存（装运）       |
| 633            | Goods issue from consignment stock at customer (shipping)    | 客户寄售库存中的发货（装运）                       |
| 641            | Goods issue for a stock transport order (shipping)           | 库存运输订单的发货（装运）                         |
| 643            | Goods issue for a cross-company-code stock transport order   | 跨公司代码库存运输订单的发货                       |
| 645            | Goods issue for a cross-company-code stock transport order performed in one step (shipping) | 一步执行的跨公司代码库存运输订单的发货（装运）     |
| 647            | Goods issue for a stock transport order performed in one step (shipping) | 一步执行库存运输订单的发货（装运）                 |
| 651            | Returns from customer (shipping)                             | 客户退货(运输)                                     |
| 653            | Returns from customer (shipping) to unrestricted-use stock   | 从客户（装运）到非限制使用库存的退货               |
| 655            | Returns from customer (shipping) to inspection stock         | 从客户（装运）到检验库存的退货                     |
| 657            | Returns from customer (shipping) to blocked stock            | 从客户（装运）到冻结库存的退货                     |
| 661            | Returns to vendor using shipping                             | 使用运输退货给供应商                               |
| 673            | Returns for a cross-company-code stock transport order (shipping) | 跨公司代码库存运输订单（装运）的退货               |
| 675            | Returns for a cross-company-code stock transport order (shipping) performed in one step | 一步执行的跨公司代码库存运输订单（装运）的退货     |

### SAP 中的库存差异移动类型

在SPA MM(物料管理)和SAP WM(仓库管理)中，与库存差异相关的移动类型。

| Movement Types | Desc                                                     | 中文描述                          |
| -------------- | -------------------------------------------------------- | --------------------------------- |
| 701            | Inventory difference in unrestricted-use stock           | 非限制使用库存中的库存差异        |
| 702            | Inventory difference in quality inspection stock (MM-IM) | 质量检验库存中的库存差异（MM-IM） |
| 707            | Inventory difference in blocked stock                    | 冻结库存中的库存差异              |
| 711            | Inventory difference in unrestricted-use stock (LE-WM)   | 非限制使用库存（LE-WM）的库存差异 |
| 713            | Inventory difference in quality inspection stock (MM-IM) | 质量检验库存中的库存差异（MM-IM） |
| 715            | Inventory difference for returns                         | 退货的库存差异                    |
| 717            | Inventory difference in blocked stock (LE-WM)            | 冻结库存的库存差异（LE-WM）       |

