---
title: " SAP Authorization table "
date: 2020-03-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - saputils

---

### 相关权限分配的Table

| Table            | Desc                                  | Table         | Decs                                       |
| ---------------- | ------------------------------------- | ------------- | ------------------------------------------ |
| AGR_1016         | 活动组参数文件名称                    | AGR_FLAGS     | 角色属性                                   |
| AGR_1016B        | 活动组参数文件名称                    | AGR_FLAGSB    | 角色属性                                   |
| AGR_1250         | 活动组的权限数据                      | AGR_HIER      | 菜单结构信息表                             |
| AGR_1251         | 活动组的权限数据                      | AGR_HIER_BOR  | Table for Object-Oriented Navigation (OBN) |
| AGR_1252         | 权限的组织元素                        | AGR_HIER2     | 菜单结构信息 - SAP 角色的客户版本          |
| AGR_1253         | 作业组的权限数据 - 静态对象           | AGR_HIER3     | 菜单结构信息 - SAP 角色的 SAP 版本         |
| AGR_AGRS         | 组合角色中的角色                      | AGR_HIERT     | 角色菜单文本                               |
| AGR_AGRS2        | 作用定义                              | AGR_HIERT2    | 角色菜单文本 - SAP 对象的客户版本          |
| AGR_ATTS         | 角色属性                              | AGR_HIERT3    | 角色菜单文本 - 原始 SAP                    |
| AGR_BUFFI        | 角色的 Internet 链接表                | AGR_HPAGE     | Role Home Page                             |
| AGR_BUFFI2       | Internet 链接表 - SAP 角色的客户版本  | AGR_HPAGET    | Description of the Home Page for a Role    |
| AGR_BUFFI3       | Internet 链接表 - SAP 角色的 SAP 版本 | AGR_INFO      | Filter Values from Generation Run          |
| AGR_CUSTOM       | 角色的定制对象                        | AGR_LOGSYS    | 逻辑系统                                   |
| AGR_DATEU        | 角色的个人设置                        | AGR_LSD       | 角色属性                                   |
| AGR_DEFINE       | 角色定义                              | AGR_MAP_KNUMA | 换算表 AG_GUID CRM <> KNUMA                |
| AGR_FAVOS        | PFCG 的个人设置                       | AGR_MAPP      | 角色中的 MiniApps                          |
| AGR_MARK         | 报表 SAPPROFC_NEW 的表格              | AGR_TIME      | 角色的日期标记                             |
| AGR_MEM_INITIAL  | 协议：初始上载的缓冲                  | AGR_USERS     | 分配角色到用户                             |
| AGR_MINI         | 角色中的 MiniApps                     | AGR_USERT     | 分配角色到用户                             |
| AGR_MINI2        | 角色中的 MiniApps                     | AGR_TIMEB     | 角色的日期标记                             |
| AGR_MINIT        | 角色最小应用文本                      | AGR_TIMEC     | 角色的日期标记                             |
| AGR_MINIT2       | 角色最小应用文本                      | AGR_TIMED     | 角色的日期标记                             |
| AGR_NUM_2        | 分配参数文件名的内部计数器            | AGR_TCDTXT    | 将角色分配到事务代码                       |
| AGR_NUMBER       | 分配参数文件名的内部计数器            | AGR_TCODE3    | 将角色分配到事务代码                       |
| AGR_OBJ          | 菜单节点分配给角色                    | AGR_TCODES    | 将角色分配到事务代码                       |
| AGR_PROF         | 角色的参数文件名                      | AGR_TEXTS     | 用于层次菜单的文件结构 - 客户              |
| AGR_REL_KNUMA_CM | 分配：协议   > 活动                   | AGR_SELECT    | 将角色分配到事务代码                       |