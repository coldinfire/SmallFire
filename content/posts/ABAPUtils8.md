---

title: " SM30表维护 "
date: 2018-06-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  ABAP

tags: 
  - abap
  - utils

---



### 表维护创建

#### 创建自定义表 

![自定义表创建](/images/ABAP/SM301.png)

需要设置为 Display/Maintenance Allowed.允许修改，才可以在SM30中进行数据维护。否则会产生以下异常：

![异常消息](/images/ABAP/SM3011.png)

#### 创建表维护

![创建表维护](/images/ABAP/SM302.png)

![设置](/images/ABAP/SM303.png)

权限组：控制访问权限

函数组：放在自定义的函数组即可

Maintenance type:

- one step：每次维护数据的时候不会产生请求号
- two step：每次维护数据的时候会产生请求号

Recording rountine:

- Standard recording routine
- no,or user,recording routine：在其他Client上有权维护此表数据

![屏幕号生成](/images/ABAP/SM304.png)

当保存完成后即可通过TCode：SM30，通过对应的表名就可以对已建立表维护的表进行数据的维护。

### 分配TCode

   通常建立的自定义表不允许用户直接查看，有时可能需要人工维护某些数据。这时候，可以通过SM30建立表维护的方式开放给用户，但是SM30的权限比较大不适宜直接分配；因此需要给对应的表维护另外分配TCode，来控制其权限。

#### TCode创建并分配

通过SE93创建TCode,并选择Transaction with parameters(parameter transaction)

![TCode分配](/images/ABAP/SM308.png)

创建界面需要配置default values和一些默认值：

- UPDATE=X：是否可以对数据进行维护
- VIEWNAME=需要维护的表名

![TCode分配](/images/ABAP/SM305.png)

#### 对维护表进行增强操作

可以通过增强的方式对维护表中的已知字段进行操作，比如每次修改后自动记录修改时间和修改人信息。

![查找源代码](/images/ABAP/SM306.png)

![Program](/images/ABAP/SM307.png)

![Source code](/images/ABAP/SM309.png)

#### 事件定义

![event](/images/ABAP/SM3010.png)

![event](/images/ABAP/SM3012.png)

- 01：数据库保存数据前
- 02：数据库保存数据后
- 03：删除已显示的数据之前
- 04：删除数据显示之后
- 05：建立新的条目.....