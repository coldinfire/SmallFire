---
title: " SAP CDS-Views "
date: 2021-11-22
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - SAP Fiori

tags: 
  - HANA

---

### Create CDS-Views

#### CDS-Views

`Menu -> File -> New -> Other -> Core Data Services -> Data Definition`

```ABAP
* CDS-View als DDIC Objekt erstellen
@AbapCatalog.sqlViewName: 'ZCDS_MATNR_VIEW'
@AbapCatalog.compiler.CompareFilter: true
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'MATNR lesen'
@Metadata.allowExtensions: true

define view ZCDS_MATNR 
  as select from mara as a 
  inner join makt as b on a.matnr = b.matnr
{
 key a.matnr as matnr,
     a.ernam as creator,
     a.vpsta as mainte_status,
     b.maktx as desc,
 case a.lvorm
  when 'X' then 'delete'
  else 'active'
  end as client_del
};
```

使用 CDS-Views

```ABAP
* Variante 1 (SELECT)
DATA: it_matnr TYPE STANDARD TABLE OF zcds_matnr.
SELECT * FROM zcds_matnr INTO TABLE @it_matnr.
LOOP AT it_matnr ASSIGNING FIELD-SYMBOL(<mat>). 
  WRITE: / <mat>-matnr, <mat>-creator, <mat>-mainte_status, <mat>-desc, <mat>-client_del.
ENDLOOP.
* Variante 2 (SALV TABLE IDA)
TRY.
  cl_salv_gui_table_ida=>create_for_cds_view( 
        CONV #( 'ZCDS_MATNR' ) )->fullscreen( )->display( ).
  CATCH cx_root INTO DATA(e_txt).  
  WRITE: / e_txt->get_text( ).
ENDTRY.
```

#### CDS-Views With Parameter

```ABAP
* CDS-View als DDIC Objekt erstellen
@AbapCatalog.sqlViewName: 'ZCDS_MATNR_VIEW'
@AbapCatalog.compiler.CompareFilter: true
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'MATNR lesen'
@Metadata.allowExtensions: true
 
define view ZCDS_MATNR
  with parameters p_min_date : dats,
                  p_max_date : dats
  as select from mara as a
  inner join makt as b on a.matnr = b.matnr
{
 key a.matnr as matnr,
     a.ernam as creator,
     a.vpsta as mainte_status,
     b.maktx as desc,
  case a.lvorm
    when 'X' then 'delete'
    else 'active'
  end as client_del
} where a.ernam like 'A%' and a.ersda between :p_min_date and :p_max_date;

* ABAP-Selection CDS-Views
DATA: it_matnr TYPE STANDARD TABLE OF zcds_matnr.
SELECT * FROM zcds_matnr( p_min_date = '20210101', p_max_date = '20211231' ) 
  INTO TABLE @it_matnr.
LOOP AT it_matnr ASSIGNING FIELD-SYMBOL(<mat>).
  WRITE: / <mat>-matnr, <mat>-creator, <mat>-mainte_status, <mat>-desc, <mat>-client_del.
ENDLOOP.
```

