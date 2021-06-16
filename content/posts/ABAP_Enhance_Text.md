---
title: "Text Enhancements"
date: 2020-12-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - Enhance

---

### 文本增强

用文本增强修改SAP标准屏幕中的字段名称，如修改Internal Order 的 End of Work。

#### Step 1 : 找到对应的Data Element

事务 KO03 进入 Internal Order 主数据显示界面，选择 General data 页签，将鼠标光标放在需要更改的字段上，然后按 F1 键，出现一个对话框后，点击Technical Info 按钮，然后复制 Data Element：AUFUSER8。如下图：

![Data Element](/images/ABAP/ABAP_Text_Enhance_00.png)

#### Step 2 : TCODE - CMOD 管理增强

通过 TCODE:CMOD (管理SAP增强)，点击菜单栏中“ Goto – Text enhancements - Keywords – Change ”。如下图所示：

![CMOD](/images/ABAP/ABAP_Text_Enhance_01.png)

输入 Step 1 中复制的 Data Element。

![Data Element](/images/ABAP/ABAP_Text_Enhance_02.png)

修改成自己想要显示的内容。

![Change Keywords](/images/ABAP/ABAP_Text_Enhance_03.png)