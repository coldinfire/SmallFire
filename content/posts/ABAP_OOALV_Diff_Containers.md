---
title: " OO ALV不同Container "
date: 2018-07-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

### 1.Custom container

Custom container can be created with a class *CL_GUI_CUSTOM_CONTAINER*, but it needs a parent container in which it could be placed or it needs an custom control area to be created in custom screen . This solution is mainly used by me in the applications I develop now. But it’s also common that you’ll mix custom containers with other in one development.

Many persons creates the custom container during the *PBO* event of the screen that contains custom container area, but you don’t have to do that. You can create it also before calling the screen, like in the simple example of using *CL_GUI_CUSTOM_CONTAINER* that you can find in Figure 1 and in demo program *ZDEMO_AIN_CL01*.

Don’t try to find anything in this program, that was not needed just to quickly display the data on the grid. There is no *GUI Status*, there is no *PBO* and *PAI* handling. I did it on purpose just to create really simple example. (As you may read it the abapGit version can differ from here)



### 2.Splitter container

Splitter (*CL_GUI_SPLITTER_CONTAINER*) needs a custom container as a parent in order to work. It is used to divide the screen area into several containers. To simplify, you decide how many rows and columns the splitter will have. So it’s like a table or even *DIV* in *HTML* where you can put your content. You can create multilevel splitters, so if you want you can split the area into two rows with one column, then create a splitter on the top row which will be divided into two rows and three columns. Your imagination, screen size and user experience will prompt you the best layout here.

The simple example found in program *ZDEMO_AIN_CL02* will create a splitter with two rows and one column. The *SCREEN* 0100 found here is exactly the same as in *ZDEMO_AIN_CL01*. 

```ABAP
DATA:G_SPLITTER TYPE REF TO CL_GUI_SPLITTER_CONTAINER,
     G_CONTAINER_2000L TYPE REF TO CL_GUI_CONTAINER,
     G_CONTAINER_2000R TYPE REF TO CL_GUI_CONTAINER.
IF g_splitter IS INITIAL.
    CREATE OBJECT g_splitter  "定义一个屏幕包含两个ALV"
      EXPORTING
        parent  = cl_gui_container=>screen0
        rows    = 2
        columns = 1.
    CALL METHOD g_splitter->set_border
      EXPORTING
        border = cl_gui_cfw=>false.
    g_container_2000l = g_splitter->get_container( row = 1 column = 1 ).
    g_container_2000r = g_splitter->get_container( row = 2 column = 1 ).
    g_splitter->set_row_height( id = 1 height = 20 ).
    g_splitter->set_row_height( id = 2 height = 80 ).
ENDIF.
```

### 3.Docking container

Docking container (*CL_GUI_DOCKING_CONTAINER*) doesn’t need any parent, neither the custom container area on custom screen. When created and displayed, it’s docked in one of four positions of the screen: top, bottom, left, right. In most cases docking container is used to display some navigation menus but as it’s possible to use it as grid parent, then I’ve used it also several times to display some limited number of columns in it.

### 4.Dialgobox container

Dialogbox container (*CL_GUI_DIALOGBOX_CONTAINER*) can be useful if you need to display a popup window with your grid and you don’t want to spend time on creating the screen with custom control on it. It’s very handy to use in that situation, but also it has a limitation that you don’t have GUI toolbar available in here. To display the container I used the same approach as for docking container. Don’t be surprised that after running this program, you’ll not be able to close the dialog container, it’s normal as I didn’t register any event for it.

 