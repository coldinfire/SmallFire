---
title: " SAP Background Job "
date: 2019-04-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - saputils

---

### 定义 Background Job

#### SE38 执行可执行程序

- 菜单栏 program ---> Execute in Background

  ![后台JOB](/images/ABAP/JOB1.png)

- 输入输出设备

  ![输出设备](/images/ABAP/JOB3.png)

- 选择开始时间（立刻执行，或定义日期时间，也可周期执行）后保存。

  ![时间](/images/ABAP/JOB2.png)

#### SM36 定义作业名

- 填写 JOB 名称、优先级（A/B/C）和目标服务器。 一旦在目标服务器上安排了后台作业，就会在该服务器上运行。 定义目标服务器的主要目的是工作负载平衡。然后点击`Start condition`选择 job 开始时间（立刻执行，或定义日期时间，也可周期执行）后保存。

  ![Start Condition](/images/ABAP/JOB4.png)

- 点击`Step`，在字段中输入您的程序名称、Variant 名称。 如果没有 Variant，请将其留空。回到主界面后再保存。后台会在你定义的时间，自动执行按照变式的条件执行程序。

  ![Step](/images/ABAP/JOB5.png)

[如何在 SAP 中定义 Event 类型的后台 Job](<http://blog.sina.com.cn/s/blog_76c57b480100rumm.html>)

### 后台作业操作

#### Background Job 分类

A 类（高/关键优先级）：某些任务是紧急或关键的，必须与 A 类优先级作业一起安排。 A 类优先级保留一个或多个后台工作进程。 用户必须决定应该为 A 类优先作业分配多少后台工作进程。

- 假设用户为此类别选择了 2 个后台工作进程，那么 B 类和 C 类可用的后台工作进程 =（在操作模式 RZ03 中设置的工作进程总数）-（允许为 A 类类别的后台工作进程）。

B 类（中优先级）：一旦 A 类作业完成，B 类作业将在 C 类作业之前开始在后台执行。

C 类（低优先级）：在 A 类和 B 类作业完成后运行。

#### Background Job 状态类型

- Scheduled：job 创建了但是还没有 release，这种状态的 job 是不会执行的
- Released：job 在启动条件满足后会启动，Ready 就是启动条件满足后，系统开始为该 job 分配但尚未分配合适的后台进程的一个中间状态
- Active：job 正在运行当中
- Finished：job 得所有 step 都成功的完成了
- Canceled：job 在某一个 step 得运行过程中异常中止了。

#### 强制结束 Background Job（ SM37、SM35、SM50)

第一步：SM50

找到 "Ty." 列为 BGD 的(Background)，然后再找到你刚运行的那个后台 Job 的行，选中；然后在菜单点击：Process ---> Cancel with core 即可。

第二步：SM37 查看 Background Job，应该为 “取消” 状态。

第三步：SM35，选中 Session Name，点击小绿旗 release 即可。

#### Debug Background Job

事务码：SM37，选中要调试的 job，输入事务码 JDBG，回车，进入调试界面，根据需要打断点等就行了。

#### 批量删除 Background Job

SE38 执行 **RSBTCDEL2**  根据条件输入后台 Job 名或则根据用户天数，Commit 尽可能大一点，Test Run 勾上会显示满足条件的 Job 名字。

#### 查看 Background Job 执行记录

从表 TBTCO 或 TBTCP 中获取所需信息。

#### 如何查看一个 Background Job 对应哪些程序

SM36 -> 点击 job selection -> 运行结果双击选择 job -> 回到第一个界面，点击"Step"按钮，转到步骤清单总览，里面就可以看到程序名称了已取消，完成的 job 是不能修改的其它状态的都可以改，在SM37里，选择相应的job，菜单里有个更改项。

SM37 就是 SM36 里 job selection 的链接。

### Background Job 相关内置函数

| FM                | Description                                |
| :---------------- | :----------------------------------------- |
| JOB_OPEN          | 创建 job 并返回这个 job 的“内存ID”.        |
| JOB_SUBMIT        | 计划 job，需要为每一个 step 进行 submit    |
| JOB_CLOSE         | release 刚创建的 job；可以在SM37中查看结果 |
| SHOW_JOBSTATE     | 监控 JOB 状态                              |
| RS_VARIANT_EXISTS | 判断变式是否存在                           |
| RS_VARIANT_DELETE | 删除变式                                   |
| RS_CREATE_VARIANT | 创建变式                                   |