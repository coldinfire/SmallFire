---
title: "ABAP BTE增强使用"
date: 2019-12-21
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 配置BTE

Tcode：FIBF

![BTE](/images/ABAP/BTE1.png)

![Add entries](/images/ABAP/BTE3.png)

![BTE](/images/ABAP/BTE2.png)

![BTE](/images/ABAP/BTE4.png)

BTE查找技巧：

```JS
BTE 技巧：
  1）在 FM：`BF_FUNCTIONS_FIND`，设断点，然后查看 CALL FUNCTION 'BF_FUNCTIONS_READ' 里面的变量，可以查看这个流程，会触发那些 event，以及那些 customerFM，这样就可以在相应的 EVENT 开发对应的 ZFM。
  2）查找对应合适的 BTE：运行事务码 XD02，查找到对应的程序为 SAPMF02D，在此程序中搜索字符串 “OPEN_FI_PERFORM”，可以找到此程序中的所有用到的 BTE。
```

​    
