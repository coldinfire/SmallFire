---
title: " SAPUI5 Message "
date: 2019-04-27
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - SAP Fiori

tags: 
  - sapui5

---

## 消息设置

### sap.m.MessageBox

SAPUI5 提供的对话框，可以显示信息、警告、错误等等。MessageBox 类是静态类，在使用之前必须执行 `jQuery.sap.require("sap.m.MessageBox")` 语句 SAPUI5 包含 jQuery 包，`jQuery.sap.require(vModuleName)` 方法的作用是加载指定的模块并且执行，这样 MessageBox 的 show() 方法才能运行。

> `sap.m.MessageBox.alert(vMessage, mOptions*?*) `对话框显示消息，有一个OK按钮（“确定”），没有图标
>
> `sap.m.MessageBox.confirm(vMessage, mOptions*?*)` 确认对话框，询问是否确定，有一个OK按钮和Cancel按钮，一个问号的图标。
>
> `sap.m.MessageBox.error(vMessage, mOptions*?*)` 显示错误对话框，带有错误图标和关闭按钮Displays an error dialog with the given message, an ERROR icon, a CLOSE button。
>
> `sap.m.MessageBox.information(vMessage, mOptions*?*)` 消息对话框，带有INFO图标和OK按钮。
>
> `sap.m.MessageBox.show(vMessage, mOptions*?*)` 显示对话框，类型为sap.m.DialogType.Message，图标和按钮由开发人员自行定义，相对灵活一些。
>
> `sap.m.MessageBox.success(vMessage, mOptions*?*)` 显示成功对话框，带有SUCCESS图标和OK按钮。
>
> `sap.m.MessageBox.warning(vMessage, mOptions*?*)` 显示警告消息，带有WARNING图标和OK按钮。
>
> `sap.m.MessageToast.show()`对用户操作提供一种简单的反馈，并且经过一段时间后自动消失，除非用户将鼠标放在消息上面。