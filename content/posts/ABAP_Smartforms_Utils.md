---
title: "Smartforms 工具集合"
date: 2018-07-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---

### 中文乱码问题

如果预览时中文正常，打印出来是乱码的情况，可以尝试在中文环境下重新维护并激活。再次打印应该可以解决。如果还有问题，则检查语言包是否支持中文(或则其他需要使用的语言)。

### Table 和 Template 区别

- `Table` 行为动态，数据输出时会根据列宽自动换行，可以固定列宽，
  但是默认情况下控制不了行高，如果要想 template 一样固定行高，需要将 table 的无换页属性打钩；通过 Main window （ table 控件）的高度来自动翻页.
- `Template` 为静态，固定列宽、行高，当输出数据过长时会自动截断，通常被用于静态表单开发。Template 跟 loop 嵌套使用，可以实现固定行高、列宽的表单开发。需要手动翻页，并程序中计算页码；（或则在程序中设计好固定行数+计算页码）.

### Smartform Debug

1. 使用事物码 Smartforms 进入，输入 Form name 或则通过事物码 NACE 查找到的表单
2. 在表单中找到需要 Debug 的一段代码，将代码复制，后续在该位置设断点
3. 在导航栏中通过 Environment –> Function Module Name 获取 Function module
4. 在 SE37 中打开该 Function Module，进入 Main program 找到 Perform GLOBAL_INIT
5. 该 FORM 中进行所有的程序数据初始化，所有 smartform 中的程序行在此处定义，可以打断点
6. 或则在 Main program 中找到想要打断点的代码位置，运行 Smartform，会进入断点，进行 Debug

### 输出格式设置

所有数字变量后面一般都要加 (C)，数字靠右，文字靠左

| Syntax     | Desc         | Syntax      | Desc                            |
| ---------- | ------------ | ----------- | ------------------------------- |
| &field(n)& | 只显示前n位  | &field(.n)& | 显示n为小数位                   |
| &field(S)& | 忽略正负号   | &field(T)&  | 忽略千分位分隔符                |
| &field(<)& | 符号在左边   | &field(L)&  | 将日期转换为本地格式显示        |
| &field(>)& | 符号在右边   | &field(I)&  | 如果字段INITIAL，不输出         |
| &field(Z)& | 不输出前导零 | &field(nR)& | n位显示，居右(必须设置长度才可) |
| &field(C)& | 空格压缩     | &field(Z)&  |                                 |

### 页码混乱问题

使用 SFSY-FORMPAGES 显示总页数的时候，如果页数大于 9,，将会在前 10 页显示成星号。

解决办法：可以添加 3ZC，&SFSY-PAGE(3ZC)&/&SFSY-FORMPAGES(3ZC)&，不过可能会出现字体颠倒或者重叠的现象，用一个单独的窗口来存放显示页码的文本，并且把窗口的类型设置为 L (最终窗口) 就 OK 了。

### 金额数字显示问题

如果金额或者数量字段显示不出来的话，可以在 “货币 / 数量字段” 标签中指定相应的数据类型。

### Smartforms 系统字段

| Field            | Description        | Field             | Description                      |
| :--------------- | :----------------- | :---------------- | :------------------------------- |
| &SFSY-DATE&      | 日期显示           | &SFSY-PAGENAME&   | 当前页的名字                     |
| &SFSY-TIME&      | 时间格式为HH:MM:SS | &SFSY-USERNAME&   | 当前用户的名字                   |
| &SFSY-PAGE&      | 当前打印页的页号   | &SFSY-WINDOWNAME& | 返回当前窗口的名字               |
| &SFSY-FORMPAGES& | 当前文档的总页数   | &SFSY-JOBPAGES&   | 当前打印请求中所有文档的合计页数 |

