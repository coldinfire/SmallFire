---
title: " PM&PS BAPI "
date: 2020-06-30
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - BAPI

---

### PM 模块

| BAPI                          | Description                   |
| :---------------------------- | :---------------------------- |
| BAPI_OBJCL_CREATE             | 分类 BAPI：创建分配           |
| BAPI_OBJCL_CHANGE             | 分类 BAPI：更改分配           |
| BAPI_OBJCL_GETDETAIL          | 分类 BAPI：读取对象的分类信息 |
| MEASUREM_DOCUM_RFC_SINGLE_001 | 计量凭证创建                  |

### PS 模块

| BAPI                      | Description                                                  |
| :------------------------ | :----------------------------------------------------------- |
| BAPI_PS_INITIALIZATION    | 创建项目定义                                                 |
| BAPI_BUS2001_CREATE       | 创建项目定义                                                 |
| BAPI_PS_PRECOMMIT         | 创建项目定义                                                 |
| BAPI_PS_INITIALIZATION    | 创建WBS                                                      |
| BAPI_BUS2054_CREATE_MULTI | 创建WBS                                                      |
| BAPI_PS_PRECOMMIT         | 创建WBS，注意参数 wbs_left和 wbs_up，这个是创建有层级的WBS必须要填写的 |
| KBPP_EXTERN_UPDATE_CO     | 修改项目和WBS的预算                                          |

