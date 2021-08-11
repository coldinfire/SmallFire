---
title: "Extjs 应用程序创建"
date: 2021-05-15
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - ExtJS

---

### Eclipse 配置 ExtJS



### 项目构建

将 Ext 开发包 copy 到 eclipse 项目的 /WebRoot 目录下：

   注意：不需要整个Ext开发包全部导入，这样很容易造成eclipse卡死，因为eclipse会自动检测js的合法性，会占用大量的检测时间、cpu和内存。通常普通的开发只需要用到\extjs-4.2.1\resources文件包、\extjs-4.2.1\ext-all.js这两个资源就可以,需要中文化再导入\extjs-4.2.1\locale\ext-lang-zh_CN.js 就可以了

 

### (3) 在web页面中通过<script>标签引入ext的库文件。(注意引入顺序)

  **Extjs4.0开始比以往有些变化，用起来麻烦不小。与以前的引入三个文件不同，现在的4.0只要引用两个文件就行了。**   

 

```
<link rel=``"stylesheet"` `type=``"text/css"` `href=``"extjs-4.2.1/resources/css/ext-all.css"``> ``// 引入样式文件 ``<script type=``"text/javascript"` `src=``"extjs-4.2.1/bootstrap.js"` `></script>  ``<script type=``"text/javascript"` `src=``"ext-4.2.1/locale/ext-lang-zh_CN.js"``></script>  ``// 中文化 
```

### (4) 调用Ext.onReady()初始化组件

   

```javascript
 <script>
   Ext.onReady(function(){
    …///在这里面创建及使用ext控件
    });
</script>
```

