---
title: " SAP 增强 "
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

SAP 增强从用途方面分类：数据元素增强、菜单增强、屏幕增强、功能增强。

SAP 增强从实现方式的分类：User Exits (第一代增强)、Customer Exits (第二代增强)、BADI (第三代增强)、BTE (第四代增强)。

其他相关增强

- BTE：财务模块常用的替代和验证
- VOFM：销售模块常用的例程等
  - Display:Requirements & Formulas => Formulas => Condition value 可定义增强公式

实现某个用途采用何种实现方式，四代增强可能都不是万能的，具体采用哪种方式实现，需要考虑实际情况（可能四种方式都能实现某个增强），以及程序员个人喜好选择合适的增强方式。

### 增强分类详解

#### User Exits (第一代增强)

User Exits 是系统中预留的一些空的 Form/Subroutine，获得 Access key 后可以在 Form 中写入自己的逻辑。这类增强都需要修改 SAP 的标准代码。User Exits 通常用于 SD 模块，SAP 在销售，运输，计费领域提供许多增强出口。

- SD出口文档：IMG -> Sales and Distribution -> System Modifications -> User Exits.

  ![User Exits](/images/ABAP/ABAP_Enhance1.png)

- 这种源代码增强和屏幕增强的说明可以从事务码 spro 后台配置中相关模块的路径里面找到。

- 同时使用的针对数据表的增强是 Append structure，可以在事务码 se11 中打开透明表，为数据表追加新的字段。

#### Customer Exits (第二代增强) 

使用 Tcode:SE38 找到程序所属的包，使用`SMOD`导航到`Utilities -> Find`，输入包名，然后执行即可

- FM Exits：在 FM 中 include 保留的 Z 程序来提供功能扩展点
- Menu Exits：在 GUI status 中预留 Fcode menu item，在程序中预留对应的 Handling FM Exits
- Screen Exits：在 Screen 中预留 Subscreen，在程序中预留 transport data to subscreen & return/retrieve data from subscreen 的 FM Exits

查找增强点相关函数

- DYNP_VALUES_READ

- MODX_ALL_ACTIVE_MENUENTRIES：(菜单增强)

- MODX_FUNCTION_ACTIVE_CHECK：(出口函数增强)

- MODX_MENUENTRY_ACTIVE_CHECK：(菜单增强)

- MODX_SUBSCREEN_ACTIVE_CHECK：(屏幕增强)  

Enhancement & Enhancement Project：
- Enhancement：把系统程序中的相关 Customer Exits 收集起来成为一个 Enhancement，一般情况是按功能和类型来收集的，例如几个相关的 FM eixts 组成一个 Enhancemnet，或者一个 screen 或 menu exits 形成一个 Enhancement。查看/修改 Enhancement 的 Tcode：`SMOD`
- Enhancement Project：在使用 Enhacement 时，要先建立一个 Enhancement Project，可以将多个Enhancement assign 给一个 Enhancement project 去管理，对应 Tcode：`CMOD`。

#### BADI (Business Add-in,第三代增强)

通过面向对象的方式来提供扩展点，它支持 Customer Exits 所有的 Enhancement 类型，
因目前 Class 中不能包含 subscreen 所以在用 BADI Enhance Screen 时比用 Customer Exits 要复杂些。

SAP BADI 的使用分类：

- 非 Multiple Case 的 BADI 同时只能有一个 Active Implementation，要 Active 新生成的需要先 Inactive 旧的。
- Multiple Case 的 BADI 则可同时有多个 Active Implementation，且所有的 Implementation 在没有  Filter 的情况下都会被遍历执行。

在某些情况下，出于业务需求，可能存在多个开发内容需要放置在这种 BADI 的实施中。因为只有一个实施类可用，就可能会导致不同开发人员的代码发生碰撞，造成一些不好的结果。这时会自然地产生为这种 BADI 创造多个实施，并让它们依次执行的需求。

读取 BADI 接口对应的自定义实施类: **cl_sic_configuration**

```ABAP
REPORT ZGET_BADI_IMPL_LIST.
DATA: lt_classes TYPE STANDARD TABLE OF sic_s_class_descr.
DATA: ls_class   LIKE LINE OF lt_classes.
TRY.
  lt_classes = cl_sic_configuration=>get_classes_for_interface( 'IF_EX_N1_CANCEL' ).
  CATCH cx_class_not_existent .
ENDTRY.
LOOP AT lt_classes INTO ls_class.
  WRITE: ls_class-clsname, / .
ENDLOOP.
```

解决方法：

1. 为我们想要增强的类创建一个基本 BADI 实施，在该实施类中使用 cl_sic_configuration 获取 BADI 对应的所有自定义实施类的列表。
2. 过滤掉系统类、以及基本实施类本身。为剩余的自定义实施类创建实例、并调用相关方法。
3. 基本 BADI 实施需要设置为 Active 状态。
4. 创建的其它 BADI 是 Inactive 状态。

#### Others

- User Exits 与 Customer Exits 的区别在于 User Exits 的使用需要 Access Key 但 Customer Exits 不要。
- FM exits 在关联的 Function Group 中的命名规则为：EXIT_PROGRAMNAME_XXX.
- Customer exits 的调用方式为：
  - FM Exits: CALL CUSTOMER-FUNCTION 'xxx' EXPORTING ... IMPORTING ...
  - Subscreen: Call CUSTOMER-SUBSCREEN INCLUDING

### 增强查找

#### User Exit 查找

使用 T-code: **SE93** 输入指定的T-code -> 从这里转到主程序，然后单击“查找”按钮.

在查找内容中输入 **Specify USEREXIT** 并选择“在主程序中查找”单选按钮，然后单击“搜索”。

- 如果使用了用户出口，将会显示出出口在SAP中的所有位置
- 如果使用了用户出口，则会在用户出口上方有一个注释

#### Customer Exits 查找

1. 通过一些专门的程序，如:[利用t-code查找增强出口的程序工具](https://www.591sap.com/thread-87-1-1.html)
2. 使用函数 MODX_FUNCTION_ACTIVE_CHECK，代码最后添加断点，执行需要增强的 TCODE，如果有增强，就会自动跳入 DEBUG 界面。在 DEBUG 界面，查看 f_tab 字段，这里面所显示的 SMOD 就是关于这个 TCODE 所有的增强项目的列表。这些增强都是属于 EXIT_XXXXXX_XXX 这种形式。查阅  MODSAP 表，确定增强属于哪个 SMOD。
3. SE80 -> Repository Infomation Sysrtem -> Enhancements -> Customer Exits -> Input search condition -> Execute
4. SE11 -> 查看表**MODSAPVIEW** -> 在MEMBER(Enhancement)中输入程序名  -> Execute : 得到的SAP extension name 即为 Customer Exits Enhancement Name.
5. 首先使用 SE93 根据事物码找到对应程序名，然后 SE11 查询数据表 TADIR（限定 PGMID=“R3TR”、 OBJECT= “PROG”、OBJ_NAME = 程序名）找对应开发类；如果找不到对应开发类，通过 SE38 查看程序，在菜单"转到 - 属性"中找开发类。然后再用 SE11 查询数据表 TADIR（限定 PGMID=“R3TR”、 OBJECT= “SMOD”、DEVCLASS = 开发类）就可找到此程序可用的增强点（并非万能）。然后根据增强点从表 **MODSAP** 中就可以看到此增强具备哪些功能【屏幕增强（S）、菜单增强（C）、功能增强（E）、表增强（T）】

#### BADI 查找

1. 通过一些专门的程序，如:[一个功能非常全面的增强出口查找工具](https://www.591sap.com/thread-86-1-1.html)

2. SE38 -> Tcode对应的程序名 -> 在程序内搜索关键字 **CL_EXITHANDLER** 

3. SE80 -> Repository Infomation System -> Enhancements -> Business Add-ins -> Implementations查找

   ![SE80](/images/SAPUtils/SAP_ENHANCE_1.png)

4. 在主程序源代码中搜索字符串 **TYPE REF TO**，然后检查程序中是否使用了BADI

5. 它的调用方式是call method (instance),可以通过 **EXIT_HANDLER** 关键词来查找

6. ST05选择"TABLE BUFFER TRACE"而不是常用的"SQL trace",然后查找 (SXS_INTER,SXC_EXIT,SXC_CLASS,SXC_ATTR)找到BADI

7. 通过 **CL_EXITHANDLER -> GET_INSTANCE** Debug程序查找调用的BADI；这只是经典BADI是这样来调用的，如果是新式的BADI，则调用为 GET BADI handle-BADI定义名、CALL BADI handle->method，来判断对象是否存在，并返回实例

   ```JS
   <1>Go to TCode SE24 and enter CL_EXITHANDLER as object type.
   <2>In 'Display' mode, go to 'Methods' tab.
   <3>Double click the method 'Get_Instance' to display it source code.
   <4>Set a breakpoint on 'CALL METHOD cl_exithandler=>get_class_name_by_interface'.
   <5>Then run your transaction.
   <6>The screen will stop at this method.
   <7>Check the value of parameter 'EXIT_NAME'.It will show you the BADI for that transaction.
   ```


### Customer Exits and BADI implementation

####  Customer Exits: SMOD, CMOD

#### BADI: SE18, SE19,SE24.

可以使用 SE18 查看BADI，可以看到BADI 对应的接口，接口中定义的方法及参数传递。

然后SE19  Implementation 该 BADI。

使用SE24 查看CLASS Interface。

注：接口编码 BADI 加前缀 `IF_CL_`，客户类编码 `ZCL_IM_`

BADI 创建和实现：[https://coldinfire.github.io/2019/ABAP_BADI/](https://coldinfire.github.io/2019/ABAP_BADI/)

### 查找的功能程序：

- [利用 t-code 查找增强出口的程序工具](https://coldinfire.github.io/2018/ABAPEnhance1/)

- [查找增强出口和 BADI](https://coldinfire.github.io/2018/ABAPEnhance2/)

- [利用 t-code 查找增强出口的程序工具](https://coldinfire.github.io/2018/ABAPEnhance1/)





