---
title: "业务流程"
date: 2018-06-17
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbusiness

---

### PP流程 ###

- MM01(Material)->CS01(BOM)->CR01(WORKCENTERY)->CA01(ROUTING)->MD11(PLANNED ORDER)->
COO1(PRODUCTION ORDERS)也可通过CO41/CO40转换计划单得来.

- COMAC:对生产订单进行可用性检查

- COHVOMPRINT:打印订单

- MB31:通过单号收货

### SD流程 ###

- VK11(PRICECONDITION)->VA21(QUOTATIONORDER)->VA01(CREATE SO)->VA41(CREATE CONTRACT)

- 销售流程：
  - 创建询价单(VA11)->创建报价单(VA21)->创建交货单(VL10)->发货过帐->出具发票->客户余额查询->收款

- V.02:检查不完整性订单
- VA14L:为交货冻结凭证
- VKM1:接触冻结的SO

- 发票(Billing)：
 - VF01(创建出具发票凭证)->VF04(维护发票到期清单)->VF05(出具发票凭证清单)->VF11(取消出具发票凭证).

### MRP流程 ###

- MD61(独立物料计划)->MD01(MRP总计划)->MD03(单项单层计划)->MD05(显示MRP物料清单)->MD14/MD15(生产计划单转采购需求)->ME57(处理采购申请分配VENDOR)->ME59N(采购申请自动生成采购定单)
- 物料成本：
  - CK11N:创建物料成本估算
  - KKPAN:不用数量创建估算
  - CK24:价格更新标记标准价格
  - CK40N:编辑成本核算
- 仓库管理：
  - COOIS:生产订单信息系统
  - MB52/MMBE:查看库存
  - VL02N:向外发货
  - MD04/MD40L:相关MRP

