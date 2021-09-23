---
title: " SAPUI5 数据类型和操作  "
date: 2019-04-21
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  Sap Fiori

tags: 
  - sapui5

---

## 数据类型和操作

### 基本数据类型

> sap.ui.model.type.Integer(oFormatOptions?, oConstraints?):支持minimum,maximum
>
> sap.ui.model.type.Float(oFormatOptions?, oConstraints?):`decimalSeparator`定义小数位的分隔符
>
> sap.ui.model.type.String(.........)
>
> sap.ui.model.type.Boolean
>
> sap.ui.model.type.Date : ui5支持原数据为JavaScript和原数据为String的日期数据进行格式输出
>
> sap.ui.model.type.Time : Time也支持原数据为Time类型或者字符串类型
>
> sap.ui.model.type.DateTime

### 属性设置

1. 尽可能使用数据类型而不是自定义格式化程序。

   ```JS
   number="{
   	parts: [{path: 'invoice>ExtendedPrice'}, {path: 'view>/currency'}],
   	type: 'sap.ui.model.type.Currency',
   	formatOptions: {
   		showMeasure: false
   	}
   }"
   ```

   - 计算字段绑定(parts)：它允许将来自不同模型的多个属性绑定到控件的单个属性。
   - 控件的属性是数字，从两个不同模型检索的绑定属性（“部件”）invoice> ExtendedPrice和view> / currency。

2. Expression Binding 仅使用表达式绑定进行简单的计算。

   `numberState="{= ${invoice>ExtendedPrice} > 50 ? 'Error' : 'Success' }`

### 数据校验

#### 基本使用

sap.ui.core.message.MessageManager();

registerObject(oObject,bHandelValidation)：第一个参数是 ManagedObject 对象的实例；第二个参数是 boolean 类型变量，为 true 时执行数据校验。

- attachValidationError(this,function(){})：控件都有该方法，用于校验失败时的处理。


- attachValidationSuccess(this,function(){})：用于校验成功时的处理。


ValueState 种类：setValueState()

- sap.ui.core.ValueState.Error


- sap.ui.core.ValueState.None


- sap.ui.core.ValueState.Success


- sap.ui.core.ValueState.Warning	

#### 集中处理数据校验

sap.ui.core.Core 也可添加 attachValidationError().

#### 自定义数据类型校验

sap.ui.model.SimpleType.extend() 自定义数据类型，可以使用 formatValue()、parseValue()

validateValue() 实现自定义的校验规则和提示消息。

抛出异常信息：throw new sap.ui.model.ValidationException("Message");

截取异常消息：使用该类型的控件通过`oEvent.getParameter("message")`获取该错误消息。

### Formart设置

1. 在Constructor或绑定方法中定义formatter (绑定单个控件)

   var oText = new sap.m.Text({
   		text:{ formatter:function(sValue){
   						return sValue && sValue.toUpperCase();
   				}
   		}
     });

2. 在Controller中定义formatter (更灵活，可重复调用)

   <Text text="{path: '/productname', formatter: '.toUpper'}"/>

   其中` .toUpper` 前面的`.`表示当前Controller方法。

3. 在专门模块中定义formatter

   单独定义formatter在Controller中引入该文件。并在view中调用。

4. 自定义数据类型中设置formatter

### 搜索与过滤

1. 添加搜索框并绑定事件 `sap.m.SearchField`

   ```xml
   <List id="invoiceList" items="{invoice>/Invoices}">
   <headerToolbar>
      <Toolbar>
         <Title text="{i18n>invoiceListTitle}"/>
         <ToolbarSpacer/>
         <SearchField width="50%" search=".onFilterInvoices"/>
      </Toolbar>
   </headerToolbar>
      <items>
      	<ObjectListItem>
        	,,,,,,
        </ObjectListItem> 
      </items>
   </List>
   ```

2. 事件定义并实现过滤

   filter对象将保留我们对filter操作的配置。**new sap.ui.model.Filter(vFilterInfo, vOperator?, vValue1?, vValue2?)**

   FilterOperator是我们需要的帮助器类型，以指定过滤器，范围。

   ```JS
   new Filter({
         path: "Price",
         operator: FilterOperator.BT,
         value1: 11.0,
         value2: 23.0
       });
       
   new Filter({
       filters: [
         ...
         new Filter({
           path: 'Quantity',
           operator: FilterOperator.LT,
           value1: 20
         }),
         new Filter({
           path: 'Price',
           operator: FilterOperator.GT,
           value1: 14.0
         })
         ...
       ],
       and: true|false
     })
   ```

   

   ```js
   onFilterInvoices : function (oEvent) {
   	// build filter array
   	var aFilter = [];
   	var sQuery = oEvent.getParameter("query"); //"query" 获取搜索字段
   	if (sQuery) {
   		aFilter.push(new Filter("ProductName", FilterOperator.Contains, sQuery)); //添加过滤条件
   	}
   
   	// filter binding
   	var oList = this.byId("invoiceList");     //获取List对象
   	var oBinding = oList.getBinding("items"); //获取绑定的items
   	oBinding.filter(aFilter);			   //根据过滤条件过滤items数据
   }
   ```

### 排序与分组

​		`new sap.ui.model.Sorter(sPath, bDescending?, vGroup?, fnComparator?)`

1. items="{path:'invoice>/Invoices' sorter:{path:'ProductName'}}"  //默认是升序ascending,可以添加属性descending : true.
2. items="{path:'invoice>/Invoices' sorter:{path:'ProductName',group:true}}"

### 私有函数和变量

- 私有函数和变量应始终以下划线开头。