---
title: " SD Function module"
date: 2019-04-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  business

tags: 
  - SD

---

### Function Modules

**RV_ORDER_FLOW_INFORMATION** - Reads sales document flow of sales document after delivery and billing 
Don't forget also check direct reference documents for the both starting document # and preceding/following document types that you are searching for (For example, if you search delivery for the given SO, check also LIPS-VGBEL and LIPS-VGPOS, if it's possible with regard to performance). 

 **SD_SALES_DOCUMENT_READ** - Reads sales document header and business data: tables VBAK, VBKD and VBPA (Sold-to (AG), Payer (RG) and Ship-to (WE) parties) 

**SD_SALES_DOCUMENT_READ_POS** - Reads sales document header and item material: tables VBAK, VBAP-MATNR 

**SD_DOCUMENT_PARTNER_READ** - partner information including address. Calls SD_PARTNER_READ 

**SD_DETERMINE_CONTRACT_TYPE** - Determines, if document is service contract or quantity contract 
In: at least VBAK-VBELN 
Exceptions: NO CONTRACT | SERVICE_CONTRACT | QUANTITY_CONTRACT 

**SD_SALES_DOCUMENT_COPY** - copy Sales Doc into new one with the required Sales Doc Type (VBAK-AUART) for further creating. ExampleO - create subsequent document 

**SD_SALES_DOCUMENT_SAVE** - create Sales Doc from the copied document   

**SD_SALES_DOCUMENT_ENQUEUE** - to dequeue use DEQUEUE_EVVBAKE 

**SD_PARTNER_READ** - all the partners information and addresses 

**RV_DELIVERY_PRINT_VIEW** - Data provision for delivery note printing 

**SD_PACKING_PRINT_VIEW** 

**SD_DELIVERY_VIEW** - Data collection for printing 
called from RV_DELIVERY_PRINT_VIEW, SD_PACKING_PRINT_VIEW 

**RV_BILLING_PRINT_VIEW** - Data Provision for Billing Document Print 

**RV_PRICE_PRINT_HEAD** - To be used in print program to get pricing data on header [and item] level. 
Input: structures KOMK (fields mandt,kalsm,waerk,knumv,vbtyp to be taken from VBDKR, kappl='V'). 
[and KOMP (field kposn to be taken from VBDPR, field mglme (quantity) can be changed to calculate price accordingly]. 
Output: pricing data in tables TKOMV [and TKOMVD]. 

**BAPI_OUTB_DELIVERY_CONFIRM_DEC** - BAPI for Outbound Delivery Verification from a Decentralized System 
You can use this method to report back outbound deliveries from a WM system to an enterprise resource planning (ERP) system. 

**BAPI_OUTB_DELIVERY_CHANGE** - BAPI for Change to Outbound Delivery 

**BAPI_INB_DELIVERY_SAVEREPLICA** - Create Inbound Delivery 

**RV_DELIVERY_CREATE** - Create Delivery 

**GN_DELIVERY_CREATE** - Create an Outbound Delivery   

**SHP_VL10_DELIVERY_CREATE** - Create Outbound Delivery

**SD_COND_ACCESS** - Select condition records