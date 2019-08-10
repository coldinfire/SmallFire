---
title: "供应商主数据创建"
date: 2019-05-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM
---



1.Define vendor account groups : OBD3

- SPRO > IMG > Financial accounting > Accounts Receivable and Accounts Payable > Vendor Accounts > Master data > preparation for creating vendor master data > Define Account groups with screen layout(Vendors)


​    <1.Enter Account group Code and Name.

​    <2.Choose: Company code data and select Account management under group.

​    <3.Select Reconciliation account（对账账户） as **required entry**

![Vendor Accout](/images/MMVendorData/VendorAccout.png)

![Vendor Accout](/images/MMVendorData/Details1.png)

![Vendor Accout](/images/MMVendorData/ReconciliationAcc.png)

2.Maintain number ranges for vendor accounts : XKN1

- SPRO > IMG > Financial accounting > Accounts Received and Accounts Payable > Vendor Accounts > Master data > Preparation for creation vendor master data > Create number range for vendor account

![Intervals](/images/MMVendorData/Intervals.png)

![Intervals Details](/images/MMVendorData/IntervalsDetails.png)

- NO：确定供应商账号范围
- EXT：如果需要外部给号，则该位置打钩

3.Assign number ranges to vendor accounts ：OBAS

- SPRO > IMG > Financial Account > Accounts Receivable and Account Payable > Vendor Accounts > Master Data > Assign number ranges to vendor account groups

![Assign](/images/MMVendorData/AssignNR_Vendor.png)

4.create sundry credit account : FS00

- SPRO > IMG > Financial accounting > G/L Accounting > GL Accounts > Master data > Preparations > GL Account Creation and processing > Edit G/L Account


Edit G/L Account Centrally:

- Enter G/L account（总账账户）、Update Company Code、Click on create icon


Account group：P&L statement(损益表)/Balance sheet (资产负债表)、Enter short text,G/L acct Long Text

Next:Control Data

Account control in company code : Update Account currency、Reconciliation account type(Vendor):对账账户类型,杂项债权人创建GL账户，需要选择Vendor

 Account management :

- Open item management / Line item display、Update Sort key


Create/Bank/interest:

- Enter Field status group（对账账户字段状态组）


5.Tolerance group for vendors/Customers : OBA3  <容差组>

- Vendors :  IMG > Financial accounting > accounts receivable and accounts payable > Business transactons > outgoing payments > manual outgoing payments > Define Tolerances


- Customer : IMG > Financial account > accounts receivable and accounts payable > Business transactions > incoming payments > Manual incoming payments > Define Tolerance

![Tolerance](/images/MMVendorData/Tolerances.png)

​	Company code、Tolerance group、Permitted Payment differences (允许的付款差异）

6.Vendor payment terms (付款条件) ：CP03,OBB8

- CP03:Create payment terms   for both customers and vendors


- OBB8:Maintain items of payment


- SPRO > IMG > Financial accounting > Accounts receivable and accounts payable > Business transactions > Incoming invoices/Credit memos > Maintain terms of payment

  ![Payment Terms](/images/MMVendorData/PaymentTerms.png)

- Payt terms:Key              

- Sales text:Desc      

- Own exp:自定义内容，会覆盖系统生成数据

- Custerm&Vendor   Block key:选择适当的标记以阻止某些活动：支付冻结等

- Default for baseline date:选择基准日期计算项   
- Percentage:折扣百分比 
- No.of days:有效天数
- Fixed date:不使用基线日期，输入折扣结束的月份日期

7.Create vendor master data ： XK01

- SPRO > IMG > Logistics > MM > Purchasing > Master Data > Vendor > Central

​    Basic Inof: Title,Name,Search term Street Address,Language

![Basic Info](/images/MMVendorData/BasicInfo.png)

​	Tax categories  :  VAT Reg.NO.--供应商的增值税登记号    

![Tax Categories](/images/MMVendorData/TaxCategories.png)

​	Bank Details:供应商的银行详细信息

![Bank Details](/images/MMVendorData/BankDetails.png)

​	Account info: 对应的账户信息、排序键

![Account info](/images/MMVendorData/AccountInfo.png)

​	Payment Data：付款条件、Chk double inv、Payment methods

![Payment Data](/images/MMVendorData/PaymentData.png)

Purchasing Org Data:

- <1.Enter order currency,terms of payment and incoterms

- <2.Enter appropriate schema group,vendor

- <3.Select GR-based Inv.Verfication if invoice verfication is done after GR

- <4.Select Src.Based inv. verf if invoice verfication after service entry

- <5.Enter the partners for the appropriate partner functions

8.Automatic payment program configuration : FBZP

- SPRO > IMG > Financial Accounting > Accounts receivables and accounts payable > Business Transactions > Outgoing Payments > Automatic Outgoing Payments > Payment Method/Band Selection for Payment Program

![Payment Details](/images/MMVendorData/PaymentPro.png)

All Company Codes:

​      Company Code,Pay company code,Pyt meth suppl,Max.cash discount

![Company Code](/images/MMVendorData/CompanyCode.png)

Paying Company Codes:

​        Enter Paying Company code.             

​		Maintain the incoming or outgoing payments.

​        NO exchange rate diff : APP不会生产汇率过账

​        NO Exch Rate Diffs:可以使用一次付款，结算具有相同参考的发票和贷项通知单

​        Separate payment for each ref:使用汇票付款请求，支票/汇票 程序

​        Bill/exch pymnt: 检查时要保留汇票的设置

![Payment Company Code](/images/MMVendorData/PaymentCompanyCode.png)

Payment methods in country:

![Payment Methods in country](/images/MMVendorData/PaymentMethods.png)

Payment methods in Company code:

![Payment Methods in Company Code](/images/MMVendorData/PaymentMethods2.png)

Bank Determination: Select the Paying Company

![Bank Determination](/images/MMVendorData/BankDetermination.png)

9.Interest calculation configuration(利息计算)

​      Interest calculation on account balances  AND Interest calculation on areas

<1.定义利息计算程序   :  OB46

![OB46](/images/MMVendorData/OB46.png)

<2.准备账户余额利息计算  : OBAA

![OBAA](/images/MMVendorData/OBAA.png)

<3.定义参考利率   : OBAC

![OBAC](/images/MMVendorData/OBAC.png)

<4.定义基于时间的条款  :  OB81

![OB81](/images/MMVendorData/OB81.png)

<5.输入利息值  : OB83

![OB83](/images/MMVendorData/OB83.png)

<6.创建总账科目    :  FS00

​	为定期贷款、支付利息、收到的利息、贷款创建总账科目

<7.准备总账科目余额利息计算配置程序  ： OBV2

![OBV2](/images/MMVendorData/OBV2.png)

