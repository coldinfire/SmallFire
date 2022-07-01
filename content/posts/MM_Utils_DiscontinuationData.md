---
title: " SAP Discontinuation Data设置 "
date: 2022-05-15
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

#### Use

You display and enter the data for discontinuation in a dialog box.

- *Discontinuation indicator*
- Discontinuation groups
- Follow-up groups

#### Features

Discontinuation Indicator for Discontinued Material

If this indicator is set, the material is defined as 'to be discontinued'. The material is then discontinued in MRP. MRP transfers dependent requirements that are no longer covered by stock of this material to the follow-up material.

You maintain this indicator in the MRP data of the material master record. In the BOM maintenance, the system displays the value in the *Material Master Record.* If the bill of material is assigned to several plants, you see the discontinuation indicator for the initial plant.

*Discontinuation groups*

This freely-definable character string groups together related discontinued items in a BOM.

- You can only enter a discontinuation group for an item if the discontinuation indicator is selected in the *MRP* data of the material master record. This discontinued item is replaced by a follow-up item.

A follow-up group is maintained for the follow-up item. The follow-up group for the follow-up item should have the same value as the discontinued item it is replacing (see [Examples of Discontinuation](http://saphelp.ucc.ovgu.de/NW750/EN/f5/05c453f57eb44ce10000000a174cb4/content.htm) ).

- You can implement a discontinuation group via a Parallel Discontinuation . When an item is discontinued (the main item to be discontinued), the other items in the discontinuation group (subordinate items to be discontinued) are automatically replaced at the same time.

If you want to implement parallel discontinuation, you must define the following settings:

- *Material Master Record* (View *MRP 4* ):

You use the *Discontinuation indicator* to control discontinuation. If the material is the main material to be discontinued, enter the value *1.* If the material is a subordinate material to be discontinued, enter the value *3.*

- Bill of material:

You enter the same discontinuation group for the main item to be discontinued and the subordinate item to be discontinued (see [Example of Discontinuation](http://saphelp.ucc.ovgu.de/NW750/EN/f5/05c453f57eb44ce10000000a174cb4/content.htm) ).

*Follow-Up Groups*

This freely-definable character string:

- Groups together related follow-up items in a BOM
- Determines which discontinued items this follow-up item replaces

The follow-up item replaces the discontinued item that has this same value in the *Discontinuation group* (see [Example of Discontinuation](http://saphelp.ucc.ovgu.de/NW750/EN/f5/05c453f57eb44ce10000000a174cb4/content.htm) ).

Entering a Follow-Up Group:

- You can only enter a follow-up group if the item is kept in stock (for example, a stock item).
- You can only maintain an item as a follow-up item when you first enter the item. Once you have saved the item, you can no longer change the value in this field.

Changing a Follow-Up Group:

If you change the item data of a follow-up item (for example, the item quantity), the system creates a new item record.