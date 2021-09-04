---
title: "SAP 后台JOB"
date: 2019-04-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils
  - JOB

---

### 定义后台 job

第一种：SE38执行可执行程序

- 菜单栏‘program’--->'Execute in Background'

  ![后台JOB](/images/ABAP/JOB1.png)

- 输入输出设备

  ![输出设备](/images/ABAP/JOB3.png)

- 选择开始时间（立刻执行，或定义日期时间，也可周期执行）后保存。

  ![时间](/images/ABAP/JOB2.png)

第二种：SM36定义作业名

- 点击`Start condition`选择job开始时间（立刻执行，或定义日期时间，也可周期执行）后保存.

  ![Start Condition](/images/ABAP/JOB4.png)

- 再点击`Step`，填写abap程序NAME和Variant后保存，回到主界面后再保存。后台会在你定义的时间，自动执行按照变式的条件执行程序。

  ![Step](/images/ABAP/JOB5.png)

[如何在 SAP 中定义 Event 类型的后台 Job](<http://blog.sina.com.cn/s/blog_76c57b480100rumm.html>)

### 强制结束后台作业（ SM37 SM35 SM50)

第一步：SM50

找到 "Ty." 列为 BGD 的(Background)，然后再找到你刚运行的那个后台 Job 的行，选中；然后在菜单点击：Process ---> Cancel with core 即可。

第二步：SM37 查看 Background Job，应该为 “取消” 状态。

第三步：SM35，选中 Session Name，点击小绿旗 release 即可。

### 如何查看一个后台job对应哪些程序?

SM36 -> 点击 job selection -> 运行结果双击选择 job -> 回到第一个界面，点击"Step"按钮，转到步骤清单总览，里面就可以看到程序名称了已取消，完成的 job 是不能修改的其它状态的都可以改，在SM37里，选择相应的job，菜单里有个更改项。

SM37 就是 SM36 里 job selection 的链接。

### 调试后台 Job

事务码：SM37，选中要调试的 job，输入事务码 JDBG，回车，进入调试界面，根据需要打断点等就行了。

#### 后台JOB状态类型

- Scheduled：job 创建了但是还没有 release，这种状态的 job 是不会执行的
- Released：job 在启动条件满足后会启动，Ready 就是启动条件满足后，系统开始为该 job 分配但尚未分配合适的后台进程的一个中间状态
- Active：job 正在运行当中
- Finished：job 得所有 step 都成功的完成了
- Canceled：job 在某一个 step 得运行过程中异常中止了。

### 批量删除后台作业

SE38执行 **RSBTCDEL2**  根据条件输入后台Job名或则根据用户天数，Commit尽可能大一点，Test Run勾上会显示满足条件的Job名字.

### 查看后台 JOB 执行记录

从表 TBTCO 或 TBTCP 中获取所需信息。