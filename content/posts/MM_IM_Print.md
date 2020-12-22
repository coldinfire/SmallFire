---
title: " Output Determination in Inventory Management (IM) "
date: 2020-07-09
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

***引用链接：[Output Determination in Inventory Management (IM)](https://wiki.scn.sap.com/wiki/display/ERPSCM/Output+Determination+in+Inventory+Management+(IM)?original_fqdn=wiki.sdn.sap.com#OutputDeterminationinInventoryManagement%28IM%29-1.Aboutthisdocument)***

## 1. About this document

This document describes the output determination in Inventory Management.

- Chapter 3 deals with the program logic and is intended to developers.
- Chapters 4 to 23 describe the single elements of output determination. Some are purely MM-IM, others are general output determination concepts. The chapters may be used as reference when dealing with a special part of the output determination. They contain general explanations as well as technical descriptions.
- Chapters 24/25 concentrate on **troubleshooting**. The first part gives a list of important breakpoints and checks. The second part is organized in the major symptoms that arise when dealing with issues about output determination in MM-IM area.
- Chapter 26 contains a list of all output determination terms in German and English.

## 2. General information

The output determination (also called message control) generates output records (messages) for a material document. The messages can be GR/GI slips (e.g. output type WE01), mails (output type MLFH for missing part mails) or even workflow events (not in standard but can be customized by customer).

The output determination program is a central functionality outside MM-IM. It is also used by MM-PUR, SD etc.
CSS component for the central program (if bug is not due to calling application like MM-IM): **CA-GTF-BS-MC**

Principle:
The application using the output determination provides the output determination program with application data (like plant, storage location) through communication structures (MM-IM: communication structures KOMKBME for header data and KOMPBME for item data). The output determination program uses these application data to determine - with the help of extremely flexible and complex tables - whether an output type is relevant or not. If an output type can be found for the given criteria (application data), an output record will be generated.

For more information about Customizing of Output determination see IMG.

## 3. Output records for a material document (table NAST)

The output records are stored in table NAST. Table Key:

| Field name | Field description | Field value used in MM-IM                                    |
| :--------- | :---------------- | :----------------------------------------------------------- |
| KAPPL      | Application       | 'ME' (Constant for MM-IM)                                    |
| OBJKY      | Object key        | Is composed of: - MBLNR Material document number - MJAHR Material document year - ZEILE  Material document item |
| KSCHL      | Output type       | Examples: WE01, WE02, WE03, WA01, MLGR, MLFH                 |

Automatic output records
Usually output records are generated in the background during a goods movement, if customizing is set correctly. The user does not bother to see which output types will be found.

Technical process (only for ABAP developers):
In SAPMM07M the following routines are processed:

1. In Form-Routine **DRUCK_PRUEFEN (MM07MFD0)** field **MKPF-WEVER** is set depending on the print field (RM07M-XNAPR) and the default print version (T158-WEVER) or the print version chosen on the initial screen.
2. The print indicator of the movement type is read from table T156 **(T156-KZDRU)**
3. In **BUCHEN_NUMMERNVERGABE** a few routines are processed:
   - **KOPFDIALOGSTRUKTUR_FUELLEN:** Header data from MKPF (like transaction type VGART, Version WEVER) are given to the communication structure KOMKBME.
   - **POSIDIALOGSTRUKTUR_FUELLEN:** Item data from MSEG (e.g. print indicator of movement type T156-KZDRU, plant, storage location) are given to communication structure KOMPBME.
     Both communication structures KOMKBME and KOMPBME will be transmitted to the output determination and are used for reading the condition tables.
   - **MESSAGING:** here the actual output determination is processed. The output types that have been found relevant are stored in internal table XNAST.
4. In **BUCHEN_AUSFUEHREN** function module **MB_MESSAGE_UPDATE** is called in dialog. First it replaces the mail variables in dialog with function module **RV_MESSAGES_MAIL_REPLACE**. This is necessary for goods movements posted with **MB_CREATE_GOODS_MOVEMENT** because **MB_POST_DOCUMENT** resets all internal tables (**MB_MOVEMENTS_REFRESH** is called) and we need the internal tables to fill the mail variables (notes 205951 + 213264). Then it sets a **PERFORM ON COMMIT**, which itself sets a **CALL FUNCTION IN UPDATE TASK**. This is necessary to ensure that data such as batch classification data or QM inspection lot are available for printing.
5. In the update task the output records from internal table XNAST are written in DB table NAST. Table NAST is linked to table MSEG through the object key.

Manual output records
If customizing is not set correctly, it might be necessary to enter the output type manually during creation (MB01) or change (MB02) of material document.

Menue: **Detail > More functions > Messages/Output** (also available as pushbutton from 4.0 on)

**Technical process when working with the output maintenance full screen:**

If the users calls the full screen of output maintenance before posting, the output determination will not be processed in **BUCHEN_NUMMERNVERGABE.** It will already be processed before sending the full screen. That way, it is possible to display the output types that have been found automatically.

**NACHRICHTEN_PFLEGE (MM07MFM0)**

- NACHRICHT_ERZEUGEN
  - **KOPFDIALOGSTRUKTUR_FUELLEN (MKPF -> KOMKBME)**
  - **POSIDIALOGSTRUKTUR_FUELLEN (MSEG -> KOMPBME)**
  - **MESSAGING** (output determination)
- **RV_MESSAGES_MAINTENANCE:** sends the output maintenance full screen (not MM-IM)

In MB01 all output records will be renumbered in **BUCHEN_NUMMERNVERGABE** and given to the update task in **BUCHEN_AUSFUEHREN** through function module **MB_MESSAGE_UPDATE**.

In MB02 function module **MB_MESSAGE_UPDATE** is called directly after the output maintenance.

You can recognize manual output records in table NAST with field **NAST-MANUE** (check with SE16)

**MIGO**
In Release 4.6B and 4.6C we do not have the output maintenance screen. Therefore it is not possible to enter manual output types. In material document display it is possible to start the automatic output determination with a checkbox 'Print' in the header general data. It is also possible to get to the output full screen in display mode.

**Why is it not possible to maintain output types manually in MIGO?**
We assume that a user only has to maintain manual output types because the customizing is not set correctly. There is no real justification for having to maintain manual output types.

Deleting output records
Output records are linked to the material document through the object key and are therefore deleted during the archiving of the material documents.

Output determination processing: overview

![Output determination processing](/images/MMGR/1_Determining_Process.jpg)

## 4. Print version for transaction (T158-WEVER)

Radio buttons on initial screen of MB01

There are 3 print versions ('1', '2', '3'). The description of the print versions (individual slip, collective slip) is only correct if the customizing is set as SAP recommends. If the customer creates a condition record for an individual slip with the version 3 (collective slip), he will get an individual slip when the users chooses radio button 'collective slip'.

Default-Version **(T158-WEVER):** determines which print version is set on initial screen of transaction.

If field 'Print' is not set, the version will be cleared. That way the system will not find any condition record and will not print any slip.

**MB_CREATE_GOODS_MOVEMENT**
Because MB_CREATE works without initial screen, we always use default version for transaction from T158. If printing with MB_CREATE does not work, check that the IMKPF transaction has a print version in T158. In case of goods receipt for purchase orders with multiple account assignments, the default version of T158 in only used if '1' or '2'. In case of collective slips, the version is changed to '1' after application of note 442706 in order to be consistent with the dialogue (transaction MB01).

**MIGO**
In transaction MIGO the print version is available since note 315434.

## 5. Field 'Print' (RM07M-XNAPR)

Check box on initial screen and header data screen of MB01. Is set from userparameter NDR = X.
X has to be written in capital letters, otherwise basis error 00002 'please enter valid value' on initial screen.

With this field it is possible to deactivate the printing of GR/GI slips (but not to deactivate mails)

Technically the field is used in Form routine **DRUCK_PRUEFEN (MM07MFD0)** to reset the print version **(MKPF-WEVER).** That way all output types working with the print version are deactivated.

**Caution: deactivation of output determination not possible !**
With the field 'Print' it is not possible to deactivate the output determination completely. The output determination is always processed, even if field 'Print' is not set, because it still might be necessary to generate some mails. The output determination can only be deactivated through coding modification.

**Caution: field 'Print' only works for condition tables with version !**
If the customer uses own condition tables without the version, the field 'Print' will have no effect. The system will find a condition record and will print a slip. Explained in note 205450.

**Setting of field 'Print' and user name in MB_CREATE_GOODS_MOVEMENT**
Because MB_CREATE_GOODS_MOVEMENT works without initial screen, field 'Print' is set exclusively from the parameter ID NDR of a user record. It is possible to give MB_CREATE_GOODS_MOVEMENT a user name for print in field **IMKPF-PR_USNAM** (if initial, the system works with **IMKPF-USNAM.** If **IMKPF-USNAM** is initial, the system works with SY-UNAME). The value of parameter NDR is read directly from the user data (table USR05) and not through **GET PARAMETER NDR**, because the user might work parallely with MB11 and there change the parameter NDR from field RM07M-XNAPR.

If printing with MB_CREATE_GOODS_MOVEMENT does not work, check the user has parameter NDR = X.
Selecting or deselecting NDR does not react like a selection ON/OFF in a transaction if you use a BAPI.
The parameter and the BAPI are in two different memories. The BAPI cannot then recognize that the parameter has been deselected.

**MIGO**
The field still exists as header data (field **GOHEAD-XNAPR** on header tabstrip 'General').

## 6. Print indicator for movement type (T156-KZDRU)

Because most movement types do not need a specific GR/GI slip, we do not use the movement type directly in the condition tables. Instead we use the print indicator from table T156.

- Most movement types have print indicator '1' (normal GR/GI slip).
- Mvt 122 has print indicator '2' to distinguish a return delivery slip from a GR slip.
- Mvt 541 has print indicator '3' because it needs a special condition table with debit/credit indicator (SHKZG) so that only one line is printed and not the automatic line.
- Mvt 501 has print indicator '6' (4.0) because it is possible to create 501M lines in MB01. MB01 prints normally a GR slip with PO data (mvt 101 uses output type WE01), but mvt 501 needs a GR slip without PO data (output type WA01). As both mvt types use the same condition table, it was necessary to distinguish them with the print indicator.

## 7. Output type (WE01, MLFH...)

The output type determines what kind of output (slip, mail, workflow event) is generated. Output types for GR/GI slips are directly linked to a SAPScript layout set and to a read routine in table TNAPR. Individual slips and collective slips have different output types because they use different layout sets and have different read routines. Furthermore the output type has a printer parameter (7, 9, U, X) that is used for the printer determination.

For more information see IMG.

Common errors:

- Customer has assigned the wrong program to an output type in table TNAPR. For example he wants to have PO data on the slip but uses the routine for other goods receipt
- Customer has entered an own read program in TNAPR that does not work.

Always check table TNAPR when analysing an output type issue.

## 8. Message schema (ME0001)

A schema gathers all output types that have to be checked by the output determination. In Inventory Management we use only one schema **'ME0001'**. The schema is a program constant and cannot be changed by the customer. The customer can add his own output types to our standard schema.

The output determination program loops over all output types that are defined in the schema and tries to find a condition record for that output type in the corresponding condition table.

In the schema it is possible to define a requirement for an output type. For example, collective slips need requirement 173 so that they will be printed for the first line only. For that purpose the Form routine KOBED_173 (in SAPLV61B) is processed.

## 9. Condition table

A condition table contains the criteria that are relevant for an output type. Our normal condition table is B072 for normal GR/GI slips (can be displayed with SE16). Table key:

- KAPPL Application (always 'ME')
- VGART Transaction type (WE, WA, WR) from T158. Never use WO because we do not print slips for MB04. The transaction type cannot be changed by the customer.
- WEVER Print version from T158 (1 and 2 for individual slip, 3 for collective slip)
- KZDRU Print indicator for movement type (1, 2, 3, 6)

Mails and mvt 541 have their own condition tables.

Customer may create an own condition table but SAP doesn't recommend it.

**New field in field catalog**
A customer may create a condition table with all fields that are contained in our field catalog. The field catalog is defines in table KOMK and is filtered for each application in table T681F. It is filled by our communication structures KOMKBME and KOMPBME. Customer may add and fill new fields as we have user-exits for all communication structures, but they have to add the fields to the application field catalog (table T681F, can be maintained with transaction NACE)

## 10. Condition records

Condition records are entries in condition tables.

Example: table B072 (application: always 'ME')

| VGART | WEVER | KZDRU | Output type                         |
| :---- | :---- | :---- | :---------------------------------- |
| WE    | 1     | 1     | WE01 (used for 101 individual slip) |
| WA    | 1     | 1     | WA01 (used for 201 individual slip) |
| WA    | 3     | 1     | WA03 (used for 201 collective slip) |

In IMG you can find a list of condition records that have to be created by the customer if he wants to use the standard settings.

Common error:
Create a condition record with transaction type WA and output type WE*. That way he gets a GR slip with empty PO data.

Always check in the condition table B072 (with SE16) if the condition records have been created as described in IMG and if customer output types are used. It gives you a hint if customer has understood the output determination and uses own output types. You can also check which output types  he uses as a lot of customers only create condition records for the output types they use.

**No transportation of output records:**
Output records are master data with automatic numbering. When you create a condition record in Customizing (transaction MN21), the system creates an entry in table B072 with a condition number (e.g. 4711). The condition number refers to the master data in table NACH (with key 4711). It is not possible to transport the condition records because the record 4711 might already exist in the target system and refer to another condition table than B072. If you have transported a condition record manually (transport of B072 entry or NACH entry), you have an inconsistency in table NACH which can cause MN21 to abort. The transported entries need to be deleted with a correction program and re-created manually with MN21. This is not a hotline problem as this is a wrong customer transport.

## 11. Access sequence

The access sequence is the link between output type and condition table.

In the output type you enter an access sequence (e.g. 0003). The access sequence contains the condition table that will be checked for the output type.

Theoretically it is possible to assign several condition tables to an access sequence but this is not necessary in MM-IM. So you will always find a 1:1 relationship between condition table and access sequence.

## 12. Layout sets (SAPScript)

You can find the layout set used for an output type in table TNAPR. Our standard layout sets begin with WESCHEIN*, WASCHEIN*. We recommend the customer to copy our standard layout set (Z...) if he needs additional fields. Our read routines in table TNAPR fill all MKPF, MSEG and PO data fields. The customer does not need an own read program if he uses fields from these tables. If he needs own form routines (e.g. to read the batch classification data), he can implement an exit-routine directly in the layout set (using /: PERFORM as SAPscript command).

Transaction: SE71, layout set: e.g. WESCHEINVERS1 for output type WE01

**Connection between text element of layout set and print program**
A layout set has windows (e.g. MAIN) and a window has text elements (e.g. W1LGMAT, W1VERBRMAT). A text element represent a few lines that will be printed together (e.g. W1LGMAT for item information in case of PO without account assignment, W1VERBRMAT for item information in case of PO with account assignment).

The print programs (e.g. SAPM07DR, form ENTRY_WE01) calls the following function modules to work with the layout set:

- **OPEN_FORM:** to open the layout set and give the printer data
- **START_FORM:** to give the language
- **WRITE_FORM** (e.g. EXPORTING ELEMENT = 'W1LGMAT' to print a particular text element.
- **CONTROL_FORM:** e.g. to start a new page, a new window
- **CLOSE_FORM** to close the layout set and be able to open a new one. Two OPEN_FORMs without a CLOSE_FORM = update termination in RV_MESSAGES_UPDATE.

Because the text elements are called by the program (in call funtion WRITE_FORM exporting element ...) the customer may copy a layout set but still has to make sure that the text elements called by program still exist in his layout set.

When a text element is called, all fields to be printed should have already been read by the program. As we read all data (like MKPF, MSEG, EKPO) before calling text elements in standard, there is practically no risk that a field is empty when the text element is being processed. As we fill the complete work area for MSEG, EKPO etc. the customer may add any field from these tables in a text element.

Only text element for the window KOPF (header data) have a delayed process (not during WRITE_FORM but after the whole page has been processed. For that reason, it was necessary to store the work area in 'old' fields in the routines HELPDATA1 and HELPDATA2 in SAPM07DR. For the customer it means that you cannot add an MSEG field in the window KOPF because you will always get the data from the next item and not from the current item.

## 13. Collective slips

We have 3 collective slips: WE03, WA03, WLB3 (used for 541).
A collective slip contains all items of a material document.

It would be logical to have a collective slip at material document level but we do only output determination at item level. That is why we create a collective slip for item 0001.

Common customer errors:

**Collective slip printed for all items:**
A collective slip is printed for all items. Solution: customer has deleted the requirement 173 for WE03, WA03, WLB3 in the schema.

**Header of collective slips contains data from the last item:**
In a collective slip it is not possible to add an MSEG field to the header data because you will always have the data of the last item. You can only add an item field in the text element processing the item data.

## 14. Requirements

Requirements are small coding parts (usually IF condition with subsequent setting of a return code) that are used in addition to the condition table check. If a condition record is found for an output type but the requirement is not fulfilled, the output type will not be generated. Requirements are assigned to an output type in the schema table. They can be maintained with transaction VOFM (also accessible in Customizing). We use requirements for collective slips and mails.

For example, collective slips need requirement 173 so that they will be printed for the first line only. For that purpose the Form routine KOBED_173 (in SAPLV61B) is processed.

You can find the coding of a requirement either with SE80 for program SAPLV61B or with VOFM.

See also common error for collective slips.

**No print if empty requirement:**
If an output type has a requirement that is not used, it might happen that the output record will not generated. The technical reason is that the program SAPLV61B checks the return code (SY-SUBRC = 0) after the execution of a requirement. So if a return code was set (SY-SUBRC <> 0) before a requirement, the system considers that the requirement gives a return code and thus is not fulfilled. Solution: remove the requirement from the output type if the requirement is empty or always set SY-SUBRC = 0 in the requirement..

If you use your own requirements, do not forget to reset the return code (SY-SUBRC = 0) if the requirement is fulfilled.

## 15. Number of GR/GI slips (MSEG-WEANZ)

Used only for individual slips because it depends on material. Output types: WE01, WE02, WF01, WF02. The idea is to print a number of slips that corresponds to number of pallets delivered. If a material always has a fixed quantity by pallet, you can maintain it in the material master (quantity GR slips on storage location view). It will be proposed in MB01.
WEANZ is recalculated when the quantity is distributed among storage locations. Note 432947.

Set in program **SAPM07DR**, Forms **AUSGABE_WE01**, **AUSGABE_WE02** etc.(Include **M07DRAUS**).

MIGO: WEANZ is processed for all goods movements, but in the print programs it is only implemented in the GR slips WE01 and WE02.

**Which output types can use WEANZ to calculate the number of slips/labels?**
In the print program there are only two kind of output types working with field WEANZ:

- Individual GR slips WE01 and WE02: in form ENTRY_WE01 and ENTRY_WE02 we perform the slip printing as many times as indicated in WEANZ. The field is not active in a collective slip as it is an item information. If item 1 says 10 slips and item 2 says 4 slips, how many collective slips should we print? It does not make sense in a collective slip.
- Labels: field MSEG-WEANZ can be used for the determination of the number of copies (table T159E see below) for all goods movements in MIGO since note 333860. In MBxx transactions, it works only for goods receipts as WEANZ is not filled for goods issues.

## 16. Labels

There is an own customizing step for setting label printout. The idea is that you have a layout set with text element ETIKETT. The system determines the number of labels to be printed from the item quantity and prints the same quantity of text elements (called by WRITE_FORM). In each text element you have the same text. That is why the text for the label printout is not defined in the layout set but in a separate text.

Furthermore you can maintain a label type and a label form in the material master. Both fields are used in the condition tables for layout set.

- Application object (defined in table TTXOB, standard **MM07ET**): here you can find the text ID (table TTXID standard **7001),** text name and layout set (standard **RM07ETIKETT**). All data are maintained in table **T159O**. We recommend to keep the standard object, text id and text name.
- Label text: report **RM07METI** to maintain the label text (used for all text elements of the layout set)
- Label types and forms (used in condition tables for labels)
- **Number of copies**: here you can define which quantity field (e.g. MSEG-MENGE or MSEG-ERFMG) will be used to calculate the number of copies (table **T159E**). A customer wanted to use field VM07M-ANZGEB (number of containers in QM), but not possible because it is not an MSEG field and the program SAPM07DR can only work with DB fields. A modification was proposed in M07DRETI, form ETI_DRUCK (read ANZGEB from table QALS to set field ANZAHL1. Another customer wanted to use field MSEG-WEANZ but it does not work in case of GI (or reversal GIs) in MBxx transactions, see chapter WEANZ above. In MIGO it should work for all goods movements since note 333860 as we always calculate WEANZ.

**Labels and collective slips (output type WEE3):**
There is no such thing as a collective label. If you use print version 3 and you have created the condition records as recommended in Customizing, you will get one output record for WE03 for the first item and one WEE3 for each item. You will thus have one collective slip with all items and individual labels for each item. The content of WEE1, WEE2 and WEE3 is exactly the same, they all use form routine ETI_DRUCK. If you enter requirement 173 for WEE3 and hope to get one collective label, you will only get labels for the first item with data of the first item.

To transport label texts, use report RSTXR3TR.

**Form routines in** SAPM07DR:
**ETI_DRUCK** (include M07DRETI): here the number of copies is calculated from T159O and the text is read in perform **ETIKETTENTEXT_LESEN**. The text element ETIKETT is called (call function **'WRITE_FORM' exporting element** = **'ETIKETT')** in forms **WRITE_LINES** and **WRITE_LAST**..

## 17. Partner functions

We only use two partner functions for mails in MM-IM. We do not use any partner functions for slips. The partner functions are set by the program in **MM07MFN0, POSIDIALOGSTRUKTUR_FUELLEN**, call function **KOMPBME_FUELLEN**. We use the technical partner functions MP for MLGR and DP for MLFH.

Partner function is language-dependent!
In table **TPAUM** you can find the translation of the partner function in different languages.

## 18. Missing part mails

A lot of customers have problem customizing the missing part mails because they not only have to customize the output determination but also the missing part check itself.

For detailed information see IMG of missing parts and output determination

The missing part mail is a kind of availability check. We check during a goods receipt if the material is missing for some MRP requirements in the past. In MRP customizing you can define a time horizon to determine how far in the past the  system has to check the MRP requirements.

In **MENGE_PRUEFEN** > **FEHLTEILE_PRUEFEN** we call an MRP function module that gives us a list of non fulfilled MRP requirements for the material. If a material has non fulfilled requirements, we send a mail to the MRP controller

If the customer does not get the message 'Material is a missing part', the problem will not be a MM-IM but an MRP problem. Set a breakpoint in **FEHLTEILE_PRUEFEN (FM07MEF0),** function module **MISSING_PARTS_CHECK.**

If you do not get to the breakpoint, it means the missing part is not active (wrong customer setting)

- If you get to the breakpoint but the function module does not export an internal table  with the list of MRP requirements, it means either the material is not a missing part or wrong customer setting in MRP.
- If you get a list of MRP requirements from the function module but the customer does not get any mail or gets an empty mail, this is an MM-IM problem (usually wrong customizing of output determination).

If the customer gets a mail for a movement type for which he does not want any or if he has performance problems, it is possible to deactivate the missing part checks in table T156S (T156SC from 4.6 on) with the checking rule ** (note 102248).

Missing Parts mail: overview

![Missing Parts mail](/images/MMGR/2_Missing_Parts_Nail.jpg)

## 19. Mail text

You can find examples of mail texts in IMG.

Common errors:
**Mail variables not replaced in mail:**
In the old SAPSpript editor (SE71), the text variables (e.g. MSEG-MATNR) were defined directly in the text as &MSEG-MATNR&. In the new editor you cannot enter the text variable directly in the editor, youneed to add the text variables through the menue..Make sure the fields you add to the layout set are defined as a variable and not as text in the layout set. In transaction SE71 it should blink when you click on it.

See also notes 205951 and 213264 (bug in MB_MESSAGE_UPDATE)

**Text changes do not appear in mail:**
Before 4.6B all mail texts were defined at output type level.
Since 4.6B it is possible to define a mail text at output type level and at condition record level. When you create a condition, the mail text from the output type is internally copied to the output record. So if you change the mail text at output type level, you need to delete the condition record and recreate it in order to get the text changes to the condition record.

## 20. GR/GI slips for stock transfers

Usually the customer wants to have only one GI slip for stock transfers (on the supplying side only), but if he uses individual slips (WA01, WA02), he will get automatically two. In order to get only one, he has to make the slips dependent from the debit/credit indicator. This is explained in consulting note 86725.

In the customer uses a collective slip, he will get only the items for the supplying side, because we make a check in the program that the item has not been created automatically (MSEG-XAUTO in form LESEN_WAS).

## 21. Multiple accounting sheet

If the PO has multiple accounting, we print automatically an accounting sheet together with the individual slip. The accounting sheet is printed on a separate layout set MB_MFKTO and contains the detail of the accounting.

- Quantities on the accounting sheet: the quantity is the quantity planned in the purchase order because we cannot decide how to distribute the quantity ourselves. It has to be done by the GR clerk.
- Collective slip: not possible with multiple accounting, we switch automatically to individual slip (since note 15839)
- Layout set MB_MFKTO: customizable since 4.5B only. Customizing is possible via an entry in table T159N for program M07DRKTO.

## 22. Language

Has to be set when calling **START_FORM**, otherwise termination **RV_MESSAGE_UPDATE**.

Can be set dynamically by program SAPM07DR from T001W language. Can also be set by customer in condition record.

## 23. Printer determination

See IMG and check list 2 from note 426554.

## 24. Trouble-Shooting guide for non-developers

Bear in mind that the majority of printing problems are attributable to incorrect Customizing or to the use of user-defined developments. Problems which arise as a result cannot be dealt with by Support. If you require help with troubleshooting, you can call our Remote Consulting Service.

Before you enter an OSS message for SAP Support, make sure that there are no errors in Customizing or a user-defined development is not involved.

In OSS you will find two notes that will help you checking the output determination in your system:

- note 426554:  check lists for output determination and printer determination for goods movements
- note 426648: check list for missing part mails

## 25. Trouble-Shooting guide with debugging

Use this troubleshooting guide only if your are familiar with ABAP programming and debugging.
**Important checks:**

- **Do you use own condition tables?** Check 1) Customizing MM-IM >Output Determination > Output types: note access sequence. 2) Access sequence: check condition table. Standard tables range from 070 to 079. Customer range is 5xx. If a customer uses own condition tables and the system cannot find any output record or finds too many of them, this is a consulting problem.
- **Do you use own print program?** Check in Customizing MM-IM > Output Determination > Assign forms and programs (or SE16 table TNAPR). Routines like ENTRY_ZE01 or program ZM07MDR are not standard. It might explain that a NAST record will not be printed or has wrong data. **Important programs:**
- **SAPLV61B:** output determination program
- **SAPM07DR:** print program (reads all data per SELECT on database and calls SAPScript layout set.

**Important Form-Routines for breakpoints:**

- **MM07MFD0, DRUCK_PRUEFEN:**
  the version **(MKPF-WEVER)** is set depending on field **RM07M-XNAPR** and default version in T158. Field RM07M-XNAPR itself is set on first screen (dialog) or from parameter NDR (MB_CREATE).
- **MM07MFN0, KOPFDIALOGSTRUKTUR_FUELLEN and POSIDIALOGSTRUKTUR_FUELLEN**
  communication structures KOMKBME and KOMPBME are given to the output determination interface. There you can check the version (MKPF-WEVER), the print indicator (T156-KZDRU) and the transaction type (MKPF-VGART)
- **MB_CREATE_GOODS_MOVEMENT:**
  the user given in IMKPF-PR_UNAME or IMKPF-USNAM is used for reading parameter NDR in **DRUCK_PRUEFEN** and is used for the printer determination in LV61BF0P, **PRINTER_PARAMETER_LESEN**
- **LV61BF0N, NACHRICHTENSTATUS_SCHREIBEN**
  the output determination program loops over all output types and tries to find a condition record. If a condition record is found (**IF XKNUMH NE SPACE**), an entry is added to internal table XNAST.
- **LV61BF0P, PRINTER_PARAMETER_LESEN**
  The printer is found in tables TNAD7 and TNAD9 (**CASE INDEX. WHEN '7'. WHEN '9').**
- **MM07MFB9, BUCHEN_AUSFUEHREN, CALL FUNCTION RV_MESSAGES_GET**
  can be used to check the internal table XNAST just before posting (before commit work)
- **SAPM07DR, Form ENTRY_...**
  the data is read from the database (using SELECT *) by the print program. The print routine also calls the layout set and the text elements that have to be printed.
- **MM07MFT0** for mails only
  reads the data and fills mail text. Set parameter for mail processing (call transaction)

Symptom 3:Update termination RV_MESSAGE_UPDATE

​    Check the sapscript for **PERFORM xxxx IN PROGRAM yyyyy**.

​    If there is for some reason an error in the routine xxxx in program yyyyy the program will stop and

​    you also will get **Update termination RV_MESSAGE_UPDATE**

**Tips:**

- Even if an output record from NAST has been printed immediately in the update task, it is possible to repeat the output with MB90 and debug the print program in dialog.
- It is not possible to debug the print program SAPM07DR during the posting of a material document as it is processed in the update task. If an update termination RV_MESSAGE_UPDATE occurs during the creation of a material document, change the processing time 4 (immediately) to 3. That way the material document will be posted without update termination and you will be able to debug SAPM07DR in dialog using MB90.

List of symptoms:

| Symptom 1 | No output record created for material document               |
| --------- | ------------------------------------------------------------ |
| Symptom 2 | GR/GI slip is printed on wrong printer                       |
| Symptom 3 | Update termination in RV_MESSAGE_UPDATE                      |
| Symptom 4 | Wrong data is printed on GR/GI slip                          |
| Symptom 5 | No mail MLFH                                                 |
| Symptom 6 | Mail MLFH is sent without text or to wrong recipient or cannot be processed with CO06 |
| Symptom 7 | GR/GI slip is printed although field 'Print' is not set (note 205450) |

####  Symptom 1 : Nothing gets printed

![ Symptom 1](/images/MMGR/3_Symptom_1.jpg)

#### **Symptom 2: Wrong printer** 

![ Symptom 2](/images/MMGR/4_Symptom_2.jpg)

#### Symptom 3: Update termination RV_MESSAGE_UPDATE. 

Can happen if OPEN_FORM without CLOSE_FORM or if language is not set.

![ Symptom 3](/images/MMGR/5_Symptom_3.jpg)

#### Symptom 4: Wrong data printed on GR/GI slip

![ Symptom 4](/images/MMGR/6_Symptom_4.jpg)

#### Symptom 5: No mail MLFH

![ Symptom 5](/images/MMGR/7_Symptom_5.jpg)

#### Symptom 6: Mail MLFH is sent without text or to wrong partner or cannot be processed with CO06s

![ Symptom 6](/images/MMGR/8_Symptom_6.jpg)

#### Symptom 7: GR/GI slip is printed although field 'Print' is not set (note 205450)

Can happen if customer uses own condition tables without print version.

![ Symptom 7](/images/MMGR/9_Symptom_7.jpg)

## 26. Terminology

![ Symptom 6](/images/MMGR/10_Terminology.jpg)