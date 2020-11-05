---
title: "SAP Webservice 使用"
date: 2019-02-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils
---



### SAP Webservice

### SAP 调用 Webservice

a. 获取 webservice wsdl,

b. 生成 webservice

c.LPCONFIG 创建 Logical Port, 保存并激活

d. 编写 webserive 程序，CALL 方法调用 webservice，

e. 测试程序；

### SAP 发布 Webservice 供其他系统调用

a. 服务配置，- sicf

b. 函数创建，- SE37：YRFC_TEST_WEB 

c. 生产 webservice, 函数 -> 设置

d. 进行 IE 发布 SOAMANAGER 

e. 外部语言进行调用测试，