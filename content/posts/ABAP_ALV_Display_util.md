---
title: "Field Catalog 字段排坑"
date: 2018-06-29
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

原文链接：[https://blog.csdn.net/zhongguomao/article/details/77774206](https://blog.csdn.net/zhongguomao/article/details/77774206)



对于初写 ALV 的 ABAPer，it_fieldcat 参数经常有一些隐藏的小坑，需要多加注意。



01、字段名大小写的问题

- SAP 运行时是区分大小写的，也就是说，'a' 和 'A' 在 SAP 运行时是不同的字符，如果在 FIELDCAT 里面使用了小写或者大小写混合的字段名，SAP 就不会认识，导致出错。其实，不仅仅是 FIELDCAT，程序任何地方最好都要注意大小写。

02、导出到 Excel 或者文本最后一位丢失.加上参考表和参考字段就好了

- 字段对应的域 Convers. routine = ALPHA，也就是有前导零的字段，比如供应商号、商品号、客户号等。
- 做 ALV Fieldcat 的时候，没有指定参考表和参考字段。
- 列的表头文本 (seltext_s 等) 比实际显示的数据短。

03、默认变式导致改了 FLDCAT 看不到效果

- FIELDCAT 添加了新的字段，ALV 却不显示！看看是不是有默认的 Layout 变式吧。

04、CONVERTION 转换

- 显示的字段，物料的前导零没去掉、单位是内部单位、WBS 元素成了数字......这一切的原因都是来自于 CONVERTION 转换，没有做内外部转换，加上参考表和参考字段吧。
  - 要么这样：fldct-edit_mask = '==MATN1'.

05、编辑数值（数量、金额）字段总是给变成小数

- 在可编辑的 ALV 里面，数值型字段修改的时候，会把数据缩小 100（1000）倍，只要根据情况设置一下小数位就可以了：fldcat-decimals_out = 3.

06、汇总的时候 DUMP;导出的时候 DUMP

- 这个不仅仅是 FIELDCAT 的事儿，有时候 LAYOUT 设置不对也会导致 DUMP，比如设置 is_layout-coltab_fieldname = 'CELLC'. 但是内表根本就没有 CELLC 这个字段，就不行了。

07、筛选的时候，长度不对;可编辑字段不指定输出长度，编辑的时候默认只能输入 10 位

- 比如物料号，18 位，但是筛选弹出来的窗口只能输入 10 位。这个可以通过添加参考表参考字段解决，也可以使用 fldct-edit_mask = '==MATN1'. 还有设置 fldct-intlen = 18. 

08、 改变了数据元素或者 domain 之后，ALV 字段没反应 

- ALV 的字段参考了数据字段的内容，但是在修改了数据元素或者域后，ALV 字段并没有立即变成修改后的样子，然后第二天莫名其妙好了。这个实际上是 SAP 上下文的锅，只需要运行一下程序 BALVBUFDEL，重置下 ALV 缓冲区就可以了。

09、金额和数值显示小数位的问题

- SAP 开发有句行话：有数值必有单位，有金额必有货币。ALV 显示也是这样，如果不给指定单位或者货币字段，显示的可能就是不正确的。
  - 数值字段：fldct-qfieldname = 'MEINS'.
  -  金额字段：fldct-cfieldname = 'WAERS'. 



```JS
CASE ls_fldct-fieldname.
  WHEN 'BSTMG' OR 'BDMNG' OR 'MENGE'.
    ls_fldct-qfieldname = 'MEINS'.
    ls_fldct-decimals_out = 3.
    ls_fldct-no_zero = 'X'.
  WHEN 'WRBTR' OR 'KBERE' OR 'BFAAC'.
    ls_fldct-cfieldname = 'WAERS'.
    ls_fldct-decimals_out = 2.
  WHEN 'LIFNR' OR 'AUFNR' OR 'KUNNR'.
    ls_fldct-edit_mask = '==ALPHA'.
  WHEN 'MATNR' OR 'IDNRK'.
    ls_fldct-edit_mask = '==MATN1'.
  	ls_fldct-intlen = 18.
  WHEN 'BSTME' OR 'MEINS'.
    ls_fldct-edit_mask = '==CUNIT'.
  WHEN OTHERS.
ENDCASE.
```



