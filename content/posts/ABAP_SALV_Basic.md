---
title: " 使用SALV功能创建ALV "
date: 2019-06-03
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

引用自：[https://www.cnblogs.com/jiangzhengjun/p/4291387.html](https://www.cnblogs.com/jiangzhengjun/p/4291387.html)

### 简介

SALV 可以像使用函数方式生成 ALV 那样，不用创建屏幕就可以调用的全屏方式显示的 ALV.

SALV 的 GRID 报表可以在后台运行，但以前函数方式或 OO 方式生成的 GRID 不能.

SALV 与现有的方法（Function ALV）相比，为了方便以接口的方式提供更整合及细微的功能.

SALV 不提供编辑功能，但可以通过 SALV 适配器调用 CL_GUI_ALV_GRID 修改成编辑模式，就可以在 ALV 中修改数据了.

这些新的面向对象的 ALV 模型具有许多优点：

- 简化设计：该模型使用高度集成的面向对象设计，这使程序员可以轻松开发 ALV。
- 统一对象模型：此模型只有一个主类，该主类将获取并设置整个布局的参数。

### 主要的类型和方法

| ALV类型       | 使用的类               |
| ------------- | ---------------------- |
| 简单的ALV报表 | CL_SALV_TABLE          |
| 分层的ALV显示 | CL_SALV_HIERSEQU_TABLE |
| 树形的ALV显示 | CL_SALV_TREE           |

**FACTORY**：所有类都有静态方法 FACTORY，该方法将取回 ALV 的实例。就像简单的报表显示一样，我们必须调用方法 `CL_SALV_TABLE => FACTORY `来获取 ALV 的实例。

 有三种显示方法可以通过传入参数设置：

|          | LIST_DISPLAY | R_CONTAINER               | CONTAINER_NAME      |
| -------- | ------------ | ------------------------- | ------------------- |
| 全屏模式 | ABAP_FALSE   | 初始值                    | 初始值              |
| List列表 | ABAP_TRUE    | 初始值                    | 初始值              |
| 控制器   | ABAP_FALSE   | CL_GUI_CONTAINER 对象引用 | Custom Control name |

```JS
" 判断是否已分配了一个有效引用 "
IF gr_container IS NOT BOUND.
 " 创建容器 "
 CREATE OBJECT gr_container
   EXPORTING
     container_name = 'CONTAINER_1'. " 屏幕上用户自定义控件名 "
 " 创建 ALV "
 cl_salv_table=>factory(
   EXPORTING
     r_container = gr_container
     container_name = 'CONTAINER_1'
  IMPORTING
     r_salv_table = gr_table
  CHANGING
     t_table = gt_data[] ).
```
**DISPLAY**：实例方法，调用此方法显示ALV

![CL_SALV_TABLE](/images/ABAP/SALV1.png)

### 设置相关属性方法

对 SALV 进行设置，先需要通过 CL_SALV_TABLE 对象实例获取相关设置对象，然后再对这些设置对进行操作

![CL_SALV_TABLE GET*](/images/ABAP/SALV2.png)