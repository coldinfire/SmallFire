---
title: " MM 委外业务(外发加工) "
date: 2019-09-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

### 委外加工

是指由本公司提供材料（可能是部分）给外包商进行加工，完工后收回成品并向委外加工商支付加工费的业务方式。

1.创建委外加工订单

- 订单类型：NB


- ITEM类别：L


- 物料最好有BOM，然后展BOM，带出原材料需求


- MARK：并不是 BOM 中的所有材料都会带出，如果在 BOM item 的 status 中设置了 Mat. Provision indicator 为 L，即由供应商提供，则不会带出


2.发料给委外加工商：

- 方法一：transfer，t-code MB1B，Movement type 541，指定 PO number


- 方法二：ME2O（报表）


- 方法三：t-code MIGO，输入 Material，委外 a 加工商


- 方法四：从另外的供应商直接提供材料给委外加工商，向供应商下采购订单，在 delivery adress 中勾上 SC vendor，输入委外加工商，然后会自动带出委外加工商的地址，收货时库存也会自动记到委外加工库存（101收货，特殊库存标记为O）


3.加工完成收货：同时做两个动作

- 收货 (movement：101)


- 原材料消耗 (movement：543)


- t-code：MIGO，输入参照 PO number（可以修改原材料消耗的数量）；如果采用的是移动平均价，则成品的价值为为外加工费和材料消耗费之和。


4.后续调整：参照PO进行调整，例如增加物料消耗等。