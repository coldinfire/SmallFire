---
title: "Authorization"
date: 2018-09-22T13:15:42+08:00
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - 权限管理

---


## 简介

### TCode

​	SU01：查看和编辑Role

​	SU10：实现对用户组的批量维护


​	SU20：权限字段清单，可以增、删、改权限字段，可以浏览字段具体被哪些权限对象所调用

​	SU21：维护权限对象，可以创建和维护权限类，权限对象，权限字段在该事务码中被分配到权限对象

​	SU22：维护权限对象的分配，可以通过该事物代码为具体事务分配权限对象

​	SU24：将Authorization Object Assign 到 T-Code 上

​	SU53：当前用户权限不足问题查看

  SUIM：用户、角色、权限对象、事务等之间的关系查看

​	PFCG：进入权限角色维护界面，创建Role 设置Role的Authorization Object

### Role

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

​		 

## 2.权限对象	

- 角色包含了若干权限对象、在透明标`AGR_1250`中存储二者之间的关系。

- 在SAP实际应用中，用户所直接操作的是屏幕及屏幕对应的字段，这些字段都是由权限对象进行控制，
  包括该字段所允许的操作及所允许的值。

- SAP权限对象(Authorization Object)： 权限对象设置后，需要绑定到事务码上，然后在ABAP程序
  中通过AUTHORITY-CHECK OBJECT 语句来做权限检查。权限对象就起作用了。

​	权限对象包含了若干权限字段、允许的操作和允许的值：在透明表`AGR_1251`中体现了ROLE/Object/Field/Value之间的关系；有一个特殊的权限对象包含若干
事务码。这个权限对象叫“S_TCODE”，该权限对象的权限字段叫“TCD”，该字段允许的值存放的就是事务代码；
在透明表USOBX中，存放了事务码与权限对 象的对应关系。 

- 权限字段(Authorization Field)： 分配取值 
  	

  - ACTVT:该字段存放的就是允许操作的代码。
    	
  - TCD: 存放该权限角色所包含的事物代码。

  - 允许的操作(Activity):  维护具体的选项操作。

  - 允许的值(Field Value): 值代表每个选型的功能。
    	

  - 保存并激活后使该权限参数文件生效。

## 3.在程序中调用权限对象
```JS
AUTHORITY-CHECK OBJECT 'XXX'
  ID 'XXX1' FIELD field
  ID 'XXX2' FIELD field
  ID 'XXX3' FIELD field
  ID 'XXX4' FIELD field.
IF SY-SUBRC <> 0.
  MESSAGE 'XXXXX'
ENDIF.
OBJECT: 表示具体的权限对象。
ID： 表示权限字段，可以同时检查权限对象中的一个或多个权限字段。
FIELD： 权限字段所检查的权限值，将该字段改为屏幕元素，可检查该字段所输入的值是否符合要求。
  *类似一个大致的权限矩阵，纵向是操作人（ID），横向是某些权限对象，权限对象再细分成若干
  事务代码、允许动作、权限字段及其允许的值等。
```
可参考:[SAP用户权限控制设置及开发](https://blog.csdn.net/candy_mmyy/article/details/54906571)、 [用SAP Authority Object 对权限控制](https://www.cnblogs.com/long2006sky/archive/2009/06/07/1498029.html)

## 4.权限授权解决

```JS
SU01: 输入UserID查看和编辑Role
	  Role: menu(T-Code)、Authorization(Display Auth)、Organization levels
SU53: 当前用户权限不足的原因
SUIM: 查找用户及Role等的具体信息，确定问题点，及可以解决的方法
PFCG: 维护Role、Menu修改
Role Menu: SAP_(SAP 标准)       Z_WIK_ALL(用户无关)
		   Z_U_(混合角色不适用)  Z_模块名_(对应的Role编码)
```
