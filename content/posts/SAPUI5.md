---

title: " SAPUI5 "
date: 2019-04-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  Sap Fiori

tags: 
  - sapui5

---

## 概述

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
> ​	`sap.ui.core.VerticalAlign.Top:`顶部对齐
   >

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

Component

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



#### 多语言

在 SAPUI5 中，通过两种方法来实现多语言.

​	(1) SAPUI5 提供 Resource Model，Resource Model 读取资源包 (Resource Bundle) 并与 View 中的控件绑定。

​	(2) 使用 jQuery.sap.resources 相关的 API 读取资源包。两种方法都需要资源包文件并且在配置中设置。

##### 语言代码

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

##### 资源包文件

1. Java的属性文件，文件的扩展名总是.properties。文件中包含于语言相关的文本。
2. 文件名包括固定部分和语言相关部分。那么 i18n.properties 是默认的文件，i18n_zh_CN.properties 是中文简体的资源文件。
3. 资源包文件为扁平结构，不能嵌套。每一行要么是 key-value键值对 ，要么是 # 开头的注释。也可以可以空行。
4. 如果 Properties 文件的文本为 Unicode 字符，文件使用16进制的编码来存储，而不是明文。

##### Resource Model 

​	使用 Resource Model 绑定数据需要三步： 

​	1) 添加资源包文件，将不同的语言放在不同的资源包文件中。`i18n.properties,i18n_zh_CN.properties`

​	2) 在 Component.js 文件中，创建 Resource model 的实例 。

​	3) 在 View 中参照 Resource Model 中定义的 key。 ` title="{i18n>masterTitle}"`

​	 url 后面添加`?sap-ui-language=XXX`，实现语言的切换。

##### jQuery.sap.resources

​	在代码中直接使用资源包的文本。

```JS
var sLocale = sap.ui.getCore().getConfiguration().getLanguage();//获取当前语言
var oBd = jQuery.sap.resources({
	url: "i18n/i18n.properties",
	locale: sLocale
})               						 //根据指定的 URL 和 Locale，创建一个新的资源包实例
var sMeg = oBd.getText("key",[sLocale]); //根据资源包文件的 key，获取与语言相关的 value。

```



## Layout设置

### 多页面显示和跳转

​	sap.m.App : 是一个全局对象，可以通过 app.to(sPageId) 跳转到另一个页面。

​		to(sPageId, sTransitionName*?*, oData*?*, oTransitionParameters*?*): [sap.m.NavContainer]

​	app.back()则跳回到刚才的page：

​		back(oBackData*?*, oTransitionParameters*?*): [sap.m.NavContainer]

​	`sap.m.Page`中，`showNavButton`设置为`true`，就会出现Navigation按钮，点击按钮的event hander通过Controller中`onNavPress`函数来实现。

```js
	var oDetailPage = new sap.m.Page({
        showNavButton: true,
        navButtonPress: [oController.onNavPress, oController],
        title: "供应商明细",
        content: [oObjectHeader]
    });
```
​	`sap.m.ColumnListItem`的type必须为Navigation，否则不能实现跳转。`sap.m.ColumnListItem`的press属性设置为一个数组，这种方法能够保证在Controller中，this表示Controller本身，而不是某个控件。

### 布局类型

​	sap.ui.layout.HorizontalLayout() : 水平布局

​	sap.ui.layout.VerticalLayout() : 垂直布局

​	sap.ui.layout.Grid() : 表格式布局

#### Grid Layout

​	Grid Layout 控件负责将页面进行表格式布局，将页面分为 12 列，子控件从左至右排列。每个控件并不是占一列，OpenUI5 根据屏幕的大小，将屏幕分为 4 种，分别是`XL: extra large 、 L: large、M: medium、S: small `。XL的如 PC 机的大桌面，L的如 PC 的桌面，M的比如平板，S的比如手机。默认情况下，每个控件在 XL 桌面上占 3 列，在 L 桌面上占 3 列，在 M 桌面上占 6 列，在 S 桌面上占 12 列。OpenUI5 用一个字符串表示为` XL3 L3 M6 S12`，通过 default Span 属性来设置。

​    当屏幕的尺寸变更的时候，OpenUI5 检测到尺寸的变化，根据上面的 4 个分类对控件的位置进行调整，从而实现所谓的自适应。

​    Grid layout 控件宽度 (Width)，可以基于像素，或者基于页面宽度的相对比例。控件之间的间距可以通过 vSpacing 和 hSpacing 属性进行设置。

   ` sap.ui.layout` : 该function可以对Layout页面布局进行设置。

​    	`new sap.ui.layout.Grid({ content: [ a1,b1,c1,d1] }).placeAt("content");`

​    将各个空间放到Layout.Grid中，然后将该Grid放到页面指定位置上。

### LayoutData 属性 

​      `sap.ui.core.Element` 类定义了` layoutData` 属性、`getLayoutData()` 方法和` setLayoutData()` 方法。控件都是 `sap.ui.core.Element` 类的间接子类，从而控件都可以利用这些属性和方法设定这个控件在页面中如何定位。`setLayoutData()` 方法的参数是` sap.ui.core.LayoutData` 对象。 Grid 布局时`layoutData` 我们可以用`sap.ui.core.LayoutData` 类的子类 `sap.ui.layout.GridData`。

```JSP
var oLabel2 = new sap.m.Label({
   text : "XXXXXXXXXXXXXXXXXXX",
   layoutData : new sap.ui.layout.GridData({
     span : "XL12 L12 M12 S12"       //通过该设置让该元素独占屏幕元素的一整行
   })
});
```

####   Margin Classes

​	在class属性中设置了四种标准的大小: tiny-8px、small-16px、medium-32px、large-48px， Begin is left and End is right。

   在 div 的 class 属性中添加对应的属性值来解决边距问题。

1. Full Margins : all around control

   - sapUiTinyMargin

   - sapUiSmallMargin

   - sapUiMediumMargin

   - sapUiLargeMargin

2. Single-sided margins : class中只能设定一个边框距离

   - sapUixxxxMarginTop

   - sapUixxxxMarginBottom

   - sapUixxxxMarginBegin

   - sapUixxxxMarginEnd

3. Two-Sided Margins : 两个方向

   - sapUiTinyMarginBeginEnd

   - sapUiTinyMarginTopBottom

4. Responsive Margins : margins depending on the screen width 

   sapUiResponsiveMargin

   > <Panel headerText="{i18n>helloPanelTitle}"
   > 	   class="sapUiResponsiveMargin"
   > 	   width="auto">
   > </Panel>
   >
   > <body class="sapUiBody sapUiResponsiveMargin" role="application">
   >
   > ​		<div id="content"></div>
   >
   > </body>

5. 100%宽度控制

   如果控件包含有`width`属性，设置该属性为`width=auto`.如果没有该属性，可以添加 sapUiForceWidthAuto属性到控件的class.

6. 移除Margins

   - sapUiNoMarginTop
   - sapUiNoMarginBottom
   - sapUiNoMarginBegin
   - sapUiNoMarginEnd
   

#### 自定义CSS和主题颜色

​	不要在自定义CSS中指定颜色，而是使用标准的主题依赖类。

### 对象组件显示

1. 组件

   > `sap.m.ObjectIdentifier:` 用于需要对操作对象进行明确区分的时候，使用这个组件进行显示。强调的是标识。				title属性是主要标识，text属性是补充，如果titleActive为true，则标题用颜色标识.
   >
   > `sap.m.ObjectNumber:` 显示数字,能根据不同的状态提供颜色区分。有四种state: Warning, Error, Success,Default.
   >
   > `sap.m.ObjectMarker:` 以图标的方式显示预定义的几种类型，可以绑定press事件。包括 [Flagged、Favorite、Draft
   >
   > 、Locked、LockedBy、Unsaved、UnsavedBy].
   >
   > `sap.m.ObjectAttribute:` 显示对象，并提供普通和active两种状态，active状态可与事件绑定。
   >
   > `sap.m.ObjectStatus:` 显示对象的文本，并且根据状态不同，文本以不同的颜色区分。
   >
   > `sap.m.ObjectHeader:` 显示对象，包括标识和附加的信息，图标等。



## Dialogs and Fragments

### Fragments

1. 片段是一个轻量级的Ui部分，它只是一组重用控件的容器。包含一到多个控件，不需要控制器。

2. 定义并调用已存在的 Fragments : "sap/ui/core/Fragment"

   1) 定义xxx.fragment.xml

   ```xml
   <core:FragmentDefinition xmlns="sap.m" xmlns:core="sap.ui.core">
   	<Dialog id="helloDialog" title="Hello {/recipient/name}">
   		<content>
   			<core:Icon src="sap-icon://hello-world" size="80px" class="sapUiMediumMargin">					 </core:Icon>
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

1. 不属于特定视图，不能将其定义为视图，这意味着必须在控制器代码中的某处实例化对话框。

2. 重用

   1) 定义单独的控制来实现Dialog的创建 HelloDialog.js

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

   2) 在Component.js文件中声明该控件为私有属性,并封装其方法

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
   		 * The component is initialized by UI5 automatically during the startup of the app and calls the init method once.
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

3. 在按钮事件中通过`this.getOwnerComponent().openHelloDialog()`调用

   onOpenDialog方法现在通过调用辅助方法getOwnerComponent来访问其组件。当调用重用对象的open方法时，我们传入当前视图以将其连接到对话框。

4. Attention

   将跨多个控制器使用的所有资产放在单独的模块中

## Icons

sap.ui.core.Icon



## 数据类型和操作

### 基本数据类型

> ​	sap.ui.model.type.Integer(oFormatOptions?, oConstraints?):支持minimum,maximum
>
> ​	sap.ui.model.type.Float(oFormatOptions?, oConstraints?):`decimalSeparator`定义小数位的分隔符
>
> ​	sap.ui.model.type.String(.........)
>
> ​	sap.ui.model.type.Boolean
>
> ​	sap.ui.model.type.Date : ui5支持原数据为JavaScript和原数据为String的日期数据进行格式输出
>
> ​	sap.ui.model.type.Time : Time也支持原数据为Time类型或者字符串类型
>
> ​	sap.ui.model.type.DateTime



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

1. 基本使用

   ​	sap.ui.core.message.MessageManager();

   ​	registerObject(oObject,bHandelValidation) : 第一个参数是ManagedObject对象的实例，第二个参数是boolean类型变量，为true时执行数据校验。

   ​	attachValidationError(this,function(){}) : 控件都有该方法，用于校验失败时的处理。

   ​	attachValidationSuccess(this,function(){}) : 用于校验成功时的处理。

   ValueState 种类:setValueState()

   ​	sap.ui.core.ValueState.Error

   ​	sap.ui.core.ValueState.None

   ​	sap.ui.core.ValueState.Success

   ​	sap.ui.core.ValueState.Warning	

2. 集中处理数据校验:

   sap.ui.core.Core也可添加attachValidationError().

3. 自定义数据类型校验:

   sap.ui.model.SimpleType.extend()自定义数据类型，可以使用formatValue(),parseValue(),validateValue()实现自定义的校验规则和提示消息。

   抛出异常信息：throw new sap.ui.model.ValidationException("Message");

   截取异常消息，使用该类型的控件通过`oEvent.getParameter("message")`获取该错误消息。

   

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


## 消息设置

### sap.m.MessageBox

​    SAPUI5 提供的对话框，可以显示信息、警告、错误等等。MessageBox 类是静态类，在使用之前必须执行 `jQuery.sap.require("sap.m.MessageBox")` 语句 SAPUI5 包含 jQuery 包，`jQuery.sap.require(vModuleName)` 方法的作用是加载指定的模块并且执行，这样 MessageBox 的 show() 方法才能运行。

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



## 模块化

1. 如何加载模块

   > `jQuery.sap.declare(sModuleName,bCreateNamespace)`申明一个模块，以确保模块存在。这个语句必须出现在	模块代码（也就是代码文件)的第一句。
   >
   > `jQuery.sap.require(vModuleName)`确保当前代码继续之前，所指定的模块被加载和执行。如果所需要的模块没有被加载，将会被同步加载和执行，如果已经加载，就忽略。
   >
   > `sap.ui.define(sModuleName,aDependencies,vFactory,bExport)`定义module，异步加载依赖模块,sap.ui.define()定义的模块具有全局命名空间。2:定义依赖 3:继承工厂函数。
   >
   > `sap.ui.require()`异步加载依赖的模块，不具有全局命名空间。

2. 使用模块方法实现Controller

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

## Model Binding

### 单数据绑定

1. 使用数据绑定前，需要先实例化Model.构造函数获取实例的URL或则数据本身作为第一个参数。

   ​    JSON-Model:

   ​        `var oModel = new sap.ui.model.json.JSONModel(dataUrlOrData);`

   ​    XML-Model:

   ​        `var oModel = new sap.ui.model.xml.XMLModel(dataUrlOrData);`

   ​    OData-Model:

   ​        `var oModel = new sap.ui.model.odata.ODataModel(dataUrl[,userJSON,user,pass]);`

2. 给Model设置值

   oModel.setData(data);  绑定定义的数据

   oModel.loadData("models/suppliers.json"); 从文件中读取数据绑定

3. 将Model分配给Core或则其他的控制器（setModel）方法。

   - Global model:

   ​        `sap.ui.getCore().setModel(oModel) ` : 这样oModel对整个应用程序可见

   - Bind a model to a view

   ​       `var oView = sap.ui.view({type:sap.ui.core.mvc.ViewType.JS,viewName:"text.view"})`          

   ​	 `oView.setModel(oModel);`

   - Bind a model to a specific control

   ​        `var oTable = sap.ui.getCore().byId("table");`

   ​        `oTable.setModel(oModel);`

4. Model属性绑定方法（{ } curly braces，bindProperty()）

   ​    控件的大多数属性都可以绑定到模型属性。

   - bindProperty  method: [extend : sap.ui.base.ManagedObject]

   ​        `oControl.bindProperty("sName","oBindingInfo");`

   ​			oBindingInfo attributes : path、model、formatter等

   ​				path : 指定绑定的数据路径

   ​				model : sap.ui.model.BindingMode.OneWay、sap.ui.model.BindingMode.TwoWay

   ​				formatter : 
   
   - 花括号:{ }
   
   ​        `var oControl = new sap.ui.commons.TextView({controlProperty:"{/modelProperty}"});`
   
   - alternatively：
   
   ​        `var oControl = new sap.ui.commons.TextView({controlProperty:{path:"/modelProperty"}});`
   
5. Model属性的获取。

   - oModel.getProperty("/sName");    根据JSON数据属性名获取对应的值

### 多数据绑定 

​    用于绑定集合数据，如绑定多行数据到一个表格中。使用模板:所有行都用同样的方法显示数据。

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

  oTable.bindAggregation("items","/modelData",function(sId,oContext){

  ​	return oColumnListItem;	//通过工厂函数，定义数据并返回

  });

### 元素绑定

​	元素绑定指根据上下文(binding context)使用相对绑定的方式绑定到model数据的某一具体对象。尤其适用于**主从数据显示(master-detail data)**的情况。

​	sap.m.List(sId?, mSettings?) : List控件适用于显示行项目，所有类型都可以。

​	sap.m.ObjectListItem(sId?, mSettings?) : 适用于显示行项目的信息，主要使用**title**属性进行标识，text、icon、atrributes和statuses等属性可以用于提供对象更多信息。

​	sap.m.Panel().bindElement({path:sPath}) : 将显示的明细与Model绑定。

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

   将value属性绑定到json model根目录下对应的字段title/attr。

2. 相对绑定

   **相对绑定尤其适用于布局(layout)控件或者容器控件**

   当父控件的绑定路径设置后，子控件可以基于这个路径使用相对路径。

## Routing导航

​	Openui5 的 routing 基于模式 ( pattern )，使用 `#` 符号表示不同的路径 ( route )，导航通过路径的改变来实现。

### Pattern 表达式

Openui5 一共有 5 种 pattern表达式:

1. **硬编码模式** : 页面之间根据模式导航，没有参数传递，比如 product/settings 表示导航到产品配置。

2. **路径含有必输参数模式** : 模式中 大括号({}) 包含的部分表示参数必须输入。比如 product/{id} 表示导航到产品某一 id，比如 product/5 表示 id 为 5 的产品，id 为必输。

3. **路径含有可选参数模式** : 模式中 冒号 包含的部分为必输参数。比如 product/{id}/detail/:detailId:，detailId 为可选参数。product/5/detail 以及 product/3/detail/2 都能与此模式匹配。

4. **路径含有查询参数模式** : 查询参数 ( query parameter ) 在问号之后。比如 product{?query}，query 这个参数为必输项。product:?query: 中的 query 这个参数为可选参数。

5. **通配参数模式 **: 以星号结尾的参数是通配参数，通配参数将根据模式尽可能匹配。

### 导航调用

1. 父导航

   1) 跳转到Detail view子导航

   2) 向Detail view传递一个参数，参数为当前点击的路径，Detail获取该路径完成数据绑定

   - var oRouter = UIComponent.getRouterFor(this);获取当前的router

   - var oItem = oEvent.getSource();获取点击所在的行

   - oItem.getBindingContext().getPath();获取点击的路径，String类型（/Sup/0）路径传到Detail

   - oRouter.navTo("detail",{supplierPath:encodeURIComponent(sPath)});方法不能包含`/`所以使用 `encodeURIComponent()` 函数编码，在Detail controller 中用`decodeURIComponent()`函数解码。

2. 子导航

   1) 获取 Master view 传递的路径，根据此路径完成 element binding。比如当 Master view 传过来 `/Suppliers/0`，则与第一条数据绑定;

   2) 根据页面之间的关系，当点击 **返回** 按钮时，返回到上一个页面。

   - `var oRouter = UIComponent.getRouterFor(this);`获取当前Router

   - `oRouter.getRoute("detail").attachPatternMatched(this._onObjectMatched, this);`，当模式匹配时，附加事件处理器为 `_onObjectMatched`。然后在 `_onObjectMatched` 中获取 Master view 传递的路径并绑定数据。

     ```JS
     _onObjectMatched: function (oEvent) {           
         var sPath = decodeURIComponent(
                 oEvent.getParameter("arguments").supplierPath);
         this.getView().bindElement({path: sPath});
     }   
     ```

   - 当用户点击导航按钮，判断是否有上一个路径 ( previous hash )，如果有就返回上一个路径，否则跳转到 Master view:

     ```JS
     onNavPress: function() {
         var oHistory = History.getInstance();
         var sPreviousHash = oHistory.getPreviousHash();
         
         if (sPreviousHash != undefined){
             window.history.go(-1);
         }else{
             var oRouter = UIComponent.getRouterFor(this);
             oRouter.navTo("master",{}, true);
         }
     }
     ```



## mock server

​	在开发过程中，通过使用模拟服务器的方法方便测试，SAPUI5将模拟服务器称为mock server.mock server的基本功能是模拟oData数据的提供者，截获应用程序对服务器端的http或https请求，并传回模拟请求的回应，可以降低与真实后端的耦合。





# 系统配置和功能块

## SAP NetWeaver Gateway

1. SAP NetWeaver Gateway 是一种技术，它提供了一种基于市场标准将设备，环境和平台连接到 SAP 软件的简单方法。

   > 任何SAP业务套件都是无中断的
   >
   > 易于开发简单的API,不需要任何工具知识
   >
   > 基于REST,oData。允许使用功能任何编程语言或模型连接到SAP应用程序

   

2. 将SAP NetWeaver Gateway 连接到 SAP Business Suite

   1) 将后端服务器配置为信任系统 : SM59

   ![1559703243928](/images/SAPUI5/1559703243928.png)

   ​          		![1559712968906](/images/SAPUI5/1559712968906.png)    

   2) SMT1

   ​             	![1559713194355](/images/SAPUI5/1559713194355.png)

3. SAP NetWeaver Gateway部署选项

   1) 中央枢纽部署 : 后端系统的开发

   ​	在此类部署选项中，中央 UI 附加组件，特定于产品的 UI 附加组件和 SAP NetWeaver 网关包含在 ABAP 前端服务器中。后端服务器包含业务逻辑和后端数据。开发在 ABAP 后端系统中进行。

   - 它需要单独的 SAP NetWeaver Gateway 系统

   - 它允许在没有后端开发授权的情况下更改 UI。
   - 它为所有 UI 问题提供单点维护。
   - 它为 Fiori Apps 的主题和品牌提供了中心位置。
   - 它提供对后端系统的单点访问。
   - 由于无法直接访问后端系统，因此增强了安全性。
   - 直接本地访问元数据（DDIC）和业务数据以及轻松重用数据。

   2) 中央集线器的部署

   ​	如果必须在后端系统上执行开发，或者在 7.40 之前的版本中执行开发，则使用此选项。如果不允许在**后端**部署 Add-On **IW_BEP**。在这种情况下，开发人员仅限于可通过后端 RFC 访问的接口。

   ​	开发在 Gateway 集线器系统中进行，并且不触及 Business Suite 后端系统。

   - 无法直接访问**元数据（DDIC）**和业务数据。因此，数据的重用是有限的。
   - 无法远程使用 GENIL 对象。
   - 在此配置中，访问仅限于远程启用的接口，如 RFC 模块，BAPI 等。

## oData(开放数据协议)

1. 概述: OData 用于定义构建和使用 RESTful API 所需的最佳实践

   - OData 提供扩展功能，以满足 RESTful API 的任何自定义需求。
   - REST 代表 Representational State Transfer。
   - 它依赖于无状态，客户端 - 服务器，可缓存的通信协议。几乎在所有情况下，都使用 HTTP 协议。
   - REST 被定义为用于设计网络应用程序的体系结构样式。
   - OData 可帮助您在构建 RESTful API 时专注于业务逻辑，而无需担心定义请求和响应头，状态代码，HTTP 方法，URL 约定，媒体类型，有效负载格式和查询选项等的方法。

2. oData服务生命周期

   OData 服务生命周期包括 OData 服务的范围。

   - 激活 OData 服务。
   - 维护 OData 服务。
   - 维护模型和服务，直至清理元数据缓存。
   - RESTful 应用程序使用 HTTP 请求发布数据以创建或更新，读取数据和删除数据。REST 对所有四个 CRUD（创建 / 读取 / 更新 / 删除）操作使用 HTTP。
   - REST 是 RPC（远程过程调用）和 Web 服务等机制的轻量级替代方法。
   
3. oData设置

   在manifest.json中配置服务器：

   ```js
   "sap.app": {
   	...
   	"ach": "CA-UI5-DOC",
   	"dataSources": {
   	  "invoiceRemote": {
   		"uri": "https://services.odata.org/V2/Northwind/Northwind.svc/",
   		"type": "OData",
   		"settings": {
   		  "odataVersion": "2.0"
   		}
   	  }
   	}
    "sap.ui5": {
   	...
   	"models": {
   	  ...
   	  "invoice": {
   		"dataSource": "invoiceRemote"
   	  }
   	}
   ```

## SAP Fiori Launchpad 

1. 关于 SAP Fiori Launchpad 的要点如下。

   - 基于 Web 的入口点，可跨平台和设备使用 SAP Business 应用程序。

   - 作为 I HTML 客户端的开箱即用思想提供。

   - 使用主题，搜索集成，自定义等功能为最终用户提供高生产率。

   - 为使用多种设备类型的最终用户提供单一入口点。