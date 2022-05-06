---
title: " Product Order相关信息的获取 "
date: 2022-04-17
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - PP

---

### 工序获取（TABLE：AFVC）

根据 Production Order 到表 AFKO 中获取 Routing 信息： AFKO-AUFPL；根据 Routing 到表 AFVC 中获取 Operation（工序）详细信息：AFVC-VORNR（Operation）、AFVC-LTXA1（Operation Text）、AFVC-ARBID（Object ID）、AFVC-LAR01（Activity Type）。

### Work Center（TABLE：CRHD）

通过 AFVC-ARBID 到表 CRHD 中获取有关 Work Center 信息。AFVC-ARBID = CRHD-OBJID 获取 CRHD-ARBPL（Work Center）、CRHD-WERKS（Plant）、CRHD-VERWE（Work Center Category），CRHD-VERAN（Person responsible）。

通过表 CRTX 可以获得 Work Center 描述，CRTX-OBJID = CRHD-OBJID 获得 CRTX-KTEXT（Work Center Description）。

### 工作中心负责人（TABLE：TC24）

通过 CRHD-VERAN 到表 TC24 获取 Work Center 负责人。CRHD-VERAN = TC24-VERAN and CRHD-WERKS = TC24-WERKS 获取 TC24-KTEXT（Work Center 负责人）。

### 成本中心（TABLE：CRCO）

通过 CRHD-OBJID 到表 CRCO 中获取成本中心。CRHD-OBJID = CRCO-OBJID 获取 CRCO-KOKRS（Controlling Area）、CRCO-KOSTL（Cost Center）、CRCO-LSTAR（Activity Type）。

### 工种（TABLE：CSLT）

通过 CRCO-KOKRS & CRCO-LSTAR 到表 CSLT 获取 CSLT-KTEXT（工种信息）。