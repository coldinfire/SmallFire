---
title: " Transfer Order(L_TO_CREATE_SINGLE) "
date: 2020-05-10
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM
  - BAPI

---

### L_TO_CREATE_SINGLE  使用

Create a transfer order with one item.

You can call this FM more than once if you want to transfer more items.

```JS
CALL FUNCTION 'L_TO_CREATE_SINGLE'
  exporting
	i_lgnum = wh_number  "Warehouse number"
	i_bwlvs = '999'      "MvT999"
	i_matnr = lqua-matnr "Material no."
	i_werks = lqua-werks "Plant"
	i_lgort = lqua-lgort "Storage location"
	i_anfme = lqua-verme "Requested Qty"
	i_altme = lqua-meins "Unit of measure"
	i_letyp = ltap-letyp "SU Type"
	i_vlpla = lqua-lgpla "Source storage bin"
	i_vlqnr = lqua-lqnum "Quant"
	i_vltyp = lqua-lgtyp "Source storage type"
	i_nlpla = ltap-nlpla "Destination storage bin"
	i_nltyp = v_nltyp    "Destination storage type"
	i_squit = squit      
  importing
	e_tanum = l_tanum    "Transfer Order"
	e_ltap  = l_ltap     
  exceptions
	no_to_created = 1
	benum_missing = 2
	bwlvs_wrong = 3
	betyp_missing = 4
	foreign_lock = 5
	vltyp_wrong = 6
	vlpla_wrong = 7
	vltyp_missing = 8
	nltyp_wrong = 9
	nlpla_wrong = 10
	nltyp_missing = 11
	rltyp_wrong = 12
	rlpla_wrong = 13
	rltyp_missing = 14
	squit_forbidden = 15
	manual_to_forbidden = 16
	letyp_wrong = 17
	vlpla_missing = 18
	nlpla_missing = 19
	sobkz_missing = 20
	sonum_missing = 21
	bestq_wrong = 22
	lgber_wrong = 23
	xfeld_wrong = 24
	date_wrong = 25
	drukz_wrong = 26
	ldest_wrong = 27
	update_without_commit = 28
	no_authority = 29
	material_not_found = 30
	lenum_wrong = 31
	others = 32.
```

