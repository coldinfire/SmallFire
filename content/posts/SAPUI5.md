---

title: " SAPUI5 概述 "
date: 2019-04-17
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  Sap Fiori

tags: 
  - sapui5

---

### SAPUI5概述

SAPUI5:一套基于HTML5,CSS3,JavaScript的开发框架

#### SAPUI5包括内容

- 重构的JavaScript语法规范，帮助用户使用一致的、规范的语法使用JavaScript
- 帮助用户构建大型应用的框架，包括MVC、数据绑定、路由控制、缓存、组件化、模块化
- 丰富的基于桌面和移动端的基础UI控件
- SAP在开发Fiori App过程中积累的可复用的高度封装的UI控件
- 便于页面排版的布局控件
- 创建自定义控件和整合第三方控件的组件
- 基于LESS框架和主题设计器的CSS架构

#### SAP核心优势

- 基于Web端开发的一站式解决方案
- 快速开发，相对高效运行
- 作为SAP未来标准产品使用的UI技术，已经经过大量实践验证，稳定可靠
- 具有大量的参考示例程序，可以快速的编写与SAP标准风格类似的应用程序
- 具有编写大型应用程序所需要的框架和技术
- 能够完美的整合第三方的JavaScript库

#### 开发工具：WebIDE

优势和功能

- 基于云端，注册就可以使用

- 具有强大的代码生成

- 具有所见即所得的视图编辑功能

- 具有预先定义的样例程序

#### SAP支持的控件

- 桌面控件库
- UX3控件库
- 表格控件库
- 移动端控件库

### MVC模式

​     MVC是程序设计的思想实现，通过将界面展示，用户操作，程序数据进行分离，降低模块间的耦合性，有利于项目的开发和维护。

​    M : Model 代表应用程序的数据

​    V : View 通过界面展示应用程序的数据和其它界面元素

​    C : Controller 处理应用程序的数据，以及处理用户的交互

![MVC](/images/SAPUI5/MVC.png)

   ● Model & View : SAPUI5 有单向绑定和双向绑定两种。通过绑定，当 model 变更时，UI 自动更新。

   ● Controlle &View : View 通知 Controller，或者 Controller使用API来修改 View。

   ● Controller & Model : Model 通知 Controller或者 Controller 修改 Model。

SAPUI5提供了JSView、XMLView、JSONView和HTMLView。主要使用XMLView 和 JSView。

### 核心类库 

​	查看`Resource`版本：Cttl+Shift+Alt+P在对应的App界面或则LunchPad.

   ` sap.m: `主要用于移动设备的响应式组件，并支持很多移动设备特性检测，比如检测touch等，此库下面List, Table等组件使用比较广泛，而且包含了下拉刷新的功能，非常完善，并自动适应不同尺寸平台

` sap.ui:` UI库包含的组件是最为丰富的，主要用于适应桌面平台，同样可以支持响应式的设计，比如sap.ui.table等组件

`sap.ui.core:`核心功能：加载和管理所有的附加资源，并包含模型和渲染管理器，一个负责处理渲染视图和控制的单例，写入DOM

`sap.ui.layout:` 构建视图中元素的特殊控件

`sap.ui.vk:` 包含3D模型的功能和控件

`sap.ui.unified:` 包含用于移动和桌面应用程序的附加控件

`sap.ui.table:` 不适用于手机屏幕，处理大量数据应用而构建的

`sap.ui.comp:` 包含SmartField,SmartFilterBar,SmartTable,ValueHelpDialog等智能控件

`sap.uxap:` 包含更多控件，包括页面标题

`sap.ushell:` 包含几个库包，含有所有统一的与shell服务相关的功能

   `sap.ca:` 是官方标准app的常用类库，如果在实际开发过程当中想要拓展标准应用，必须要了解此类库的一些特性，否则拓展起来会有很大问题。

### 核心函数

   `sap.ui.getCore()` : 获取核心的实例

   ` sap.ui.getCore().byId(id)` : 根据组件id获取其控制；可用于获取已经删除的组件

   `sap.ui.getCore().applyChanges()` : 在系统运行前修改UI5组件属性

   ` jQuery.sap.domById(id)` : 根据ID获取HTML元素;如果UI5也存在该ID返回HTML最顶层的元素;和 document.getElementById 类似

   `jQuery.sap.byId(id)` : 根据ID获取JQuery对象的DOM元素; $(,,#myId)

  ` sap.m.MessageBox` : SAPUI5风格对话框显示

`sap.m.URLHelper.redirect("http://www.jd.com", true);`重定向

### 常用组件

#### SplitApp

​    SplitApp : 这是一个Master Detail形式的结构，可以在移动，桌面不同尺寸的设备上自适应，也是大部分app需要采用的一种架构形式。

#### List

   List : 列表在任何应用中是比较常见的，List在SAPUI5定义功能比较完善，支持分批加载数据，提高运行效率，支持下拉等功能，并提供给我们丰富的标准Item, 可以简单配置使用，更可以根据自身需求定义item。

​	对于移动设备来说，出于性能考虑，不要超过100行。使用**growing**特性可以加速内部的渲染。

​	List控件继承自`sap.m.ListBase`，ListBase的items聚合属性(类型：`sap.m.ListeItemBase[]`) 设置行项目的模板。

- sap.m.List

  - new sap.m.List({items:{path:"/path",template:oTemplate}});

  - new sap.m.List().bindItems({path:"/path",template:oTemplate});

- sap.m.ObjectListItem


ObjectListItem适用于显示行项目的信息，主要使用title属性进行标识，text、icon、atrributes和statuses等属性可以用于提供对象更多信息。继承自sap.m.ObjectListItem，可定义press事件对用户的点击做出回应。

#### Table

​    Table : 这是一个表单性质的的组件，支持响应式，很多是够我们做一个表单展示需要此控件的支持。自身也携带了丰富的property供我们选择。

##### sap.ui.table.Table 

 1. sap.ui.table.Table

    `oTable.setModel(oModel);`实现Table与JSONModel的绑定，也就是View和Model的绑定。

    `oTable.bindRows("/Suppliers");`语句实现Table与JSON数据的绑定。

    > width : sap.ui.core.CSSSize (default: auto)。表格的宽度可以是百分百，或者基于像素px。
    >
    > titel : 设置Table的标题 。
    >
    > visibleRowCount : int (default: 10)。默认显示10行，可以自定义显示的行数。 
    >
    > firstVisibleRow : int (default: 0) 。默认为0，从第一行开始展示数据。
    >
    > selectionMode : sap.ui.table.SelectionMode (default: MultiToggle)。包括单行(sap.ui.table.SelectionMode.Single)、多行	         								 (sap.ui.table.SelectionMode.MultiToggle)和不能选择行(sap.ui.table.SelectionMode.None)。 
    >
    > editable : boolean (default: true)。默认可以编辑，如果只是显示，将此属性设置为false。
    >
    > columns : [ind1,ind2] 里面填充Table的列元素。	

2. sap.ui.table.Column

   > width : 宽度(100px)。
   >
   > label : 设置标题栏。
   >
   > template : 设置单元格的显示模板。
   >
   > sortProperty : 设置排序针对的字段。

##### sap.m.Table

1. sap.m.Table

   继承自sap.m.ListBase，用于显示包含行和列的表格式数据。表格的列可以通过`columns`聚合属性来设置，也可以使用`addColumn()`方法来添加。每一列都是`sap.m.Column`对象。

   使用`columns`聚合属性和`items`聚合属性，items属性实现的就是聚合绑定.。	

   sap.m.Table的重要属性：

   > `columns: `定义Table包含哪些列，类型是sap.m.Column数组。另外，sap.m.Table从sap.m.ListBase继承，所以可以直接使用sap.m.ListBase的属性。
   >
   > `growing: `设置Table显示的数据可以依据向model的请求增加行noDataText: 当Table没有数据的时候显示的文本，类型是string。
   >
   > `items: `sap.m.ListItemBase数组，sap.m.ListItemBase类定义了列表项(list item)的基本特征。

2. sap.m.ColumnListItem:

   `sap.m.ColumnListItem`用于创建`sap.m.Table`的行，行中包含的`cells`需要与`sap.m.Table`的Columns匹配，顺序一致。

   `sap.m.Table().bindItems("/modelData",oCllumnListItem); `绑定行

   `sap.m.Table().bindAggregation("items","/modelData",oCollumnListItem);`:聚合绑定。

   `oColumnListItem.addCell();`为Items添加元素。

   `sap.m.ColumnListItem`的press属性设置为一个数组，这种方法能够保证在Controller中，this表示Controller本身，而不是某个控件。
   
   > `vAlign: `行的垂直对齐：
   >
   > ​	`sap.ui.core.VerticalAlign.Bottom:`底部对齐
   >
   > ​	`sap.ui.core.VerticalAlign.Inherit:`从父控件继承
   >
   > ​	`sap.ui.core.VerticalAlign.Middle: `居中对齐
   >
   >  `sap.ui.core.VerticalAlign.Top:`顶部对齐

   `cells: `行包含的cells，每一个cell都是`sap.ui.core.Control`对象，从而开发人员可以根据需要选择合适的控件，灵活度很高。

```JS
 <ObjectIdentifier text="{ID}"></ObjectIdentifier> : cell元素。
```



### 主题设置

​    SAPUI5 默认提供了一些主题，并在组件添加新的主题。    

​        \- Blue crystal (常用)    

​        \- Platium     

​        \- Gold Reflection (常用)    

​        \- High Contast Black     

​        \- Belize (常用)    - …

​    设置主题：    

​        1.在Header中设置 : `data-sap-ui-theme="sap_bluecrystal"   ` 

​        2.在程序中设定样式 :` sap.ui.getCore().applyTheme(sThemeName, sThemeBaseUrl?)`

### 文件模块介绍

#### Index

```JS
<script id="sap-ui-bootstrap"
	src="https://openui5.hana.ondemand.com/resources/sap-ui-core.js"
	data-sap-ui-theme="sap_belize"
	data-sap-ui-libs="sap.m"
	data-sap-ui-compatVersion="edge"
	data-sap-ui-preload="async"
	data-sap-ui-bindingSyntax = "complex"
	data-sap-ui-resourceroots = '{"sFileSourceName":"oURL"}'>
</script>
```

​	src : 核心资源的加载路径。

​	data-sap-ui-theme : 设置主题

​	data-sap-ui-libs : 选择文件默认加载的库文件

​	data-sap-ui-compatVersion : edge兼容模式，为了方便使用新功能

​	data-sap-ui-preload : async 设置文件加载形式为异步加载

​	data-sap-ui-bindingSyntax : 数据绑定的设置 complex复杂绑定，对绑定数据进行计算。

​	data-sap-ui-resourceroots : 命名文件的根目录，声明资源文件位置

​	data-sap-ui-onInit="module:sNameSpace/index" : 定义初始化时加载的初始页面文件

```JSP
<script>
    sap.ui.localResources("ui5mvc");
    var app = new sap.m.App({initialPage:"idmain1"});
    var view = sap.ui.view({
        id:"idmain1", 
        viewName:"ui5mvc.main", 
        type:sap.ui.core.mvc.ViewType.JS});
    app.addPage(view);
    app.placeAt("content");
</script>
```
- `sap.ui.localResources("filename")` : 将当前目录下的FILE文件夹注册为当前文件夹，程序会在该文件夹下查找

  View和Controller代码。

- `sap.m.App` : 是SAP移动APP的root element,提供导航功能，并将一些header标签加到HTML页。

- `sap.ui.view` : 定义一个view,ID,name,type来指定显示的View。

  ​									 `sap.ui.xmlview`可直接定义xml类型View。View type: 

  - sap.ui.core.mvc.ViewType.JS       "JS"
  - sap.ui.core.mvc.ViewType.XML    "XML"
  - sap.ui.core.mvc.ViewType.JSON    "JSON"
  - sap.ui.core.mvc.ViewType.HTML   "HTML"

- `app.placeAt()` : 该方法将控件放到指定的div中。

#### View

1. view.js

```javascript
sap.ui.jsview("ui5mvc.main", {
	getControllerName : function() {
		return "ui5mvc.main";
	},
	createContent : function(oController) {
		var oShell = new sap.ui.ux3.Shell();// Create Shell

		oShell.addWorksetItem(new sap.ui.ux3.NavigationItem({
			key : "btn",
			text : "Button"
		}));// Add Navigation item

		oShell.addWorksetItem(new sap.ui.ux3.NavigationItem({
			key : "tf",
			text : "Textfield"
		}));

		oShell.addWorksetItem(new sap.ui.ux3.NavigationItem({
			key : "xml",
			text : "XMLView"
		}));

		var mContent = {}; // map holding shell content
		mContent.btn = new sap.ui.commons.Button({
			text : "Hello World"
		});

		mContent.tf = new sap.ui.commons.TextField();
		oShell.attachWorksetItemSelected(function(evt) {
			var key = evt.getParameter("key");
			oShell.setContent(mContent[key]);
		});// Add WorksetItem Selected event

		mContent.xml = sap.ui.view({
			viewName : "ui5mvc.XML",
			type : sap.ui.core.mvc.ViewType.XML
		});
	
		oShell.setContent(mContent.btn);//initial content
		return oShell;
	}
 });
```

`getControllerName`: 函数用于返回 controller name

`createContent`: 函数用于返回页面上要显示的元素

2. view.xml

```XML
<core:View xmlns:core="sap.ui.core"
	xmlns:mvc="sap.ui.core.mvc" xmlns="sap.m" controllerName="ui5mvc.XML"
	mlns:html="http://www.w3.org/1999/xhtml">
	<html:h1>My first XML-Header</html:h1>
	<Panel>
		<Button press=".sayHello" text="Say Hello"></Button>
	</Panel>
</core:View>
```
​		空的的namespace设定 : xmlns = "sap.m".

​		命名的namespace设定 : xmlns:mvc = "sap.ui.core.mvc". mvc标签指代`sap.ui.core.mvc`

- 申明namespace: `xmlns:t="sap.ui.table"`。xml中就可以表示为`<t:Table> ... </t:Table>`
- 如果属性是简单类型，可以直接作为atrribute的方式来申明，如Table的width属性、title属性
- 如果属性是Aggregation和Association，则使用子标签，如Column的Label，是`sap.m.Label`。
- 绑定的语法稍有差异。
- **`视图模型可以包含分配给控件的任何配置选项，以绑定属性。`**
- **`一个 ” . ”在格式化程序名称前面表示在当前视图的控制器中查找该函数`**

xmlView 聚合绑定	

​	1）xmlview中对需要动态显示的部分不作声明 

​	2）在controller中定义factory function，实现控件的绑定和动态加载。

#### Controller		

1. 系统生成的文件

```javascript
sap.ui.controller("ui5mvc.XML", {
// onInit: function() {
//		
// },

// onBeforeRendering: function() {
//
// },

// onAfterRendering: function() {
//
// },

// onExit: function() {
//
// }

sayHello : function() {
	sap.ui.commons.MessageBox.show("Hello World");
}
});
```
2. 通过模块定义

```JS
sap.ui.define(
	["Dependencies1","Dependencies2","formatter"],
	function(Controller,formatter){
	"use strict";
		return Controller.extend("ControllerName",{
            formatter:formatter,
            
			onInit:function(){},
               onBeforeRendering:function(){},
               onAfterRendering:function(){},
               onExit:function(){}
		});
	}
);
```

1. 生命周期：

​			Start  -->  视图和控制器被实例化  -->  控制器被加载(存在控制器) -->  onInit  -->  onBeforeRendering  --> 

​		视图被渲染  -->  onAfterRendering  -->  onExit  -->  END(视图和控制器被销毁)

- onInit : 当视图被实例化并且其控件已经创建时调用。用于在显示前修改视图，绑定事件处理程序并执行其他一次性初始化任务。

- onExit : 视图退出时调用。用于释放资源并完成任务。

- onAfterRendering : 当视图被渲染时调用;是HTML的一部分。用于执行HTML的后续操作，SAPUI5控制在渲染后访问此钩子。
- onBeforeRendering : 在控制器视图重新呈现之前调用，不在第一次呈现之前调用。用于在其中调用第一个渲染前的钩子。

2. 控制器只是将加载的格式化程序函数存储在本地属性格式化程序中，以便能够在视图中访问它们。

#### Model

应用程序数据

- 客户端 JSON model、XML model、Resource model
- 服务端 oData model

#### Resource

三种方法声明文件位置：

- `sap.ui.localResources()`

  ​	sap.ui.localResources("foldle.foldle");

- `jQuery.sap.registerModulePath()`

  ​	jQuery.sap.registerModulePath(sModuleNamePrefix, sURL);

- bootstrap声明 : `data-sap-ui-resourceroots = '{"sName":"oURL"}'`

   变更位置后需要修改系统自动生成的文件名称。

#### Component

Component.js通过调用manifest.json的配置信息，完成初始化调用。

```JS
sap.ui.define([
        "sap/ui/core/UIComponent",
        "sap/ui/model/resource/ResourceModel",
        "sap/ui/model/json/JSONModel"
        
    ], function (UIComponent, ResourceModel, JSONModel) {
    "use strict";

    return UIComponent.extend("webapp.Component", {
		//metadata
        metadata: {
            manifest: "json"
         },

        init : function () {
            // call the base component's init function
            UIComponent.prototype.init.apply(this, arguments);

            // create the views based on the url/hash
            this.getRouter().initialize();
        }
    });
});
```

#### Application Descriptor

​	manifest.json配置应用程序的相关信息。被称为Application Descriptor。

1. sap.app

   包含特定于应用程序的属性

   - ID(强制)：应用程序组件的命名空间，唯一的，必须与组件的空间名称对应

   - type：定义我们想要配置的内容，例如：应用程序

   - i18n：定义资源包文件的路径

   - title：应用程序资源包中引用的句柄语法中的应用程序标题

   - description：简短说明文本应用程序在应用程序资源包中引用的句柄语法中的作用

   - applicationVersion：应用程序的版本，以便以后可以轻松更新应用程序

2. sap.ui

   提供以下UI属性

   - technology：此值指定UI技术; 在我们的例子中，我们使用SAPUI5
   - deviceTypes：告诉应用程序支持哪些设备：台式机，平板电脑，手机（默认情况下均为true）

3. sap.ui5

   该 sap.ui5 namespace添加SAPUI5自动处理的SAPUI5特定配置参数。最重要的参数是：

   - rootView：如果指定此参数，组件将自动实例化视图并将其用作此组件的根
   - dependencies：这里我们声明应用程序中使用的UI库
   - models：在描述符的这一部分中，我们可以定义在应用程序启动时由SAPUI5自动实例化的模型。在这里，我们现在可以定义本地资源包。我们将模型“i18n”的名称定义为键，并按名称空间指定包文件。

##### 资源包文件

1. sap.app设置资源包文件的路径和文件名。使用的相对于 `manifest.json` 文件的相对路径。

   > "sap.app": {
   > 	 "_version": "1.1.0",
   > 	 "id": "resource",
   > 	 "type": "application",
   > 	 "i18n": "i18n/i18n.properties",
   > 	 ...
   >
   > },

2. sap.ui5中models设置名称为 i18n 的 **resource model**

   > "sap.ui5": {        
   >  	...
   >  	"models": {
   >  	    ...
   >  	    "i18n": {
   >  	        "type": "sap.ui.model.resource.ResourceModel",
   >  	        "settings": {
   >  	            "bundleName": "webapp.i18n.i18n"
   >                }
   >      }
   >
   > }

   `bundleName` 后面是根据 index.html文件的 **resource roots** 设置的相对路径。然后在代码中添加对 ResourceBundle 的依赖后，通过 `{i18n>xxx}` 实现绑定。

##### Models

1. sap.app设置资源包文件的路径和文件名。使用的相对于 `manifest.json` 文件的相对路径。

   ```JS
   "sap.app": {
       ...
       "dataSources": {
           "mainService": {
               "uri": "./service/data.json",
               "type": "JSON"
           }
       }
   ```

2. sap.ui5的`models`没有指定名称的 model，当 view 中数据绑定时，没有给出前缀的时候，就参照到这个 model。使用sap.app中设置的dataSource.

   ```JS
   "sap.ui5": {
           ...
           "models": {
               "": {
                   "dataSource": "mainService"
                },
           ...
           }
   ```

##### Root View

​	Root view (启动即显示的 view)：类型为 xml，名称为 App。OpenUI5 在相应文件夹下面查找名为 `App.view.xml` 文件并加载。通过这种方式，实现了 root view 的配置化.

> "sap.ui5": {
> 		"_version": "1.1.0",
> 		"rootView": {
> 			"viewName": "webapp.view.App",
> 			"type": "XML"
> 		}
>
> }

​	启动流程 : 

​	1) `index.html` 的 `ComponentContainer` 根据 `name` 或 `component` 属性实例化 Component。

​	2) Component 的 `metadata` 指向设定的 `manifest.json` 文件。

​	3) `manifest.json` 文件的 `sap.ui5>rootView` 设定了启动时候加载并显示的 root view 为 `App.view.xml` 。

​	4) App view 并不需要像之前文章介绍的内嵌 master view 和 detail view，而是由路由器根据路径在 pattern 中找匹配的模式，在 target 中找对应的 view 加载。

##### Routing设置

```JS
"sap.ui5": {
        ...
        "routing": {
            "config": {
                "routerClass": "sap.m.routing.Router",
                "viewType": "XML",
                "viewPath": "webapp.view",
                "controlId": "app",
                "controlAggregation": "pages",
                "bypassed": {
                    "target": "notFound"
                }
            },
            "routes": [{
                "pattern": "",
                "name": "master",
                "target": "master"
            },
            {
                "pattern": "detail/{supplierPath}",
                "name": "detail",
                "target": "detail"
            }],
            "targets": {
                "master": {
                    "viewName": "Master",
                    "viewLevel": 1
                },
                "detail": {
                    "viewName": "Detail",
                    "viewLevel": 2
                },
                "notFound": {
                    "viewName": "NotFound",
                    "viewId": "notFound"
                }
            }
        }
}
```



### 多语言

在 SAPUI5 中，通过两种方法来实现多语言.

​	(1) SAPUI5 提供 Resource Model，Resource Model 读取资源包 (Resource Bundle) 并与 View 中的控件绑定。

​	(2) 使用 jQuery.sap.resources 相关的 API 读取资源包。两种方法都需要资源包文件并且在配置中设置。

#### 语言代码

​	OpenUI5 对页面的显示，有一个 **当前语言( Current Language )** 的概念，按照当前语言，读取相应的资源包文件，按当前语言显示。OpenUI5 按照如下顺序顺序(从高到低)，如果都没有找到，最后读取通用设置（比如 i18n.properties)。

​	`sap.ui.getCore().getConfiguration().getLanguage()` 获得当前语言。

> 1) URL中的 locale 参数（即在 url 后面加上 `?sap-ui-language=en` ) 
>
> 2) 应用程序代码的 locale 设置，sap.ui.getCore().getConfiguration().applySettings({ language: 'de'});
>
> 3) Android 平台的用户代理字符串设置 
>
> 4) 浏览器的一般语言设置，可以用 window.navigator.language 查看 
>
> 5) 浏览器中用户语言配置。这个与浏览器相关，比如 IE 通过 window.navigator.userLanguage 查看。 
>
> 6) 浏览器语言配置。这个业余浏览器相关，比如 IE 通过 window.navigator.browserLanguage 查看 
>
> 7) OpenUI5中硬编码，默认为 en

#### 资源包文件

1. Java的属性文件，文件的扩展名总是.properties。文件中包含于语言相关的文本。
2. 文件名包括固定部分和语言相关部分。那么 i18n.properties 是默认的文件，i18n_zh_CN.properties 是中文简体的资源文件。
3. 资源包文件为扁平结构，不能嵌套。每一行要么是 key-value键值对 ，要么是 # 开头的注释。也可以可以空行。
4. 如果 Properties 文件的文本为 Unicode 字符，文件使用16进制的编码来存储，而不是明文。

#### Resource Model 

​	使用 Resource Model 绑定数据需要三步： 

​	1) 添加资源包文件，将不同的语言放在不同的资源包文件中。`i18n.properties,i18n_zh_CN.properties`

​	2) 在 Component.js 文件中，创建 Resource model 的实例 。

​	3) 在 View 中参照 Resource Model 中定义的 key。 ` title="{i18n>masterTitle}"`

​	 url 后面添加`?sap-ui-language=XXX`，实现语言的切换。

#### jQuery.sap.resources

​	在代码中直接使用资源包的文本。

```JS
var sLocale = sap.ui.getCore().getConfiguration().getLanguage();//获取当前语言
var oBd = jQuery.sap.resources({
	url: "i18n/i18n.properties",
	locale: sLocale
})               //根据指定的 URL 和 Locale，创建一个新的资源包实例
var sMeg = oBd.getText("key",[sLocale]); //根据资源包文件的 key，获取与语言相关的 value
```
