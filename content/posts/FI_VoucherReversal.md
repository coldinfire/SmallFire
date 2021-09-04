---
title: " 财务凭证冲销 "
date: 2018-09-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - SAPbusiness

---

### 凭证的更改

#### 1：已经过帐的

TCODE：FB02.

过完帐的允许更改的地方有限，只有凭证抬头文本，参照，分配，文本，原因代码等

#### 2：预制凭证的更改.

TCODE：FBV2.

预制凭证可以更改的地方很多，只有凭证编码 + 公司代码 + 记帐码不允许更改.

如果科目错误，可以把金额置为 0 , 这样凭证保存后打印出来就不会含有那个科目了. 

### 凭证冲销

原则：通过后勤产生的会计凭证只能够通过冲销相应的物料凭证 (后勤凭证) 来达到冲销会计凭证的目的。二：固定资产的产生的凭证不可通过冲销，只可做一个相反的会计凭证来调整.

#### 1：财务模块手工输入的凭证的冲销

TCODE:FB08

输入凭证号码，会计年度，公司代码，冲销原因 (如果冲销当月凭证就选择 01, 以前月分的用 02 如果选择 02 需要输入记帐日期). 回车.

如果出现下面的显示 “财务中不能冲销的凭证” 就说明这不是通过财务做进去的凭证，而是后勤产生的凭证。不可在 FB08 冲销.

当输入凭证属于手工输入的凭证时，保存即可。就会出现提示：出现凭证 XXXXXXXXXX 已经保存。表明是冲销完成.

#### 2：MM 模块的凭证冲销

**2.1：MM 模块产生的会计凭证的冲销**

TCODE:MBST

输入凭证类型和记帐日期，会计年度等.

保存。系统出现：出现凭证 XXXXXXXXXX 已经记帐，表明冲销完成。以前物料凭证关联生成的会计凭证也相应的被冲销.

**2.2：发票校验的取消**

TCODE:MR8M

输入发票号码，冲销原因

保存即可.

提示需要手工清除会计的凭证的提示。表明已经无错误的冲销完成。然后要手工清除此两张凭证（它们是不能自动清账的）.

#### 3：SD 的凭证冲销

**3.1：SD 发货凭证的冲销**

注意：如果已经在系统中开票了，必须先冲销开票然后再冲销发货过帐。再才能按下列步骤进行 SD 发货凭证的冲销。

操作：

TCODE:VL09

输入相应的界定条件

系统根据用户输入列出所有交货凭证。

用户选中相应要冲销的凭证点击工具条的 “冲销” 按钮，系统会出现 “确实需要冲销次发货吗？” 提示框，选择 “OK” 按钮确认。

**3.2：发票的取消 (在 SD 开发票的时候错误)**

操作：

TCODE:VF11 （如果要反记账冲销，则在 S1 类开票类别中 “反记账” 填写 A/B）

输入要取消的发票号码

点击：保存

冲销完毕.
