---
title: " TCode 可用BAPI查找 "
date: 2019-11-22
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - BAPI

---

### 查找 T-Code 可用 BAPI

1、MM02，先查看事物码系统状态

![MM02](/images/ABAP/SAP_BAPI_0.png)

2、双击 Program(Screen)，进入程序界面

![Status](/images/ABAP/SAP_BAPI_1.png)

3、点击 Display Object List，转到 SE80

![SE80](/images/ABAP/SAP_BAPI_2.png)

4、点击 Display Superord Object List，转到程序所属 Package

![Package](/images/ABAP/SAP_BAPI_3.png)

5、SE80 展开 PACKAGE 下的 Business Engineering -> Business Object Types，根据业务需求选择对应的 Item

![Business Object Type](/images/ABAP/SAP_BAPI_4.png)

6、双击打开选择的 Business Object，展开 Methods 根据描述选择需要的功能双击

![Methods](/images/ABAP/SAP_BAPI_5.png)

7、在弹出的屏幕中，选择 ABAP 页，Name 中显示的既是对应的 BAPI 名称

![ABAP](/images/ABAP/SAP_BAPI_6.png)

- 页签下面勾选的 **API Function**，说明找到的 BAPI 就是修改 Master Data 的，如果勾选的是 **Function module**，说明是通过函数实现的。

### 通过 BAPI 浏览器或者业务对象资源库浏览器查找 BAPI

#### 使用 Tcode

- BAPI 浏览器：BAPI
- 业务对象浏览器：SWO3

#### 通过目录找到对应层级

![SE80](/images/ABAP/SAP_BAPI_7.png)

![SE80](/images/ABAP/SAP_BAPI_8.png)

#### 双击 Business Object，显示对象；展开方法栏，双击对应方法

![Methods](/images/ABAP/SAP_BAPI_5.png)

#### 转到 ABAP 页面，名称即为 BAPI 名

![ABAP](/images/ABAP/SAP_BAPI_6.png)