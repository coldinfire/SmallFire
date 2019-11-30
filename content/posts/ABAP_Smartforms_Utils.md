---
title: "Smartforms常用工具"
date: 2018-07-24
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---

#### Table和template区别：

- 1、`table` 行为动态，数据输出时会根据列宽自动换行，可以固定列宽，
  但是默认情况下控制不了行高，如果要想 template 一样固定行高，需要将 table 的无换页属性打钩；通过 Main window （还是 table 控件？）的高度来自动翻页.
- 2、`template` 为静态，固定列宽、行高，当输出数据过长时会自动截断，通常被用于静态表单开发。
  template 跟 loop 嵌套使用，可以实现固定行高、列宽的表单开发。需要手动翻页，并程序中计算页码；（程序中计算页码？）.

#### Smartforms Debug

1. 使用TCode:Smartforms进入，输入smartform name或则同个NACE查找到smartform
2. 在smartform中找到需要Debug的一段代码，打上相应的断点
3. 在导航栏中通过 Environment –> Function Module Name 获取Function module
4. 在SE37中打开该Function Module,进入Main program找到Perform GLOBAL_INIT
5. 该FORM中进行所有的程序数据初始化，所有smartform中的程序行在此处定义，可以打断点
6. 或则在Main program中找到想要打断点的代码位置，运行Smartform，会进入断点，进行查错

#### Page Break in a smartforms

 Page break based on a field value,Compare the field value and if the value chnaged then do a page break.

- Create a Page with a Main Window to display the item deatils as Table in the smartform.

- Create a Loop inside the Main window, to loop the internal tables with data.

- Create a Command in the loop, in the condition tab of the command check the field value.

- Select the checkbox (Go to New Page) and give the next page which you want to display.

- Create a text under the command to display data.

  Based on the condition you have given in the condition tab of the command, a page break will automatically get triggered and the needed data will get flowed to the next page.

