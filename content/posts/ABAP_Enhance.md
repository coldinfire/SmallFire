---
title: "增强"
date: 2018-09-13
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - Enhance

---

## 基本概念
- User Exits：是系统中预留的一些空的Form/Subroutine，获得Access key后可以在Form中写入自己的逻辑。
  用户出口通常用于SD模块，SAP在销售，运输，计费领域提供许多退出。
  - SD出口文档：IMG->Sales and Distribution -> System Modifications -> User Exitrs.

- Customer Exits：使用:SE38找到程序所属的包，使用SMOD导航到Utilities->Find，输入包名，然后执行即可

  - FM Exits：在FM中include 保留的 Z 程序来提供功能扩展点
  - Menu Exits：在GUI status中预留+Fcode menu item, 在程序中预留对应的Handling FM Exits
  - Screen Exits：在Screen 中预留 Subscreen, 在程序中预留transport data to subscreen & return/retrieve data from subscreen的 FM Exits

- Enhancement & Enhancement Project：
  - Enhancement：把系统程序中的相关Customer Exits收集起来成为一个Enhancement，一般情况是按功能和类型来收集的, 
        比方说几个相关的FM eixts组成一个enhancemnet，或就一个 screen 或 menu exits 形成一个enhancement。
  -  查看/修改 Enhancement的t-code为：SMOD
  - Enhancement Project：在使用Enhacement时，要先建立一个Enhancement Project，可以将多个Enhancement assign给一个enhancement project去管理，对应t-code：CMOD。

- BADI (Business Add-in)，通过面向对象的方式来提供扩展点，它支持Customer Exits所有的enhancement 类型，
  因目前Class中不能包含subscreen所以在用BADI enhance screen时比用Customer Exits要复杂些。
  非Multiple Case的BADI同时只能有一个Active Implementation，即要Active新生成的需先inactive旧的。
  若是Multiple Case的BADI则可同时有多个Active Implementation，且所有的Implementation在没有Filter的情况下
  都会被遍历执行。

- Other
  - User Exits与Customer Exits的区别在于User Exits的使用需要Access Key但Customer Exits不要。
  - FM exits在关联的Function Group中的命名规则为：EXIT_program name_nnn
  - Customer exits的调用方式为：
    - FM Exits: CALL CUSTOMER-FUNCTION 'xxx' EXPORTING ... IMPORTING ...
    - Subscreen: Call CUSTOMER-SUBSCREEN INCLUDING

- VOFM 
  Display:Requirements & Formulas => Formulas => Condition value 可定义增强公式

## How to find user exits?

1. Using t-code: `SE93` and specify the transaction code.
      From here go to the main program and click on the `FIND`button
   `Specify USEREXIT` and select `find in main program` radio button and click search... 
      if any user exit is used, it will list all the places as in SAP if any user exit is used, 
    a comment is been written above the user exit.

2. How to find customer exits?

  - <1>通过一些专门的程序，如:[利用t-code查找增强出口的程序工具](https://www.591sap.com/thread-87-1-1.html)

  - <2>Search string “call customer” in the main program source code;

  - <3>SE80 -> Repository Infomation System -> Enhancements -> Customer Exits -> Input search condition -> Execute

  - <4>SE11 -> Database table: MODSAPVIEW -> Display Contents -> Input "*program name*" into Enhancement field ->
     Execute -> 得到的SAP extension name 即为 Customer Exits Enhancement Name.

3. How to find BADIs?

    - <1> 通过一些专门的程序，如:[一个功能非常全面的增强出口查找工具](https://www.591sap.com/thread-86-1-1.html)

    -  <2> SE38 -> 事物代码对应得程序名 -> 在程序内搜索 CL_EXITHANDLER


    - <3> SE24 -> CL_EXITHANDLER -> 在GET_INSTANCE中打断点，然后运行相应的事务码找到对应的BADI
    
    - <4> SE80 -> Repository Infomation System -> Enhancements -> Business Add-ins
    
    - <5> Search string “type ref to” in the main program source code, then check if there is BAdI used in the program
    
    - <6> 它的调用方式是call method(instance),可以通过exit_handler关键词来查找
    
    - <7> ST05选择"table buffer trace"而不是常用的"SQL trace",然后查找 (SXS_INTER,SXC_EXIT,SXC_CLASS,SXC_ATTR)找到BADI

4. Customer Exits and BADI implementation.

     - <1> Customer Exits: SMOD, CMOD

     - <2> BADI: SE18, SE19.

5. Find a BADI in one minute

     - <1>Go to TCode SE24 and enterG CL_EXITHANDLER as object type.

     - <2>In 'Display' mode, go to 'Methods' tab.

     - <3>Double click the method 'Get Instance' to display it source code.

     - <4>Set a breakpoint on 'CALL METHOD cl_exithandler=>get_class_name_by_interface'.

     - <5>Then run your transaction.

     - <6>The screen will stop at this method.

     - <7>Check the value of parameter 'EXIT_NAME'. It will show you the BADI for that transaction.

查找的功能程序：

- [利用 t-code 查找增强出口的程序工具](https://coldinfire.github.io/2018/ABAPEnhance1/)

- [查找增强出口和 BADI](https://coldinfire.github.io/2018/ABAPEnhance2/)

- [利用 t-code 查找增强出口的程序工具](https://coldinfire.github.io/2018/ABAPEnhance1/)





