---
title: " BAPI和Tcode执行之间的差异 "
date: 2021-08-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - BAPI

---

### BAPI 和 Tcode 执行之间的差异

BAPI 和前台 Tcode 操作大部分是相同的，但是仍然会有一些差异。当使用 BAPI 进行操作时，如果遇到问题，可能需要查找一些资料信息或者 Debug 进行参数排查。

#### Subsequent Debit/Credit

无法使用 BAPI 创建后续借记或贷记凭证。

#### Error message F5A252

BAPI_INCOMINGINVOICE_CREATE

- F5A252 - Caution: Cash discount 1 already expired for net posting or max. 

Discount - is in use(折扣 - 正在使用中)，仅适用于前台事物码。 现金折扣支票是一项财务功能，不用作 MM-IV 功能。 在 MIRO 系统中，通过财务程序进行检查并输出可定制的消息 F5A252（标准消息类型：警告）。 这意味着 EDI、ERS 和 BAPI 流程系统不会运行检查。

可能的解决方案：

您可以在 BADI 功能模块中开发此检查功能：MRMBADI_PAYMENT_TERMS。 Function Module 将在这些发票流程中执行。

#### Withholding tax zero value

BAPI_INCOMINGINVOICE_CREATE

如果在预扣税基(Withholding tax base)字段中输入了具有预扣税证书（100% 免税）和零 (0.00) 的供应商。 

- MIRO 中，在字段税基中输入 0.00 时，没有错误消息并且发票已成功过帐。

- BAPI 显示错误消息 M8412，但它仍然成功过帐发票。
  - M8412 - Withholding tax code requires you to enter a base amount

在 MIRO 中，您还可以手动更改数据，在 BAPI 过程中这是不可能的。

可能的解决方案：

   a) 不使用预扣税码过帐：那么 FI 凭证中的基本金额将为零，但税码也将为空。

   b) 使用 MIRO 而不是 BAPI 过账。 在 MIRO 中，您可以手动输入 0,00 的预扣税基数，这稍后也会出现在 FI 文档中。

#### 预扣税 (Withholding tax)

BAPI_INCOMINGINVOICE_PARK

BAPI_INCOMINGINVOICE_CREATE

尽管在供应商主数据中填写了信息，但如果供应商需要缴纳预扣税，则您必须填写 BAPI 中的 WITHTAXDATA 表。

#### 典型预扣税 (Classic Withholding tax)

如果你想使用 BAPI_INCOMINGINVOICE_CREATE 创建带有经典预扣税的发票，则必须实现 BADI -> MRM_WT_SPLIT_UPDATE。 不可以将 BAPI 接口表 WITHTAXDATA 与经典预扣税一起使用。 

BAPI 不支持经典预扣税，因为 WITHTAXDATA 结构仅用于扩展预扣税。SAP Note 830717 说明：这是支持的，但只能通过 BADI 实现。

#### 重复发票检查 (Duplicate invoice check)

BAPI_INCOMINGINVOICE_CREATE

在前台 Tcode 执行时，重复发票检查可确保系统中没有输入重复发票。 此功能在 BAPI 中不可用。 重复发票检查被视为“纯在线功能”； 由于 BAPI 是一个自动过程，因此它们不执行此检查。

可能的解决方案：

考虑在客户自己的程序中添加 FM -> MRM_DUPLICATE_INVOICE_CHECK，在调用 FM BAPI_INCOMINGINVOICE_CREATE 之前或在 BAPI 提交之前（调用 FM BAPI_TRANSACTION_COMMIT）。

#### 带有采购订单参考的发票预制凭证 (Invoice park with Purchase order reference)

使用 BAPI，如果基于 GR 的 Invoice 处于 active 状态并且没有已过帐的 GR，则无法暂存发票，请参阅 include -> LMRM_BAPIF23 的 FORM：itemdata_check 中完成的检查。 

在 BAPI 中，不可能像在前台 MIR7 中那样将参考采购订单保存在附加选择表中。

#### 付款条件 (Terms of payment)

BAPI_INCOMINGINVOICE_PARK

如果发票在没有采购订单参考的情况下使用此 BAPI 创建预制凭证，则付款条件取自供应商主数据。 但如果稍后添加采购订单参考行项目，付款条款将不会从采购订单更新。

如果发票在没有采购订单参考的情况下在 MIR7 中创建预制凭证，那么如果添加新的采购订单参考行，付款条款将从采购订单中更新。

#### 一次性供应商银行数据 (One-time vendor bank data)

BAPI_INCOMINGINVOICE_POST

对于一次性供应商 (CPD)，没有 PO 参考，预订到总账科目，系统不会给出填写银行详细信息的窗口和错误。 MIR4 为一次性供应商提供错误 F5 - 732。 在MM-invoice online 过程中，一次性供应商（CPD）数据检查，这些数据是否正确并在发票抬头中完成，已经通过财务端的财务程序完成。 这仅适用于前台 Tcode 操作。