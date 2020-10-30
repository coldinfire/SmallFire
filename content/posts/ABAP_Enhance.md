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

### 基本概念

SAP 增强已经发展过几代了，可参考 SAP 标准教材 BC425 和 BC427。

SAP 增强从用途来说分

- 数据元素增强
- 菜单增强
- 屏幕增强
- 功能增强

SAP 增强从实现方式来说分

- 第一代增强（User Exits）
- 第二代增强（Customer Exits：SMOD、CMOD）
- 第三代增强（BADI）
- 第四代增强（BTE）

其他相关增强

- BTE：财务模块常用的替代和验证
- VOFM 
  销售模块常用的例程等
  - Display:Requirements & Formulas => Formulas => Condition value 可定义增强公式

实现某个用途采用何种实现方式，四代增强可能都不是万能的，具体采用哪种方式实现，需要考虑实际情况（可能四种方式都能实现某个增强），以及程序员个人喜好选择合适的增强方式。

### 具体分类

#### User Exits(第一代增强)

是系统中预留的一些空的Form/Subroutine，获得Access key后可以在Form中写入自己的逻辑。这类增强都需要修改 sap 的标准代码。User Exits通常用于SD模块，SAP在销售，运输，计费领域提供许多退出。

- SD出口文档：IMG->Sales and Distribution -> System Modifications -> User Exitrs.
- 这种源代码增强和屏幕增强的说明可以从事务码 spro 后台配置中相关模块的路径里面找到。
- 同时使用的针对数据表的增强是 Append structure，可以在事务码 se11 中打开透明表，为数据表追加新的字段。

#### Customer Exits(第二代增强) 

使用Tcode:SE38找到程序所属的包，使用`SMOD`导航到`Utilities -> Find`，输入包名，然后执行即可

- FM Exits：在FM中include 保留的 Z 程序来提供功能扩展点
- Menu Exits：在GUI status中预留+Fcode menu item, 在程序中预留对应的Handling FM Exits
- Screen Exits：在Screen 中预留 Subscreen, 在程序中预留transport data to subscreen & return/retrieve data from subscreen的 FM Exits

Enhancement & Enhancement Project：
- Enhancement：把系统程序中的相关Customer Exits收集起来成为一个Enhancement，一般情况是按功能和类型来收集的,比方说几个相关的FM eixts组成一个enhancemnet，或者一个 screen 或 menu exits 形成一个enhancement。查看/修改 Enhancement的t-code为：`SMOD`
- Enhancement Project：在使用Enhacement时，要先建立一个Enhancement Project，可以将多个Enhancement assign给一个enhancement project去管理，对应t-code：`CMOD`。

#### BADI (Business Add-in,第三代增强)

通过面向对象的方式来提供扩展点，它支持Customer Exits所有的enhancement 类型，
因目前Class中不能包含subscreen所以在用BADI enhance screen时比用Customer Exits要复杂些。
非Multiple Case的BADI同时只能有一个Active Implementation，即要Active新生成的需先inactive旧的。
若是Multiple Case的BADI则可同时有多个Active Implementation，且所有的Implementation在没有Filter的情况下
都会被遍历执行。

#### Others

- User Exits与Customer Exits的区别在于User Exits的使用需要Access Key但Customer Exits不要。
- FM exits在关联的Function Group中的命名规则为：EXIT_PROGRAMNAME_XXX.
- Customer exits的调用方式为：
  - FM Exits: CALL CUSTOMER-FUNCTION 'xxx' EXPORTING ... IMPORTING ...
  - Subscreen: Call CUSTOMER-SUBSCREEN INCLUDING

### 怎么查找增强

#### 查找User Exit

使用 T-code: **SE93** 输入指定的T-code -> 从这里转到主程序，然后单击“查找”按钮.

在查找内容中输入 **Specify USEREXIT** 并选择“在主程序中查找”单选按钮，然后单击“搜索”。

- 如果使用了用户出口，将会显示出出口在SAP中的所有位置
- 如果使用了用户出口，则会在用户出口上方有一个注释

#### 查找Customer Exits

1. 通过一些专门的程序，如:[利用t-code查找增强出口的程序工具](https://www.591sap.com/thread-87-1-1.html)
2. 在主程序中查找 **call customer**
3. SE80 -> Repository Infomation Sysrtem -> Enhancements -> Customer Exits -> Input search condition -> Execute
4. SE11 -> 查看表**MODSAPVIEW** -> 在MEMBER(Enhancement)中输入程序名  -> Execute : 得到的SAP extension name 即为 Customer Exits Enhancement Name.
5. 首先使用SE93根据事物码找到对应程序名，然后 SE11 查询数据表 TADIR（限定 PGMID=“R3TR”、 OBJECT= “PROG”、OBJ_NAME = 程序名）找对应开发类，如果找不到对应开发类，通过 SE38 查看程序，在菜单"转到 - 属性"中找开发类。然后再用 SE11 查询数据表 TADIR（限定 PGMID=“R3TR”、 OBJECT= “SMOD”、DEVCLASS = 开发类）就可找到此程序可用的增强点（并非万能）。然后根据增强点从表 **MODSAP** 中就可以看到此增强具备哪些功能【屏幕增强（S）、菜单增强（C）、功能增强（E）、表增强（T）】

#### 查找BADI

1. 通过一些专门的程序，如:[一个功能非常全面的增强出口查找工具](https://www.591sap.com/thread-86-1-1.html)

2. SE38 -> Tcode对应的程序名 -> 在程序内搜索关键字 **CL_EXITHANDLER** 

3. SE80 -> Repository Infomation System -> Enhancements -> Business Add-ins -> Implementations查找

   ![SE80](/images/SAPUtils/SAP_ENHANCE_1.png)

4. 在主程序源代码中搜索字符串 **TYPE REF TO**，然后检查程序中是否使用了BADI

5. 它的调用方式是call method (instance),可以通过 **EXIT_HANDLER** 关键词来查找

6. ST05选择"TABLE BUFFER TRACE"而不是常用的"SQL trace",然后查找 (SXS_INTER,SXC_EXIT,SXC_CLASS,SXC_ATTR)找到BADI

7. 通过 **CL_EXITHANDLER -> GET_INSTANCE** Debug程序查找调用的BADI

   ```JS
   <1>Go to TCode SE24 and enter CL_EXITHANDLER as object type.
   <2>In 'Display' mode, go to 'Methods' tab.
   <3>Double click the method 'Get_Instance' to display it source code.
   <4>Set a breakpoint on 'CALL METHOD cl_exithandler=>get_class_name_by_interface'.
   <5>Then run your transaction.
   <6>The screen will stop at this method.
   <7>Check the value of parameter 'EXIT_NAME'.It will show you the BADI for that transaction.
   ```


### Customer Exits and BADI implementation.

####  Customer Exits: SMOD, CMOD

#### BADI: SE18, SE19,SE24.

可以使用 SE18 查看BADI，可以看到BADI 对应的接口，接口中定义的方法及参数传递。

然后SE19  Implementation 该 BADI。

使用SE24 查看CLASS Interface。

注：接口编码 BADI 加前缀 `IF_CL_`，客户类编码 `ZCL_IM_`

### 查找的功能程序：

- [利用 t-code 查找增强出口的程序工具](https://coldinfire.github.io/2018/ABAPEnhance1/)

- [查找增强出口和 BADI](https://coldinfire.github.io/2018/ABAPEnhance2/)

- [利用 t-code 查找增强出口的程序工具](https://coldinfire.github.io/2018/ABAPEnhance1/)





