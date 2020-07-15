---
title: " SAPUI5 Layout  "
date: 2019-04-19
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  Sap Fiori

tags: 
  - sapui5

---

## Layout设置

### 多页面显示和跳转

​	sap.m.App : 是一个全局对象，可以通过 app.to(sPageId) 跳转到另一个页面。

​		to(sPageId, sTransitionName *?* , oData *?* , oTransitionParameters *?* ): [sap.m.NavContainer]

​	app.back()则跳回到刚才的page：

​		back(oBackData *?* , oTransitionParameters *?* ): [sap.m.NavContainer]

​	`sap.m.Page`中，`showNavButton`设置为`true`，就会出现Navigation按钮，点击按钮的event hander通过Controller中`onNavPress`函数来实现。

```js
var oDetailPage = new sap.m.Page({
  showNavButton: true,
  navButtonPress: [oController.onNavPress, oController],
  title: "供应商明细",
  content: [oObjectHeader]
});
```

`sap.m.ColumnListItem`的type必须为Navigation，否则不能实现跳转。`sap.m.ColumnListItem`的press属性设置为一个数组，这种方法能够保证在Controller中，this表示Controller本身，而不是某个控件。

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

#### Margin Classes

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

   ```js
   <Panel headerText="{i18n>helloPanelTitle}"
   	   class="sapUiResponsiveMargin"
   	   width="auto">
   </Panel>
   <body class="sapUiBody sapUiResponsiveMargin" role="application">
   	<div id="content"></div>
   </body>
   ```

   

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

## Icons

sap.ui.core.Icon