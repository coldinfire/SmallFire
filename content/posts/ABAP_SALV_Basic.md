---
title: " SALV 创建ALV "
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

SALV 可以使用函数方式生成 ALV ，不用创建屏幕就可以调用的全屏方式显示的 ALV。

SALV 的 GRID 报表可以在后台运行，但以前函数方式或 OO 方式生成的 GRID 不能。

SALV 与现有的方法（Function ALV）相比，为了方便以接口的方式提供更整合及细微的功能。

SALV 不提供编辑功能，但可以通过 SALV 适配器调用 CL_GUI_ALV_GRID 修改成编辑模式，就可以在 ALV 中修改数据了。

这些新的面向对象的 ALV 模型具有许多优点：

- 简化设计：该模型使用高度集成的面向对象设计，这使程序员可以轻松开发 ALV
- 统一对象模型：此模型只有一个主类，该主类将获取并设置整个布局的参数

### 主要的报表类型和方法

| ALV 类型               | 类型描述                 |
| :--------------------- | :----------------------- |
| CL_SALV_TABLE          | 简单的ALV报表类型        |
| CL_SALV_HIERSEQU_TABLE | 以层次结构显示的报表类型 |
| CL_SALV_TREE           | 以树形结构显示的报表类型 |

要显示ALV，只需要调用以下两个方法即可：

- FACTORY：静态方法。定义要显示在屏幕上的数据；定义 ALV 报表样式；调用此方法就会返回 CL_SALV_TABLE 类型的实例对象

- DISPLAY：实例方法。调用此方法屏幕上就可以显示 ALV

#### FACTORY 静态方法

所有类都有静态方法 FACTORY，该方法将取回 ALV 的实例。就像简单的报表显示一样，我们必须调用方法 `CL_SALV_TABLE => FACTORY( ). `来获取 ALV 的实例。

 有三种显示方法可以通过传入参数设置：

- LIST_DISPLAY：该参数决定了列表显示的模式是以 List 普通列表方式显示，还是 Grid 网格方式显示
- R_CONTAINER：用户自定义控件区域的引用对象，类型为 CL_GUI_CONTAINER
- CONTAINER_NAME：屏幕上用户自定义控件区域（Custom Control）的名称
- R_SALV_TABLE：用来接收工厂产生的实例

| Factory 参数   | 全屏模式   | List列表  | 利用控制器的模式          |
| :------------- | :--------- | :-------- | :------------------------ |
| LIST_DISPLAY   | ABAP_FALSE | ABAP_TRUE | ABAP_FALSE                |
| R_CONTAINER    | 初始值     | 初始值    | CL_GUI_CONTAINER 对象引用 |
| CONTAINER_NAME | 初始值     | 初始值    | Custom Control name       |

参数定义

```ABAP
DATA: go_table TYPE REF TO cl_salv_table.
DATA: go_functions TYPE REF TO cl_salv_functions_list.
DATA: go_container TYPE REF TO cl_gui_custom_container.
```

使用示例

```ABAP
" 判断是否已分配了一个有效引用 "
IF go_container IS NOT BOUND.
  " 创建容器 "
  CREATE OBJECT go_container
    EXPORTING
      container_name = 'CONTAINER_1'. " 屏幕上用户自定义控件名 "
  " 创建 ALV "
  cl_salv_table=>factory(
    EXPORTING
      r_container = go_container
*     list_display = abap_true "以列表形式显示"
      container_name = 'CONTAINER_1'
    IMPORTING
      r_salv_table = go_table
    CHANGING
      t_table = gt_data[] ).
  "设置工具栏"
  go_functions = go_table->get_functions( ).
  "将激活所有的ALV内置通用按钮"
  go_functions->set_all( abap_true). 
  "显示"
  go_table->display( ).
ENDIF.
```
#### DISPLAY 方法显示 ALV

实例方法，调用此方法显示 ALV。

![CL_SALV_TABLE](/images/ABAP/SALV1.png)

### 设置相关属性方法

对 SALV 进行设置，先需要通过 CL_SALV_TABLE 对象实例获取相关设置对象，然后再对这些设置对进行操作。

如对样式的设置，需要先通过 CL_SALV_TABLE 对象的 GET_LAYOUT( ) 方法拿到 CL_SALV_LAYOUT 对象，实后通过该对象相关方法对 SALV 进行设置；除样式外，如排序、工具栏设置、事件等，都是先通过某个 GET 方法拿到相应对象，再通过该对象相关方法对 SALV 进行设置，这与以前 Function ALV、及 OO ALV 不太一样。（如上面的设置工具栏代码所示）

![CL_SALV_TABLE GET](/images/ABAP/SALV2.png)