---
title: " Web Dynpro ABAP - Trigger ALV Events "
date: 2018-10-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro

---

### Question

由于需要一个可编辑的 ALV 表，因此必须使用 ALV 事件，例如 on_cell_action事件和 on_click 事件。

尽管我已经注册了 ALV 事件，但是在调试过程中，无法触发 ALV 事件，例如 on cell action 事件。由于未启用 ALV 事件，因此该事件触发失败。

在以 WDDOMODIFYVIEW 方法在 Web Dynpro 组件中为动态创建的 ALV 启用 ALV 事件后，成功地成功触发了 ALV 事件。

在本 Web Dynpro 教程中，我将展示如何为 ALV 事件注册事件处理程序以及如何使用 ABAP 代码以 WDDOMODIFYVIEW 方法启用事件。

### 在 Web Dynpro 组件中注册 ALV 事件处理程序

要在 Web Dynpro 中触发 ALV 事件，例如 ON_CLICK 或 ON_CELL_ACTION， ABAP开发人员应为相应的事件方法注册事件处理程序，并应在 Web Dynpro 组件的 WDDOMODIFYVIEW 方法中启用相关事件。

为了注册上述事件，请转到 View 对象，ABAP 开发人员将在其中显示 ALV 表对象。然后切换到 Methods 选项卡，如下面的屏幕截图所示:

![Method Types](/images/webdynproABAP/Portal32.png)

首先从 Method Type 下拉列中选择 **Event Handler**。然后在 **Event** 的列上使用搜索帮助,选择相关组件使用所需的事件。然后键入事件处理程序方法的名称和描述。

![Event Types](/images/webdynproABAP/Portal33.png)

### 在 Web Dynpro WDDOMODIFYVIEW 方法中启用 ALV 事件

在成功触发 ALV 事件之前，应修改 Web Dynpro 组件 WDDOMODIFYVIEW 方法以启用 ALV 事件。以下 ABAP 代码可用于启用 WDDOMODIFYVIEW 方法中的 ALV 事件。

```html
* Data Declaration
data alv_cmp_usage type ref to if_wd_component_usage.
data alv_controller type ref to iwci_salv_wd_table .
data alv_config type ref to cl_salv_wd_config_table.
* Instantiate ALV 
alv_cmp_usage = WD_THIS->WD_CPUSE_ALV_OPENITEMS( ).
IF alv_cmp_usage->HAS_ACTIVE_COMPONENT( ) is initial.
  alv_cmp_usage->CREATE_COMPONENT( ).
ENDIF.
alv_controller = wd_this->wd_cpifc_alv_openitems( ).
* Call get_model of Interface Controller to get reference of ALV
alv_config = lalv_controller->get_model( ).
* Enable the ON_CELL_ACTION Event
call method alv_config->if_salv_wd_table_settings~set_cell_action_event_enabled
 exporting
  value = ABAP_TRUE .
```

当您单击 ALV 行或在可编辑的 ALV 表单元上按 Enter 键时，将触发这些事件处理程序。可以在这些 ALV 事件处理程序方法中添加自定义代码。