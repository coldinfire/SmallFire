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

SAP 增强从实现方式的分类：User Exits (第一代增强)、Customer Exits (第二代增强)、BADI (第三代增强)、Enhancement-Point (第四代增强)。

其他相关增强

- **BTE**：财务模块常用的替代和验证
- **VOFM**：销售模块常用的例程等
  - Display：Requirements & Formulas => Formulas => Condition value 可定义增强公式

实现某个用途采用何种实现方式，四代增强可能都不是万能的，具体采用哪种方式实现，需要考虑实际情况（可能四种方式都能实现某个增强），以及个人喜好选择合适的增强方式。

### 增强分类详解

#### User Exit (第一代增强)

User Exits 是系统中预留的一些空的 Form/Subroutine，这些 Form 的名称一般是以 **UserExit** 开头的子模块，所以一般找到所要增强的主程序，再查找 UserExit_ 关键字即可找到相关的出口。这类增强都需要修改 SAP 的标准代码，在做增强开发时需要先获得 Access key， 然后在 Form 中写入业务逻辑。

User Exits 通常用于 SD 模块，SAP 在销售，运输，计费领域提供许多增强出口。

- SD出口文档：IMG -> Sales and Distribution -> System Modifications -> User Exits.

  ![User Exits](/images/ABAP/ABAP_Enhance1.png)

- 这种源代码增强和屏幕增强的说明可以从事务码 spro 后台配置中相关模块的路径里面找到。

- 同时使用的针对数据表的增强是 Append structure，可以在事务码 se11 中打开透明表，为数据表追加新的字段。

#### Customer Exit (第二代增强) 

使用 SMOD 和 CMOD 维护该类型增强；在 SAP 标准程序中，使用 `CALL CUSTOMER-FUNCTION 'num'` 调用对应的函数模块，出口函数名称由三部分组成：`EXIT_<程序名>_<3位数字>`，程序名是调用该出口函数的程序名。

- 示例：`SAPMM06E -> CALL CUSTOMER-FUNCTION '010' -> FM：EXIT_SAPMM06E_010`

增强类型：

- E（FM Exits）：在 FM 中 include 保留的 Z 程序来提供功能扩展点
  - 检查功能出口类用户出口是否被激活：`MODX_FUNCTION_ACTIVE_CHECK`
- C（Menu Exits）：在 GUI status 中预留 Fcode menu item，在程序中预留对应的 Handling FM Exits
  - 检查菜单关键字类增强激活状况：`MODX_MENUENTRY_ACTIVE_CHECK` 、`MODX_ALL_ACTIVE_MENUENTRIES`
- S（Screen Exits）：在 Screen 中预留 Subscreen，在程序中预留 transport data to subscreen & return/retrieve data from subscreen 的 FM Exits；增强调用使用 `CALL CUSTOMER-SUBSCREEN`
  - 检查屏幕类增强激活状况：`MODX_SUBSCREEN_ACTIVE_CHECK`
- T（Table）：表结构增强，针对数据表的增强出口是 **CI_** 开头的结构，这些结构将 `.INCLUDE` 结构的形式包含到时相应的数据表中，用户可以通过向这些结构中添加字段从而达到对数据表字段的增加

Enhancement & Enhancement Project：
- Enhancement（`SMOD`）：把系统程序中的相关 Customer Exits 收集起来成为一个 Enhancement，一般情况是按功能和类型来收集的，例如几个相关的 FM eixts 组成一个 Enhancemnet，或者一个 screen 或 menu exits 形成一个 Enhancement。
- Enhancement Project（`CMOD`）：在使用 Enhacement 时，要先建立一个 Enhancement Project，可以将多个Enhancement assign 给一个 Enhancement project 去管理。

增强实现：

- Customer Exit 需要将增强放到 PROJECT 里面才能使用
- 步骤：SMOD 查找开发类下增强 ---> 查看增强下的 FM ---> 进入 FM下的 include ---> include下编写增强代码 ---> 激活增强代码 ---> CMOD 创建 PROJECT ---> PROJECT 绑定增强 ---> 激活 PROJECT ---> 业务模拟验证

#### BADI (Business Add-in,第三代增强)

BADI 通过面向对象的方式来提供扩展点，它支持 Customer Exits 所有的 Enhancement 类型，因目前 Class 中不能包含 subscreen 所以在用 BADI Enhance Screen 时比用 Customer Exits 要复杂些。

SAP 预定义了 Interface，由客户来实例化相应的接口，应用程序通过方法调用来获得用户所定义 class 的 instance。

SAP BADI 的使用分类：

- 非 Multiple Case 的 BADI 同时只能有一个 Active Implementation，要 Active 新生成的需要先 Inactive 旧的。
- Multiple Case 的 BADI 则可同时有多个 Active Implementation，且所有的 Implementation 在没有  Filter 的情况下都会被遍历执行。

在某些情况下，出于业务需求，可能存在多个开发内容需要放置在这种 BADI 的实施中。因为只有一个实施类可用，就可能会导致不同开发人员的代码发生碰撞，造成一些不好的结果。这时会自然地产生为这种 BADI 创造多个实施，并让它们依次执行的需求。解决方法：

1. 为我们想要增强的类创建一个基本 BADI 实施，在该实施类中使用 cl_sic_configuration 获取 BADI 对应的所有自定义实施类的列表。
2. 过滤掉系统类、以及基本实施类本身。为剩余的自定义实施类创建实例、并调用相关方法。
3. 基本 BADI 实施需要设置为 Active 状态。
4. 创建的其它 BADI 是 Inactive 状态。

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

#### Enhancement-Point (第四代增强)

第四代增强是对第三代做的加强。Enhancement Spot 是对 Enhancement options 的一个管理平台；在该容器中我们可以定义自己的 BADI 或则 Enhancement Point。Enhancement Implementation 用来组织 Enhancement options 的实现代码。

Enhancement-Point 技术与 BADI 是有区别的，首先 BADI 是 SAP 预留的类的接口，而 Enhancement-Point 则是允许用户对现有的 SAP 代码进行修改：例如插入、替换，只要符合一定的规则即可，不需要 SAP 预先定义好。其最大的优势在于方便，可以直接使用程序中所有已定义的变量，不像 BADI 和 USER EXIT中只能使用方法或函数接口传过来看参数。

***隐式增强和显式增强：***

显式增强：就是手动加入到程序中的 Enhancement options，有两种显式增强

- **ENHANCEMENT-POINT（增强点）**：是在程序中直接插入代码，其概念与 USER EXIT 类似，标准程序预留了部分已定义好的增强点，没有代码，只有一个预留点。可以插入代码来实现这个增强（也可以自定义增强点，但不能自定义增强选项，增强选项一定是系统预留下来的，如果没有增强选项则该处不可做增强），但是不能做屏幕和菜单增强。
- **ENHANCEMENT-SECTION（增强选项）**：定义和实现的方法与 ENHANCEMENT-POINT 一样。两者的区别是：增强点没有代码，只有一个预留点，允许在这个位置插入新代码（implementation）；而在 Enhancement-section 和 End-enhancement-section. 之间有代码，implementation  之后会替换旧代码，只执行新代码，原来的代码不再执行。

隐式增强：在执行程序，包含程序，函数组，对话模块的结尾；Form例程，函数模块，方法等的开始和结尾；结构的结尾这些地方都会有。

***一般增强步骤***

1. DEBUG 标准程序找到需要增强的位置，点 EDIT -> SHOW IMPLICIT ENHANCEMENT OPTIONS 查看是否有预留增强选项。（标准程序不能自己创建 enhancement point ,只能使用系统预留的）

2. 创建增强点；创建增强点的实现，在增强点的实现中添加代码

#### 注意事项

- User Exit 与 Customer Exit 的区别在于 User Exit 的使用需要 Access Key 但 Customer Exit 不要
- FM exit 在关联的 Function Group 中的命名规则为：EXIT_PROGRAMNAME_XXX
- Customer exit 的调用方式为：
  - FM Exit：`CALL CUSTOMER-FUNCTION 'xxx' EXPORTING ... IMPORTING ...`
  - Subscreen：`Call CUSTOMER-SUBSCREEN INCLUDING`

### 增强查找

#### User Exit 查找

使用事务码 **SE93** 输入需要查找的 T-code -> 从这里转到主程序；然后单击“查找”按钮，在查找内容中输入 **USEREXIT** 并选择“In main program”，然后点击“搜索”。

- 如果使用了用户出口，将会显示出出口在 SAP 中的所有位置
- 如果使用了用户出口，则会在用户出口上方有一个注释

![User Exits](/images/ABAP/ABAP_Enhance2.png)

#### Customer Exit 查找

1. 通过一些专门的程序，如:[利用t-code查找增强出口的程序工具](https://www.591sap.com/thread-87-1-1.html)

2. 使用函数 MODX_FUNCTION_ACTIVE_CHECK，代码最后添加断点；执行需要查找的 TCODE，如果有增强就会自动跳入 DEBUG 界面；在 DEBUG 界面，查看 `L_FUNCNAME` 字段，该字段显示的值即为增强出口（如果对应的增强出口值不是想要查找的操作点位，退出 Debug 界面，然后继续正常的业务操作，当再次进入该 Debug 界面时，再次查看是否满足需求）。这些增强都是属于 `EXIT_<程序名>_<3位数字>` 这种形式。查阅  `MODSAP` 表，确定增强属于哪个 SMOD。

   ![Customer Exit](/images/ABAP/ABAP_Enhance4.png)

3. SE11 查看表 **MODSAPVIEW** -> 在字段 MEMBER 中输入 Enhancement 后执行：得到的SAP extension name 即为 Customer Exits Enhancement Name.

   ![Customer Exits](/images/ABAP/ABAP_Enhance3.png)

4. 首先使用 SE93 根据事物码找到对应程序名；然后 SE11 查询数据表 TADIR（限定 PGMID = "R3TR"、 OBJECT =  "PROG"、OBJ_NAME = "程序名"）找对应开发类；如果找不到对应开发类，通过 SE38 查看程序，在菜单"转到 - 属性"中找开发类。然后再用 SE11 查询数据表 TADIR（限定 PGMID = "R3TR"、 OBJECT= "SMOD"、DEVCLASS = "开发类"）就可找到此程序可用的增强点（并非万能）。然后根据增强点从表 **MODSAP** 中就可以看到此增强具备哪些功能【屏幕增强（S）、菜单增强（C）、功能增强（E）、表增强（T）】

5. SE80 -> Repository Infomation Systerm -> Enhancements -> Customer Exits -> Input search condition -> Execute

#### BADI 查找

1. 通过一些专门的程序，如:[一个功能非常全面的增强出口查找工具](https://www.591sap.com/thread-86-1-1.html)

3. SE80 -> Repository Infomation System -> Enhancements -> Business Add-ins -> Implementations查找

   ![SE80](/images/SAPUtils/SAP_ENHANCE_1.png)

4. 在主程序源代码中搜索字符串 `CL_EXITHANDLER`，查看它对应的 TYPE REF TO 接口名，接口命名规则 `IF_EX_<BADI>`，得到 BADI 名称

6. ST05 选择 "TABLE BUFFER TRACE" 而不是常用的 "SQL trace"，然后查找表(SXS_INTER、SXC_EXIT、SXC_CLASS、SXC_ATTR) 找到 BADI

5. Classic BADI ：通过 **CL_EXITHANDLER -> GET_INSTANCE** Debug 程序查找调用的 BADI；New BADI：调用 **GET BADI handle** （handle TYPE REF TO BADI定义名）、**CALL BADI handle->method** 来判断对象是否存在和返回对应实例

   ```JS
   <1> Go to TCode SE24 and enter CL_EXITHANDLER as object type.
   <2> In 'Display' mode, go to 'Methods' tab.
   <3> Double click the method 'Get_Instance' to display it source code.
   <4> Set a breakpoint on 'CALL METHOD cl_exithandler=>get_class_name_by_interface'.
   <5> Then run your transaction. The screen will stop at this method.
   <7> Check the value of parameter 'EXIT_NAME'.It will show you the BADI name.
   ```

### BADI implementation

BADI 创建和实现：[https://coldinfire.github.io/2019/ABAP_Enhance_BADI/](https://coldinfire.github.io/2019/ABAP_Enhance_BADI/)

### 显式和隐式增强技术

- [显式增强](https://blog.csdn.net/weixin_40672823/article/details/105994981)

- [隐式增强](https://blog.csdn.net/weixin_40672823/article/details/106055695)

### 查找的功能程序：

- [查找增强出口和 BADI](https://coldinfire.github.io/2018/ABAP_Enhance_Util1/)
- [利用 t-code 查找增强出口的程序工具](https://coldinfire.github.io/2018/ABAP_Enhance_Util2/)

### 增强实现









