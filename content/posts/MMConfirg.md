---
title: "MM 配置流程"
date: 2019-03-04
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM
---



#### MM 模块主要配置流程

```JS
1. 创建工厂、公司代码                    22. 批量导入物料主数据（BAPI）
2. 创建库存地点                         23. 创建供应商
3. 创建采购组织                         24. 创建采购信息记录（Info Recode）
4. 创建采购组                           25. 维护物料管理的自动记账
5. MRP Controller                      26. 将存货科目设置为只能自动记账
6. 分配工厂到公司                       27. 设置采购价格差异容差限制
7. 分配采购组织到公司                    28. 设置收货的容差限制
8. 分配工厂到采购组织                    29. 设置发票冻结的容差限制
9. 定义物料                             30. 激活项目金额检查
10. 定义计划边界码                      31. 运行MRP
11. 物料管理设置-库存预留                32. 创建采购申请
12. 自动创建库存地点                    33. 创建采购订单
13. 税务代码缺省值                      34. 采购订单收货
14. 物料管理的初始期间                  35. 采购发票输入
15. 物料需求计划相关的参数维护           36. 发票冻结
16. 激活物料需求计划                    37. 在MIR4中显示发票和会计凭证
17. 检查MRP相关参数维护(主要看01范围)
18. 定义物料类型的属性
19. 定义评估控制方式
20. 定义评估分组
21. 定义评估类
```
