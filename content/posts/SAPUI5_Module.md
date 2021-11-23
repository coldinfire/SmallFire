---
title: " SAPUI5 模块化 "
date: 2019-04-22
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  Sap Fiori

tags: 
  - sapui5

---

### 模块化

#### 如何加载模块

> `jQuery.sap.declare(sModuleName,bCreateNamespace)`申明一个模块，以确保模块存在。这个语句必须出现在	模块代码（也就是代码文件)的第一句。
>
> `jQuery.sap.require(vModuleName)`确保当前代码继续之前，所指定的模块被加载和执行。如果所需要的模块没有被加载，将会被同步加载和执行，如果已经加载，就忽略。
>
> `sap.ui.define(sModuleName,aDependencies,vFactory,bExport)`定义module，异步加载依赖模块,sap.ui.define()定义的模块具有全局命名空间。2:定义依赖 3:继承工厂函数。
>
> `sap.ui.require()`异步加载依赖的模块，不具有全局命名空间。

#### 使用模块方法实现Controller

```JS
sap.ui.define(
	["Dependencies1","Dependencies2"],
	function(Controller){
	"use strict";
		return Controller.extend("ControllerName",{
			onInit:function(){},
               onBeforeRendering:function(){},
               onAfterRendering:function(){},
               onExit:function(){}
		});
	}
);
```

- 参数1 : 不定义，便于对模块进行访问
- 参数2 : 指定依赖的模块，可指定多个
- 参数3 : 定义工厂函数，实现Controller功能