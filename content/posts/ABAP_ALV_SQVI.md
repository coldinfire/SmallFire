---
title: "SQVI 创建一张简易的报表"
date: 2019-08-28
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

### SAP提供的SQVI功能，用以快速自定义一个自己需要的报表

通过该方式创建的报表，只能在SQVI下查，谁的用户创建的就只能谁看，其他用户无法共享。

1. 创建，并选择数据源为：表连接 

   ![创建 Quick View](/images/ABAP/SQVI_1.png)
   
2. 插入需要查数的表

   ![插入表](/images/ABAP/SQVI_2.png)

3. 接着将表的主键进行连接

   ![主键连接](/images/ABAP/SQVI_3.png)

4. 点击返回按钮，系统将进入报表显示及筛选字段的选择界面，将报表需要显示的字段筛选出来

   ![报表显示字段设置](/images/ABAP/SQVI_4.png)

5. 完成报表中字段的选择后，点击“选择字段”。在“选择字段”中，我们添加筛选界面的筛选字段。

   ![报表选择字段设置](/images/ABAP/SQVI_6.png)

6. “筛选字段”与“报表显示字段”选择完毕后，我们可以点击执行。

   ![点击执行](/images/ABAP/SQVI_5.png)

7. 创建并展示出这一报表。

   ![报表界面](/images/ABAP/SQVI_7.png)

8. 可以根据实际业务，输入筛选条件，并查出需要的数据。

   ![报表结果](/images/ABAP/SQVI_8.png)

