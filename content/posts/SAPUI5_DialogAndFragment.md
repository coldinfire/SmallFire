---
title: " SAPUI5 Dialog and Fragment "
date: 2019-04-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - SAP Fiori

tags: 
  - sapui5

---

## Dialogs and Fragments

### Fragments

1. 片段是一个轻量级的 UI 部分，它只是一组重用控件的容器。包含一到多个控件，不需要控制器。

2. 定义并调用已存在的 Fragments : "sap/ui/core/Fragment"

   1) 定义xxx.fragment.xml

   ```xml
   <core:FragmentDefinition xmlns="sap.m" xmlns:core="sap.ui.core">
   	<Dialog id="helloDialog" title="Hello {/recipient/name}">
   		<content>
               <core:Icon src="sap-icon://hello-world" size="80px" class="sapUiMediumMargin">
               </core:Icon>
   		</content>
   		<beginButton>
               <Button text="{i18n>dialogCloseButtonText}" press=".onCloseDialog"></Button>
   		</beginButton>
   	</Dialog>
   </core:FragmentDefinition>
   ```

   

   ```js
   // create dialog lazily
   if (!this.byId("helloDialog")) {  //如果id为helloDialog的Dialog不存在
   	// load asynchronous XML fragment
   	Fragment.load({
   		id: oView.getId(),
   		name: "sap.ui.demo.walkthrough.view.HelloDialog",
             controller:this    //函数的回调
   	}).then(function (oDialog) {
   		// connect dialog to the root view of this component (models, lifecycle)
   		oView.addDependent(oDialog);
   		oDialog.open();
   	});
   } else {
   	this.byId("helloDialog").open();
   },  
   onCloseDialog:function(){
       this.byId("helloDialog").close();
   }
   ```

   - 始终使用addDependent方法将对话框连接到视图的生命周期管理和数据绑定，即使它未添加到其UI树中。

3. 如果片段中的对话框尚不存在，则通过使用以下方法调用sap.ui.xmlfragment方法来实例化片段

4. 回调open方法

### Dialog

1、不属于特定视图，不能将其定义为视图，这意味着必须在控制器代码中的某处实例化对话框。

2、重用

- 
  定义单独的控制来实现Dialog的创建 HelloDialog.js

```JS
sap.ui.define([
    "sap/ui/base/ManagedObject", //实现该类
    "sap/ui/core/Fragment"
  ],
  function (ManagedObject, Fragment) {
  "use strict";
  return ManagedObject.extend("SAPUI5.Walkthrough.controller.HelloDialog", {
    constructor: function (oView) { //oView参数用于关联当前视图到对话框.
      this._oView = oView;
    },
    exit: function () {
      delete this._oView();
    },
    open: function () {
      var oView = this._oView;
      //create dialog lazily
      if (!oView.byId("helloDialog")) {
        var oFragmentController = {
          onCloseDialog: function () {
            oView.byId("helloDialog").close();
          }
        };
        //load asynchronous XML fragment
        Fragment.load({
          id: oView.getId(),
          name: "SAPUI5.Walkthrough.view.HelloDialog",
          controller: oFragmentController
        }).then(function (oDialog) {
          //connect dialog to the root view of this component (models,lifecycle)
          oView.addDependent(oDialog);
          oDialog.open();
        });
      } else {
        oView.byId("helloDialog").open();
      }
    }
  });
});
```

- 在Component.js文件中声明该控件为私有属性,并封装其方法

```JS
sap.ui.define([
    "sap/ui/core/UIComponent",
    "sap/ui/Device",
    "SAPUI5/Walkthrough/model/models",
    "sap/ui/model/json/JSONModel",
    "./controller/HelloDialog"
  ], function (UIComponent, Device, models,JSONModel,HelloDialog) {
    "use strict";
    return UIComponent.extend("SAPUI5.Walkthrough.Component", {
      metadata: {
      manifest: "json"
    },
    /**
    * The component is initialized by UI5 automatically during the startup of 
    the app and calls the init method once.
    * @public
    * @override
    */
    init: function () {
      // call the base component's init function
      UIComponent.prototype.init.apply(this, arguments);
      // enable routing
      this.getRouter().initialize();
      // set the device model
      this.setModel(models.createDeviceModel(), "device");
      //set dialog
      this._helloDialog = new HelloDialog(this.getRootControl());
    },
    exit:function(){
      this._helloDialog.destory();
      delete this._helloDialog;
    },
    openHelloDialog:function(){
      this._helloDialog.open();
    }
  });
});
```

3、在按钮事件中通过`this.getOwnerComponent().openHelloDialog()`调用

onOpenDialog 方法现在通过调用辅助方法 getOwnerComponent 来访问其组件。当调用重用对象的 open 方法时，我们传入当前视图以将其连接到对话框。

4、Attention

将跨多个控制器使用的所有资产放在单独的模块中