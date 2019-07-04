---
title: "SAP memory使用"
date: 2019-03-04
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abap
  - utils

---

### SAP memory和ABAP memory ###
1. 使用的语句不同

    SAP memory使用SET/GET parameters；

      - SPA：SET PARAMETER ID 'MAT' FIELD p_matnr.

      - GPA：GET PARAMETER ID 'MAT' FIELD p_matnr.

    ABAP Memory使用EXPORT 和IMPORT :

       - EXPORT p_matnr = p_matnr TO MEMORY ID 'ZTESTMAT'.

       - IMPORT p_matnr = p_matnr FROM MEMORY ID 'ZTESTMAT'

     FREE MEMORY ID 'ZTESTMAT'.         清空指定的ABAPmemory

     FREE MEMORY.                                    清空externalsession内的所有ABAPmemory

2. 共享范围不同

    SAP memory用于所有external session间.

     ABAP memory用于同一个external session的internal session间。

3. 作用范围不同（就是生存期）

    SAP memory在登陆到退出这期间一直有效。

    ABAP memory只在同一个session(window) 内有效。

4. Dialog获取SAPMemory方式

    在dialog 屏幕上建一个input field, 然后Parameter ID属性与'SAP_MMR'绑定,并打上2个勾。

    Set Parameter: 允许将屏幕值返回给SAP Memory (类似于执行SET PARAMETER ID语句)

    Get Parameter: 允许读取SAP Memory的值并默认显示(类似于执行GET PARAMETER ID语句).