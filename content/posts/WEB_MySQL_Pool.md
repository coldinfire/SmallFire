---
title: " 数据库连接池 "
date: 2017-12-07
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - MySQL
---

### 连接池的应用

每次访问数据库都会创建一个连接，初始化连接，关闭连接，会走访问数据库的所有操作，由于每次创建初始化连接和关闭连接会花费大量的时间，会导致整个程序效率低下，因此为解决在访问数据库时要不断的初始化连接和关闭连接带来的时间消耗问题，我们采取了连接池的方式。