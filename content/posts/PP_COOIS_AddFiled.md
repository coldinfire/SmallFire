---
title: "COOIS 添加字段"
date: 2021-04-15
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - PP
---



参考文档 :[Add New or Custom Fields to COOIS Output](https://blogs.sap.com/2015/10/07/add-new-or-custom-fields-to-coois-output/)

### COOIS 添加并显示自定义字段

要在订单信息系统的 ALV OUTPUT 中增强/添加/显示其他字段或列，而 COOIS ALV 输出中的标准系统中未提供这些字段或列。

USING BADI `WORKORDER_INFOSYSTEM` ：METHOD – `TABLES_MODIFY_LAY`

#### Step 1：添加字段结构

Go to SE11, display the structure **IOHEADER** ，Click on **Append Structure** button。

添加自己需要添加的字段到 append structure。

#### Step 2：配置显示界面

Use transaction SE38 to execute the report **RCNCT000.**

![SE38 RCNCT000](/images/PP/COOIS_3.png)

Input **IOHEADER** in the field string name and **RCNHEAD** in the include name as shown below and click on Execute ：

![RCNCT000 IOHEADER RCNHEAD](/images/PP/COOIS_4.png)

In the following screen, click on the **GENERATE** Button:

![Result](/images/PP/COOIS_5.png)

Use transaction SE38 to execute the report **RCOTX000** .

![SE38 RCOTX000](/images/PP/COOIS_0.png)

Input the name of the structure **IOHEADER** in the field string name Choose F8 to execute the report.

![RCOTX000 IOHEADER](/images/PP/COOIS_1.png)

Below is the screen print of the report RCOTX000 output. You will be able to identify your fields in the below screen (fields per this requirement are not shown below though)

![RCOTX000 IOHEADER](/images/PP/COOIS_2.png)

Click “SAVE” and you will be taken to back to the selection screen.

#### Step 3：实现 BADI 并在方法中获取字段值

实现 BAPI : WORKORDER_INFOSYSTEM

转到事务 SE19，创建 BADI  WORKORDER_INFOSYSTEM 的实现。 对于将新/客户字段添加到COOIS 输出的要求，这是适当的 BADI。

![WORKORDER_INFOSYSTEM](/images/PP/COOIS_6.png)

方法中添加逻辑：TABLES_MODIFY_LAY

双击方法名称 TABLES_MODIFY_LAY 继续执行业务逻辑，以将数据填充到必填字段中。

![TABLES_MODIFY_LAY](/images/PP/COOIS_7.png)