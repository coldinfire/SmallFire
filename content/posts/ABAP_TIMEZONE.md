---
title: "不同时区日期转换"
date: 2018-12-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils
---

时区表：TTZZ

### 获取用户时区

Function：TZON_GET_USER_TIMEZONE

### 根据时区得到时间戳

FUNCTION：IB_CONVERT_INTO_TIMESTAMP

### 根据时间戳和时区，获取当地日期时间

FUNCTION：IB_CONVERT_FROM_TIMESTAMP

