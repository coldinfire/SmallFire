---
title: "Smartforms 分页打印"
date: 2018-07-29
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---



### Smartform中的table节点插入分页

#### Command Node

在smartforms的loop节点中可以插入一个command node用来强制分页，那么如何在table节点中插入分页的command node呢？这要利用到table节点的sort event.

在table节点的data tab页中勾上event on sort end，别忘了输入要排序的字段，然后就会在左边table节点的最后出现一个Event on Sort End的节点，在这个下面插入command node进行分页就可以了。

![Command node](/images/ABAP/smartform_page1.png)

在command的条件选项卡中设置检查字段值。选中复选框(Go to New Page)，然后提供要显示的下一页。

![Command node](/images/ABAP/smartform_page2.png)

#### 注意事项：

- 这么做相当于，在table中根据某个字段进行分页，值相同的行记录分到一个page中。
- 最后会多分一个空页，需要根据自己的table中的数据，通过command 节点中的condition进行控制。
- 打印参数控制打印页数：ssfcompop-tdpageslct

### Page Break in a smartforms

根据字段值进行分页，比较该字段值，如果该值被更改，则进行分页。

- 创建一个带有主窗口的页面，将项目详细信息显示为smartform中的Table。
- 在主窗口内创建一个循环，循环内部表数据。
- 在循环中创建一个命令，在命令的条件选项卡中检查字段值。
- 选中复选框（转到新页面），然后提供要显示的下一页。
- 在命令下创建文本以显示数据。
- 根据在命令条件选项卡中给出的条件，分页符将自动被触发，所需的数据将流到下一页。