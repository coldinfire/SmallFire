---
title: " SAPUI5 Routing 导航 "
date: 2019-04-24
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - SAP Fiori

tags: 
  - sapui5

---

## Routing导航

​	Openui5 的 routing 基于模式 ( pattern )，使用 `#` 符号表示不同的路径 ( route )，导航通过路径的改变来实现。

### Pattern 表达式

Openui5 一共有 5 种 pattern表达式:

1. **硬编码模式** : 页面之间根据模式导航，没有参数传递，比如 product/settings 表示导航到产品配置。
2. **路径含有必输参数模式** : 模式中 大括号({}) 包含的部分表示参数必须输入。比如 product/{id} 表示导航到产品某一 id，比如 product/5 表示 id 为 5 的产品，id 为必输。
3. **路径含有可选参数模式** : 模式中 冒号 包含的部分为必输参数。比如 product/{id}/detail/:detailId:，detailId 为可选参数。product/5/detail 以及 product/3/detail/2 都能与此模式匹配。
4. **路径含有查询参数模式** : 查询参数 ( query parameter ) 在问号之后。比如 product{?query}，query 这个参数为必输项。product:?query: 中的 query 这个参数为可选参数。
5. **通配参数模式**  :以星号结尾的参数是通配参数，通配参数将根据模式尽可能匹配。

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

