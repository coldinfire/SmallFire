---
title: " SAP ABAP中自定义权限对象 "
date: 2018-09-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - utils
  - SAPbusiness

---

### 自定义权限对象流程

SAP 扩展中用户往往都需要使用自己的权限对象，为了达到目的，请按下列流程建立和维护权限对象。一旦有账号需要赋予权限，直接用 SAP 系统标准的角色权限配置就可以了。

SAP权限对象创建和分配流程

- SE11：创建数据类型
- SU20：创建权限对象字段（存储在 AUTHX 表中）
- SU21：创建权限对象，权限对象类别（存储在 TOBCT 表中）权限对象（存储在 TOBJ 表中），生成 SAP_ALL
- SE38/SE93：创建程序，引用权限对象；给程序分配事务代码,维护权限对象
- SU24：TCode分配权限对象
- SU01 或 PFCG： 将授权对象分配给对象类

#### SE11创建Domain和Data Element

创建权限字段时需要参考Data Element(可以使用系统已有的字段)，在Element中维护权限值让维护者知道怎么维护

#### SU20创建权限字段

后点击Create图标，创建新的权限字段并保存。

![SU20](/images/ABAP/AUTHORITY_SU201.png)

![SU20](/images/ABAP/AUTHORITY_SU202.png)

#### SU21创建权限类、权限对象

首先创建权限类(Object class)，如果存在适用的则不用创建；

然后创建权限对象(Auth Object)，放到指定的Class并分配对应的权限字段。

![SU21](/images/ABAP/AUTHORITY_SU21.png)

![SU21 创建权限类](/images/ABAP/AUTHORITY_SU211.png)

![SU21 创建权限对象](/images/ABAP/AUTHORITY_SU212.png)

权限字段中维护需要权限字段，可以在对象文档中维护一下该权限的一些描述：

![SU21 维护权限对象文档](/images/ABAP/AUTHORITY_SU213.png)

ACTVT是常用的字段，用来控制创建，修改，显示，等常用权限的控制：

![SU21 ACTVT字段控制设置](/images/ABAP/AUTHORITY_SU214.png)

一旦新增了用户的自定义权限对象后，需要单击工具栏中的`Regenerate SAP_ALL`按钮，将把新增的权限对象赋值给 SAP_ALL 这个权限参数文件。

#### SE38创建程序并使用

```JS
AUTHORITY-CHECK OBJECT 'Z_KL_CLOT'
  ID 'Z_KL_CLO' FIELD field
  ID 'ACTVT' FIELD '02'.
IF SY-SUBRC <> 0.
  MESSAGE 'You have no authority xxxx'.
ENDIF.
OBJECT:表示具体的权限对象。
ID:表示权限字段，可以同时检查权限对象中的一个或多个权限字段。
FIELD:权限字段所检查的权限值，将该字段改为屏幕元素，可检查该字段所输入的值是否符合要求。
ACTVT:Create = 01; Change = 02; Display = 03
*类似一个大致的权限矩阵，纵向是操作人(01)，横向是某些权限对象，权限对象再细分成若干事务代码、
允许动作、权限字段及其允许的值等。
```

#### SU24 为 Tcode 添加权限对象

若是事务码有多个权限控制对象，需要自己手动添加另外的权限对象。

保证每个事务代码所用到的的权限对象都能够完整被带出来。

![SU24](/images/ABAP/AUTHORITY_SU241.png)

![SU24 添加权限对象](/images/ABAP/AUTHORITY_SU242.png)

Proposal 

- YSE 运行相应事务码时，程序检查此权限对象
- NO 运行相应事务码时，程序不检查此权限字段

权限对象中有四个标识：？ U  C  CM

当权限对象对应的是 C 或 CM 时，控制有效。他们之间的区别在于，CM 在 PFCG 分配权限时会自动带出来，C 标记的需要手工分配。

### PFCG为角色分配权限

在实际应用中，往往会开发很多的工具和报表，并且需要对这些特定的程序进行权限分配，开发人员需要了解权限分配。

- 用户的权限菜单是通过权限角色分配来实现的

- 角色维护又分为单一角色和复合角色，单一角色是一个独立的权限对象，而复合角色可以由多个单一角色组合而成，能够同时继承不同单一角色的权限。

![PFCG](/images/ABAP/AUTHORITY_PFCG1.png)

#### 为角色分配菜单权限

PFCG进入维护角色，维护 “菜单” 选项页，用于定义并分配该角色所能操作的事务代码。创建TCode节点将需要控制的Tcode维护进去。由于添加了TCode，TCode应用的权限对象会自动带到角色中。

![PFCG Menu](/images/ABAP/AUTHORITY_PFCG2.png)

#### 为角色分配权限数据

上面分配完菜单后，就实现了在用户菜单中能看到的相关事务码菜单项，但是能否操作菜单中所对应事务的业务数据还得设置具体的权限数据：

​	![PFCG Authorizations](/images/ABAP/AUTHORITY_PFCG3.png)

为某个角色分配具体的权限数据后，会自动产生一个 **参数文件**，SAP 在执行中会通过读取该参数文件的数据来进行用户权限的检查及管控。

在进行更改 “权限数据” 前，先简单了解一下 **SAP 的权限对象**（权限对象设置好后，需要绑定到事务码上，然后在 ABAP 程序中是通过 AUTHORITY-CHECK OBJECT 语句来做权限检查的，这样权限对象就起作用了）：

在 SAP 实际应用中，用户所直接操作的是屏幕及屏幕所对应的字段，而这些具体字段都是由权限对象进行控制，包括该字段所允许的操作及允许的值（数据）。

此时参数文件生成，但还不具有内容，“权限” 选项页的标志是红色尚未变绿。在 “权限” 页签中单击 “更改权限数据” 按钮，**系统将自动抓取该角色菜单中所分配的所有事务码所对应的权限对象**，会弹出一个定义组织级别对话框，要求用户输入具体的业务数据控制范围。可以在此页面需维护参数文件及具体的权限对象参数。维护权限对象参数也就限定了在事物码执行中的操作范围。点击生成参数文件按钮执行生成参数文件操作。

![PFCG 参数文件](/images/ABAP/AUTHORITY_PFCG4.png)

SAP 角色的权限分配是从权限对象直接派生过来的，可以在不同的权限角色中同时调用同一权限对象，并为所生成对象分配定义不同的权限值。

点击 “保存” 后，SAP 会将权限对象以及所维护的权限值以树状结构分层列出，最顶级为对象类，对象类是同类属性的权限对象的集合，一个对象类可以包含多个权限对象。在权限角色维护页面的主菜单中执行 “实用程序 | 技术名称打开” 命令，将在每个字段的右边显示所有的对象类名称及权限对象名称：

![PFCG 权限对象结构](/images/ABAP/AUTHORITY_PFCG5.png)

该界面通过状态灯来表示各权限对象维护状态，绿灯代表激活，黄色表示未激活，红色代表未给权限字段分配值，单击权限字段前的铅笔可以定义该字段的授权值。权限对象维护完成后，节点的状态灯会变为绿色，单击 “保存” 按钮后会弹出一个 “为生成权限参数文件分配参数文件名称” 对话框：这是系统自动生成的，名称及描述可以更改，并且可以删除。

![PFCG 参数文件](/images/ABAP/AUTHORITY_PFCG6.png)

点击“激活”：激活权限数据，返回到角色维护界面，可以看到生成的权限参数文件

![PFCG 参数文件](/images/ABAP/AUTHORITY_PFCG7.png)

![PFCG 参数文件](/images/ABAP/AUTHORITY_PFCG8.png)

#### 将角色分配给用户

切换到 “用户” 选项页，将权限分配到具体的用户账号，在此可将角色分配至多个用户；SU01 也可以做此操作，但那是为一个用户分配多个角色。在用户分配栏中输入用户帐号，右侧自动显示角色的有效期。

![PFCG User](/images/ABAP/AUTHORITY_PFCG9.png)

操作后角色并不能立即生效，“用户” 选项页的标签是黄色，**“用户比较”**:[User Comparsion] 按钮状态是红色，点击 “用户比较” 按钮，完成比较保存后才生效。

#### 使用SU01分配角色给用户

当分配好角色时，该角色所带的参数文件也会自动带过来放在"参数文件"Tab 中，但你也可以在参数文件中还可以直接将其他的参数文件加进来，如 SAP_ALL、SAP_NEW (参数文件)

![SU01 Roles](/images/ABAP/AUTHORITY_PFCG10.png)

![PFCG Profiles](/images/ABAP/AUTHORITY_PFCG11.png)





参考链接：

- ​	[参考链接 https://blog.csdn.net/hubaichun/article/details/73718437](https://blog.csdn.net/hubaichun/article/details/73718437)
- ​	[SAP用户权限控制设置及开发](https://blog.csdn.net/candy_mmyy/article/details/54906571)