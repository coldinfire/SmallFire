---
title: " SAPUI5 数据绑定 "
date: 2019-04-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  Sap Fiori

tags: 
  - sapui5

---

## Model Binding

### 单数据绑定

#### 使用数据绑定前

需要先实例化Model。构造函数获取实例的 URL 或则数据本身作为第一个参数。

JSON-Model:

- `var oModel = new sap.ui.model.json.JSONModel(dataUrlOrData);`

XML-Model:

-  `var oModel = new sap.ui.model.xml.XMLModel(dataUrlOrData);`


OData-Model:

- `var oModel = new sap.ui.model.odata.ODataModel(dataUrl[,userJSON,user,pass]);`

#### 给 Model 设置值

oModel.setData(data);：绑定定义的数据

oModel.loadData("models/suppliers.json");：从文件中读取数据绑定

#### 将 Model 分配给 Core 或则其他的控制器（setModel）方法

Global model:

- `sap.ui.getCore().setModel(oModel) ` : 这样oModel对整个应用程序可见

Bind a model to a view

- `var oView = sap.ui.view({type:sap.ui.core.mvc.ViewType.JS,viewName:"text.view"})`          

- `oView.setModel(oModel);`

Bind a model to a specific control

- `var oTable = sap.ui.getCore().byId("table");`

- `oTable.setModel(oModel);`

#### Model属性绑定方法（{ } curly braces，bindProperty()）

控件的大多数属性都可以绑定到模型属性。

bindProperty  method: [extend : sap.ui.base.ManagedObject]

- `oControl.bindProperty("sName","oBindingInfo");`

- oBindingInfo attributes : path、model、formatter等
  - path : 指定绑定的数据路径
  - model : sap.ui.model.BindingMode.OneWay、sap.ui.model.BindingMode.TwoWay
  - formatter : 

花括号:{ }

- `var oControl = new sap.ui.commons.TextView({controlProperty:"{/modelProperty}"});`

alternatively

- `var oControl = new sap.ui.commons.TextView({controlProperty:{path:"/modelProperty"}});`

#### Model属性的获取

- oModel.getProperty("/sName"); ：根据JSON数据属性名获取对应的值

### 多数据绑定 

用于绑定集合数据，如绑定多行数据到一个表格中。

- 使用模板:所有行都用同样的方法显示数据

```JS
var oItemTemplate = new sap.ui.core.ListItem({text:"{aggrProperty}"});
var oComboBox = new sap.ui.commons.ComboBox({
  items:{path:"/modelAggregation", template: oItemTemplate}
});
```

- bindAggregation():

​        `oComboBox.bindAggregation("items","/modelAggregation",oItemTemplate);`

- 工厂函数实现聚合绑定

  ```js
  oTable.bindAggregation("items","/modelData",function(sId,oContext){
    return oColumnListItem;	//通过工厂函数，定义数据并返回
  });
  ```

### 元素绑定

元素绑定指根据上下文(binding context)使用相对绑定的方式绑定到model数据的某一具体对象。尤其适用于**主从数据显示(master-detail data)**的情况。

​	sap.m.List(sId?, mSettings?) : List控件适用于显示行项目，所有类型都可以。

sap.m.ObjectListItem(sId?, mSettings?) : 适用于显示行项目的信息，主要使用title属性进行标识，text、icon、atrributes和statuses等属性可以用于提供对象更多信息。

sap.m.Panel().bindElement({path:sPath}) : 将显示的明细与Model绑定。

1. 左边是一个List控件，右边在Panel中放置几个控件组合。当选择左边某个产品的时候，右边相应显示该产品的信息。

  `oEvent.getSource().getBindingContext()`获取绑定的项，再使用`getPath()`方法得到path路径，然后设置右边的detailPanel与这个路径绑定。

2. Model 中detail包含多条数据的情况，点击 一个header,显示多个行项目，可以通过`sap.ui.model.Filter()`的方法实现。

   绑定点击事件,`oSupTable.attachRowSelectionChange(fuction(oEvent()))`。

   通过`var oRowContext = oEvent.getParameter("rowContext")`获取行的上下文。如果选中第一行，rowContext就是constructor {oModel: 指定Model, sPath: "数据第一行地址"}。

   然后通过`var sSelectedId = oModel.getProperty("id", oRowContext)`;就能获取到所选择行的id数据。

   通过 `var oBinding = oPrdTable.getBinding()`;获取对应详细数据的绑定。

   定义过滤规则，var oF = new sap.ui.model.Filter({path:"key index",oprator: new sap.ui.model.FilterOperator.BT,value1:value...})。

   使用过滤规则，oBinding.filter(oF);

### 绝对绑定和相对绑定

1. 绝对绑定

   将 value 属性绑定到 json model 根目录下对应的字段 title/attr。

2. 相对绑定

   **相对绑定尤其适用于布局(layout)控件或者容器控件**

   当父控件的绑定路径设置后，子控件可以基于这个路径使用相对路径。