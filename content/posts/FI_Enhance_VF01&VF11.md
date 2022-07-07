---
title: " FB02 修改行文本函数 "
date: 2022-03-02
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - FICO
  - Enhance

---

### V60A0001 & RV60AFZC

通过调整 SAP 预制的标准增强点（V60A0001）实现禁止跨月发票冲销需求，V60A0001 的触发点是用户敲下 VF01 或者 VF11 那一刻、但是还未填充 Billing 值前的操作。

通过调整 SAP 预制的标准增强点（RV60AFZC）实现禁止跨月发票开具需求，RV60AFZC 的触发点是 VF01、VF11、VF04 等回车之后填充 Billing 过程中的操作。

VF11 的增强需要使用 V60A0001，开发票的增强使用 RV60AFZC。