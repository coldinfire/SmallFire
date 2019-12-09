---
title: "SAP Authorization"
date: 2018-09-22
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - authority
  - SAPbusiness

---

### TCode简介

​	SU01：查看和编辑Role

​	SU10：实现对用户组的批量维护


​	SU20：权限字段清单，可以增、删、改权限字段，可以浏览字段具体被哪些权限对象所调用

​	SU21：维护权限对象，可以创建和维护权限类，权限对象，权限字段在该事务码中被分配到权限对象

​	SU22：维护权限对象的分配，可以通过该事物代码为具体事务分配权限对象

​	SU24：将Authorization Object Assign 到 T-Code 上

​	SU53：当前用户权限不足问题查看

​    SUIM：用户、角色、权限对象、事务等之间的关系查看

​	PFCG：进入权限角色维护界面，创建Role 设置Role的Authorization Object

#### Role

- 角色(Role): 用于给用户分配具体的权限菜单，可以把相关操作的菜单分配到某个角色中，每个
  角色可以分配给多个用户，每个用户也可以同时分配多个角色。

- 通用角色(Common Role)、本地角色(Local Role)：本地角色较之通用角色的区别是，在同样的
  操作权限情况下本地角色多了具体的限制值。

- 参数文件: 每个角色都会有唯一对应的参数文件，SAP通过参数文件检查用户访问呢系统的权限。

- 组(Group): 权限检查用户组，与“登录数据”页面中的用户组是配合使用的，可以实现对用户管理
  的分散维护。

- 权限角色: 用户的权限菜单时通过权限角色分配实现的。角色维护又分为单一角色和符合角色，单一角色
           是一个独立的权限对象，复合角色可以有多个单一角色组合而成。


  - 菜单: 用于定义并分配该角色所能操作的事物代码。

  - 权限: SAP会根据对该角色的菜单项所分配的角色列出具体的权限对象，可以设置权限对象的具体的参数值。为某个角色分配具体的权限数据后，会自动产生一个参数文件。
    	

  - 用户: 将权限角色分配到SAP具体的用户账号。需要点击用户比较，并点击完成比较即可。	

### 2.权限对象	

- 角色包含了若干权限对象、在透明标`AGR_1250`中存储二者之间的关系。

- 在SAP实际应用中，用户所直接操作的是屏幕及屏幕对应的字段，这些字段都是由权限对象进行控制，
  包括该字段所允许的操作及所允许的值。

- SAP权限对象(Authorization Object)： 权限对象设置后，需要绑定到事务码上，然后在ABAP程序
  中通过AUTHORITY-CHECK OBJECT 语句来做权限检查。权限对象就起作用了。

​	权限对象包含了若干权限字段、允许的操作和允许的值：在透明表`AGR_1251`中体现了Role/Object/Field/Value之间的关系；有一个特殊的权限对象包含若干
事务码。这个权限对象叫“S_TCODE”，该权限对象的权限字段叫“TCD”，该字段允许的值存放的就是事务代码；
在透明表USOBX中，存放了事务码与权限对 象的对应关系。 

- 权限字段(Authorization Field)： 分配取值 
  	

  - ACTVT:该字段存放的就是允许操作的代码。
    
  - TCD: 存放该权限角色所包含的事物代码。

  - 允许的操作(Activity):  维护具体的选项操作。

  - 允许的值(Field Value): 值代表每个选型的功能。
    	

  - 保存并激活后使该权限参数文件生效。

### 3.权限授权解决

SU01: 输入UserID查看和编辑Role
	  

- Role: menu(T-Code)、Authorization(Display Authori)、Organization levels

SU53: 当前用户权限不足的原因

SUIM: 查找用户及Role等的具体信息，确定问题点，及可以解决的方法

PFCG: 维护Role、Menu修改
Role 

- `SAP_(SAP 标准)` 
- `Z_WIK_ALL(用户无关)
  ` 
- `Z_U_(混合角色不适用)`
- ` Z_模块名_(对应的Role编码)`

### 4.在程序中调用权限对象

​	sap 扩展中用户往往都需要使用自己的权限对象，为了达到目的，请按下列流程建立和维护权限对象：

- SE11：创建Domain和数据类型
- SU20 ：创建权限对象字段（存储在 AUTHX 表中）
- SU21 ：创建权限对象，创建权限对象类别（存储在 TOBCT 表中）点击对象类别创建权限对象（存储在 TOBJ 表中），生成 SAP_ALL
- SE38/SE93：创建程序，引用权限对象；给程序分配事务代码
- SU24：TCode分配权限对象
- SU01 或 PFCG： 将授权对象分配给对象类

**SE11创建Domain和Data Element**

​	创建权限字段时需要参考此Data Element。

**SU20创建权限字段**

​	输入Tcode:SU20进入界面后点击Create图标，创建新的权限字段。![SU20](/images/ABAP/AUTHORITY_SU20.png)

**SU21创建权限类、权限对象**

​	首先创建权限类，如果存在适用的则不用创建；然后创建权限对象，放到指定的Class并分配对应的权限字段。![SU21](/images/ABAP/AUTHORITY_SU21.png)![SU21](/images/ABAP/AUTHORITY_SU21D.png)

**创建程序，引入权限对象**

```JS
AUTHORITY-CHECK OBJECT 'XXX'
  ID 'XXX1' FIELD field
  ID 'XXX2' FIELD field
  ID 'XXX3' FIELD field
  ID 'XXX4' FIELD field
  ID 'ACTVT' FIELD '02'.
IF SY-SUBRC <> 0.
  MESSAGE 'XXXXX'
ENDIF.
OBJECT: 表示具体的权限对象。
ID： 表示权限字段，可以同时检查权限对象中的一个或多个权限字段。
FIELD： 权限字段所检查的权限值，将该字段改为屏幕元素，可检查该字段所输入的值是否符合要求。
ACTVT: Create = 01; Change = 02; Display = 03
*类似一个大致的权限矩阵，纵向是操作人（ID），横向是某些权限对象，权限对象再细分成若干事务代码、
允许动作、权限字段及其允许的值等。
```
#### 给Tcode分配权限对象

​	保证每个事务代码所用到的的权限对象都能够完整被带出来。![SU24](/images/ABAP/AUTHORITY_SU24.png)![SU24](/images/ABAP/AUTHORITY_SU24D.png)

![SU24](/images/ABAP/AUTHORITY_SU24D1.png)

**维护权限值**

​	PFCG进入维护角色，维护 “菜单” 选项页，创建TCode节点将需要控制的Tcode维护进去。由于添加了TCode，TCode应用的权限对象会自动带到角色中。

​	切换至 “权限” 选项页，在此页面需维护参数文件及具体的权限对象参数，维护权限对象参数也就限定了在事物码执行中的操作范围。点击生成参数文件按钮执行生成参数文件操作。这是系统自动生成的，名称及描述可以更改，并且可以删除。

​	 此时参数文件生成，但还不具有内容，“权限” 选项页的标志是红色尚未变绿，点击右下的 “更改权限数据” 按钮进行更改，进入角色维护界面。角色维护界面显示具体的权限状态等信息，可以维护权限队象的信息。维护完成，保存，并生成参数文件。

​	切换到 “用户” 选项页，在此可将角色分配至多个用户；SU01 也可以做此操作，但那是为一个用户分配多个角色。在用户分配栏中输入用户帐号，右侧自动显示角色的有效期。

​	操作后角色并不能立即生效，“用户” 选项页的标签是黄色，“用户比较” 按钮状态是红色，点击 “用户比较” 按钮，完成比较保存后才生效。



参考:

​	[SAP用户权限控制设置及开发](https://blog.csdn.net/candy_mmyy/article/details/54906571)

​	[用SAP Authority Object 对权限控制](https://www.cnblogs.com/long2006sky/archive/2009/06/07/1498029.html)

