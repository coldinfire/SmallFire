---
title: "MM常用TCode"
date: 2019-03-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM
---

#### MM 常用Tcode：

| Table            | Desc                                                         | Table            | Desc                                            |
| ---------------- | ------------------------------------------------------------ | ---------------- | :---------------------------------------------- |
| MM01/02/03/04/06 | 物料：创建/修改/显示/显示修改/删除标记                       | MK01/02/03/05/06 | 供应商：创建/修改/显示/冻结/删除标记            |
| ME01/02/0M       | 货源清单：创建/查看/物料与供应商关系                         | ME11/12/13/15/1L | 采购信息记录：创建/修改/显示/删除/供应商        |
| ME41/42/43       | 询价单：创建/修改/查询                                       | ME47/48/49       | 报价：维护/显示/对比                            |
| ME28/29          | 采购审批/采购审批(单个)                                      | ME9F             | 消息输出：采购订单                              |
| ME21N/22N/23N/2L | 采购订单：创建/修改/查看/根据供应商查看                      | ME51N/52N/53N/5A | 采购申请:创建/修改/查看/批量查看                |
| ME57/ME58        | 分配和处理申请/订单：分配的请求                              | MM60             | 物料清单查询                                    |
| MIGO             | 收货                                                         | MIRO             | 采购开票                                        |
| MMBE             | 库存总览                                                     | MB51             | 物料凭证清单                                    |
| MB52             | 物料仓库库存                                                 | MBST             | 冲销物料凭证                                    |
| MB11             | 货物移动                                                     | MB03/MBSM        | 查看物料凭证/根据物料查物料凭证                 |
| MB1A             | 消耗类出货，一般都会生产会计凭证(发料)                       | MB01             | 按采购订单收货                                  |
| MB1B             | 物料的转储 (外协发料、库间转储)(转储 / 调拨)                 | MB31             | 按订单收货 (过账)                               |
| MB1C             | 生成收货凭证，采购订单、生产订单之外的其他物料收货的事务代码 (收货) | COGI             | 货物移动错误处理 (处理生产过程中货物移动的错误) |
| MB21/22/23/24    | 预留：创建/修改/显示/删除                                    | MKVZ             | 供应商清单                                      |
| MMAM/MM50        | 更改物料类型/扩充物料视图                                    | OX09             | 库位维护                                        |
| COOIS            | 查看工单信息                                                 | MD03/04/05       | 手动 MRP/MRP/MRP清单                            |
| VF01/02/03/04/11 | 发票凭证：创建/修改/显示/到期/取消                           | CO03             | 显示生产订单                                    |