---
title: " HTML DOM事件 "
date: 2017-09-25
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - JS

---

### HTML DOM 事件

当某些组件被执行了某些操作后，触发指定的执行程序。

- 事件：某些操作,如鼠标和键盘等操作
- 事件源：组件
- 监听器：程序代码
- 注册监听：将事件，事件源，监听器结合在一起

#### DOM 事件模型

冒泡型事件：事件按照从最特定的事件目标到最不特定的事件目标的顺序触发  

捕获型事件：与冒泡事件相反的过程，事件从最不精确的对象开始触发，然后到最精确 

```html
<body onclick="handleClick()">  
  <div onclick="handleClick()">Click Me</div>  
</body> 
冒泡触发的顺序是：div、body、html、document、window
捕获型触发的顺序：document、div
```

HTML DOM 事件允许 Javascript 在 HTML 文档元素中注册不同事件处理程序。

事件通常与函数结合使用，函数不会在事件发生前被执行。 

#### 事件绑定

Method 1：在标签上事件中定义

- `<img id="" src="" onclick="fun1()">`

Method 2：在JavaScript中绑定

```html
<script>
    function fun1(){
        alert('function');
    }
    var obj = document.getElementById("id_value");
    obj.onclick = fun1;
</script>
```

### 事件类型

#### 加载完成事件

`window.onload` 当所有的页面加载完成后，才触发 javascript 代码。

```javascript
window.onload = function(){
  document.getElementById().onblure = function(){
    alert("失去焦点");
  }
}
```

#### 鼠标事件

| Event         | Desc                                   |
| :------------ | :------------------------------------- |
| onclick       | 当用户点击某个对象时调用的事件句柄     |
| oncontextmenu | 在用户点击鼠标右键打开上下文菜单时触发 |
| ondblclick    | 当用户双击某个对象时调用的事件句柄     |
| onfocus       | 当获得焦点时                           |
| onblur        | 当对象失去焦点                         |
| onmousedown   | 鼠标按钮被按下                         |
| onmouseup     | 鼠标按键被松开                         |
| onmouseenter  | 当鼠标指针移动到元素上时触发           |
| onmouseleave  | 当鼠标指针移出元素时触发               |
| onmousemove   | 鼠标被移动                             |
| onmouseover   | 鼠标移到某元素之上                     |
| onmouseout    | 鼠标从某元素移开。                     |

#### 键盘事件

| Event      | Desc                     |
| :--------- | :----------------------- |
| onkeydown  | 某个键盘按键被按下       |
| onkeyup    | 某个键盘按键被松开       |
| onkeypress | 某个键盘按键被按下并松开 |

#### 表单事件

| Event    | Desc             |
| :------- | :--------------- |
| onchange | 文本的内容被改变 |
| onselect | 文本被选中       |
| onsubmit | 确认按钮被点击   |
| onreset  | 重置按钮被点击   |