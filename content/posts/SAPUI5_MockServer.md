---
title: " SAPUI5 Mock Server "
date: 2019-04-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - SAP Fiori

tags: 
  - sapui5

---

## Mock server

在开发过程中，通过使用模拟服务器的方法方便测试，SAPUI5 将模拟服务器称为 mock server.mock server 的基本功能是模拟 oData 数据的提供者，截获应用程序对服务器端的 http 或 https 请求，并传回模拟请求的回应，可以降低与真实后端的耦合。