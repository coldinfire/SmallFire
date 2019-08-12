---
title: "SAP后台JOB"
date: 2019-04-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abap
  - utils

---

### 定义后台 job

- 第一种：SE38执行可执行程序

  - 菜单栏‘program’--->'Execute in Background'

    ![后台JOB](/images/ABAP/JOB1.png)

  - 输入输出设备

    ![输出设备](/images/ABAP/JOB3.png)

  - 选择开始时间（立刻执行，或定义日期时间，也可周期执行）后保存。

    ![时间](/images/ABAP/JOB2.png)

- 第二种：SM36定义作业名

  - 点击‘Start condition’选择job开始时间（立刻执行，或定义日期时间，也可周期执行）后保存.

    ![Start Condition](/images/ABAP/JOB4.png)

  - 再点击‘Step’，填写abap程序‘NAME’和‘Variant’后保存，回到主界面后再保存。后台会在你定义的时间，自动执行按照变式的条件执行程序。

    ![Step](/images/ABAP/JOB5.png)

[如何在 SAP 中定义 Event 类型的后台 Job](<http://blog.sina.com.cn/s/blog_76c57b480100rumm.html>)

#### 强制结束后台作业（SAP SM37 SM35 SM50)

解决方法：

第一步：SM50

​	找到Ty. 列为 BGD 的(Background)，然后再找到你刚运行的那个后台 Job 的行，选中；然后在菜单点击：Process---Cancel with core. 即可。

第二步：SM37 查看 Background Job，应该为 “取消” 状态。

第三步：SM35，选中 Session Name，点击小绿旗 release 即可。

#### 如何查看一个后台job对应哪些程序?

​	sm36-->点击“job selection”-->运行结果双击选择job-->回到第一个界面，点击“步骤”按钮，转到步骤清单总览，里面就可以看到程序名称了已取消，完成的job是不能修改的其它状态的都可以改，在sm37里，选择相应的job，菜单里有个更改项
sm37就是sm36里job selection的链接.