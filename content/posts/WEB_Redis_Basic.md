---
title: " Redis 基本使用 "
date: 2018-03-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - Redis

---

### Redis 简介

redis 时一款高性能的非关系型数据库(NOSQL)。

- 数据的存储格式是key,value形式，且数据之间没有关联关系
- 数据存储在内存中

关系型数据库和 NoSQL 数据库是互补的关系。一般将数据存储在关系型数据库中，在 nosql 数据库中备份存储关系型数据库的数据。

### Redis 命令操作

#### Redis 数据结构

redis 存储的是 key,value 形式的数据，key 都是字符串，value 有5中不同的数据结构。

- 字符串类型：string
- 哈希类型：hash，map 格式
- 列表类型：list，linkedlist 格式
- 集合类型：set
- 有序集合类型：sortedset