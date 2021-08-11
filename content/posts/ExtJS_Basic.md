---
title: "ExtJS 基础知识"
date: 2021-05-09
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - ExtJS

---

### Ext JS 简介

Ext JS 是一个 JavaScript 框架，它具有面向对象编程的功能。Ext JS 是一个“JavaScript 优先”的框架。 也就是说，用户界面及其逻辑都是在 JavaScript 中定义的。 各种包含的主题（例如 Material）提供了必要的样式。

Ext JS 为使用跨浏览器功能构建 Web 应用程序提供了丰富的 UI。 Ext JS 基本上用于创建桌面应用程序，它支持所有现代浏览器。Ext JS 提供对 MVC 和 MVVM 应用程序架构的支持。

**Ext 是封装 Ext JS 中所有类的命名空间。**

#### MVC  (Model-View-Controller)

在 MVC 架构中，大多数类是模型、视图或控制器。用户与视图交互，视图显示模型中保存的数据。这些交互由控制器监控，然后控制器根据需要通过更新视图和模型来响应交互。

- Model(模型) ：用于封装与应用程序的业务逻辑相关的数据以及对数据的处理方法。一个数据字段的集合，可通过关联被链接到其他模型和通过代理链接到一个数据流。

- View(视图)：表示数据给用户。任何类型的组件，这些组件将信息输出到浏览器，如Form、Grid、Chart。

- Controller(控制器)：是 MVC 应用程序的逻辑部分。它处理事件并作出响应。“事件”包括用户的行为和数据 Model 上的改变。Ext.app.Controller 为控制器的基类。

View 和 Model 通常不知道彼此，因为 Controller 全权负责指导更新。一般来说，控制器将包含 MVC 应用程序中的大部分应用程序逻辑。理想情况下，视图几乎没有（如果有）业务逻辑。模型主要是数据的接口，并包含管理对所述数据的更改的业务逻辑。

MVC 的目标是明确定义应用程序中每个类的职责。因为每个类都有明确定义的职责，所以它们隐含地与更大的环境分离。这使得应用程序更易于测试和维护，并且其代码更易于重用。

#### MVVM

MVC 和 MVVM 之间的主要区别在于 MVVM 具有称为 ViewModel 的视图抽象。ViewModel 使用一种称为“数据绑定”的技术来协调 Model 的数据和 View 对该数据的表示之间的变化。

- (VM) ViewModel - ViewModel 是管理特定于视图的数据的类。它允许感兴趣的组件绑定到它并在此数据更改时进行更新。

结果是模型和框架执行尽可能多的工作，最小化或消除直接操作视图的应用程序逻辑。

#### Ext.js 命名规范

Ext JS中的命名约定遵循标准 JavaScript 约定，这不是强制性的，而是遵循的良好做法。它应该遵循 Camel case 语法命名类，方法，变量和属性。如果 name 与两个单词组合，则第二个单词将始终以大写字母开头。

| 名称          | 惯例                                                         |
| :------------ | :----------------------------------------------------------- |
| Class Name    | 以大写字母开头，然后是camel case E.g. StudentClass           |
| Method Name   | 以小写字母开头，然后是camel case E.g. doLayout()             |
| Variable Name | 以小写字母开头，然后是camel case E.g. firstName              |
| Property Name | 以小写字母开头，然后是camel case E.g. enableColumnResize = true |
| Constant Name | 只有大写，E.g. COUNT, MAX_VALUE                              |

### 在Ext JS中定义类

**Ext.onReady** 是指在整个页面加载完后执行。

```ejs
<script type="text/javascript">
  Ext.onReady(function(){  
  //页面初始化代码
  });
</script>
```

**Ext.define()** 用于在 Ext JS 中定义类。

`Ext.define(class_name, class_members/properties, callback_function);`

- 类名称：是根据应用程序结构的类名称 - appName.folderName.ClassName
- 类属性/成员：定义类的行为。
- 回调函数是可选的：当类正确加载时，会调用它。

Ext JS 定义类

```ejs
Ext.define("Person", {
    //定义、初始化成员属性
    name: '',
    age: 0,
    //定义成员方法
    say: function (msg) {
        Ext.Msg.alert(this.name + " Says:", msg);
    },
    //定义构造函数
    constructor: function (name, age) {
        this.name = name;
        this.age = age;
    }    
});
```

#### 创建对象

由两种方式可以创建对象：

- 使用 new 关键字

  - var studentObject = new Person();

- 使用 Ext.create() 方法创建

  - ```javascript
    Ext.create('Ext.Panel', {
        renderTo: 'helloWorldPanel',
        height: 200,
        width: 600,
        title: 'Hello world',
        html: 'First Ext JS Hello World Program'
    });
    ```

#### 继承

ExtJS 允许对现有的类进行扩展，其扩展可以通过继承来实现。

在Ext JS继承可以使用两种方法：

- Ext.extend

  - ```javascript
    //基类
    Ext.define("Developer", {
        extend: 'Person',        //继承
        coding: function (code) {
          Ext.Msg.alert(this.Name + " 正在编码..", code);
        },
        constructor: function(){
            this.callParent(arguments);
        }
    });
    ```

- 使用Mixins : Mixins是在没有扩展的情况下在类B中使用类A的不同方式。

  - ```javascript
    mixins : {
       commons : 'DepartmentApp.utils.DepartmentUtils'
    },
    ```

在构造函数中，通过调用 this.callParent()方法，实现对属性的初始化。如果此处只调用了父类的构造方法，则可以省略掉，ExtJS 会自动为我们调用父类的构造函数。

如果在子类中使用了自定义的构造函数，ExtJS 则不会再自动调用父类的构造函数。

像java导包一样，在html中要导入父类的js文件。

### 类的选项 - config

可以在定义类的时候为它添加配置项——**config,它是Extjs的属性包装器，为属性提供get和set方法。**

```javascript
Ext.onReady(function(){
    Ext.define("Person", {
        config: {
            name: '',
            age: 0
        },
        say: function (msg) {
            //在方法中，“this.config.属性名”和“this.属性名”的形式都可以调用成员属性。
            Ext.Msg.alert(this.config.name+","+ this.age + " Says:", msg);
        },
        constructor: function (config) {
            this.initConfig(config);
        }
    });
    //注意：因为在类中将成员属性放进了config中，因此已不能用new的方式进行实例化了。
    //需要使用Ext的create方法创建并返回一个实例:
    var Tom = Ext.create("Person", {
        Name: 'Tom',
        Age: 26
    });
    Tom.Say("Hello");
    
    //使用Ext自动生成的set、get方法设置、访问实例属性
    //使用“Tom.config.Age”、“Tom.Age”都可以得到Age的值
    Tom.setAge(18);      //注：“Tom.Age=18”也可以——这是js赋值的方法
    alert(Tom.getAge()); //注：“Tom.Age”也可以——这是js取值的方法
})
```

在类的定义中添加了config项，将需要在配置中完成的属性添加在里面，而在构造函数中，我们通过调用this.initConfig方法完成对config的初始化；

在定义Person类对象的时候，使用Ext.create方法，传入类名Person以及配置项。

### 容器

Ext JS 中的容器是我们可以添加其他容器或子组件的组件。这些容器可以具有多个布局以将部件布置在容器中。 我们可以从容器和其子元素添加或删除组件。 

`Ext.container.Container` 是 Ext JS 中所有容器的基类。

#### 常用容器列表

| Type                   | Desc                                                         |
| :--------------------- | :----------------------------------------------------------- |
| Ext.panel.Panel        | 这是允许在正常面板中添加项目的基本容器                       |
| Ext.form.Panel         | Form面板为表单提供了一个标准容器，它本质上是一个标准的Ext.panel.Panel，它自动创建一个用于管理任何Ext.form.field.Field对象的BasicForm。 |
| Ext.tab.Panel          | 标签面板就像一个普通的面板，但支持卡标签面板布局             |
| Ext.container.Viewport | Viewport是一个容器，它会自动调整大小到整个浏览器窗口的大小。 然后，您可以在其中添加其他ExtJS UI组件和容器 |

Ext.panel.Panel & Ext.form.Panel

```javascript
<script type="text/javascript">
    Ext.onReady(function () {
        var child1 = Ext.create('Ext.Panel', {
            title:'Child Panel1',
            html: 'Text field1'
        });
        var child2 = Ext.create('Ext.Panel', {
            title:'Child Panel2',
            html: 'Text field2'
        });
        Ext.create("Ext.panel.Panel", {
            renderTo: Ext.getBody(),
            width: 100,
            height: 100,
            border: true,
            frame: true,
            items: [child1, child2]
        });
        Ext.create('Ext.form.Panel', {
            renderTo: Ext.getBody(),
            width: 100,
            height: 100,
            border: true,
            frame: true,
            layout: 'auto',// auto is one of the layout type.
            items: [child1, child2]
        });
    });
</script>
```

Ext.tab.Panel

```javascript
<script type="text/javascript">
    Ext.onReady(function () {
        Ext.create('Ext.tab.Panel', {
            renderTo: Ext.getBody(),
            height: 100,
            width: 200,
            items: [{
               xtype: 'panel',
               title: 'Tab One',
               html: 'The first tab',
               listeners: {
                  render: function() {
                     Ext.MessageBox.alert('Tab one', 'Tab One was clicked.');
                  }
               }
            },{
               // xtype for all Component configurations in a Container
               title: 'Tab Two',
               html: 'The second tab',
               listeners: {
                  render: function() {
                     Ext.MessageBox.alert('Tab two', 'Tab Two was clicked.');
                  }
               }
            }]
         });
    });
</script>
```

Ext.container.Viewport  

```javascript
<script type="text/javascript">
    Ext.onReady(function () {
        var childPanel1 = Ext.create('Ext.panel.Panel', {
            title: 'Child Panel 1',
            html: 'A Panel'
        });
        var childPanel2 = Ext.create('Ext.panel.Panel', {
            title: 'Child Panel 2',
            html: 'Another Panel'
        });
        Ext.create('Ext.container.Viewport', {
        renderTo: Ext.getBody(),
            items: [ childPanel1, childPanel2 ]
        });
     });
</script>
```

