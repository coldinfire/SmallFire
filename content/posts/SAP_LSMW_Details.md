---
title: "LSMW操作步骤详解"
date: 2020-09-03
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - utils
  - SAPbusiness
---

### Step1:数据准备

数据准备一般使用 Excel 整理数据，并转换成标准文本。我们以创建物料主数据（事物代码 MM01）的基本视图为例。

物料主数据的基本视图我们需要输入以下字段的数据：

- 物料编码
- 物料名称
- 计量单位
- 物料组

| **物料编码** | **物料名称**      | **计量单位** | **物料组** |
| ------------ | ----------------- | ------------ | ---------- |
| T3-AT-050    | A 型 T 三通 50mm  | PC           | 0501       |
| T3-AT-250    | A 型 T 三通 250mm | PC           | 0501       |
| T3-AT-300    | A 型 T 三通 300mm | PC           | 0501       |
| T3-AT-350    | A 型 T 三通 350mm | PC           | 0501       |
| T3-AT-400    | A 型 T 三通 400mm | PC           | 0501       |
| T4-AT-025    | A 型 T 四通 25mm  | PC           | 0502       |
| T4-AT-040    | A 型 T 四通 40mm  | PC           | 0502       |
| T4-AT-060    | A 型 T 四通 60mm  | PC           | 0502       |

对于每种物料，它的物料编码、物料名称、物料组是不尽相同的，这需要设为变量；而计量单位全部为 PC，可以设为常量。我们将变量的数据放入 Excel 表中。

![Step1](/images/LSMW/LSMW6.png)

第一行是字段的名称，不可使用汉字，只能使用英文字母，其中 物料编码：MCODE、物料名称：MNAME、物料组：MATKL、计量单位是常量，不需要放入表中。MATKL的列的单元格式设为文本字段，不要选择常规或数字，否则 “0501” 就会变成 “501”。

将整理好数据的 Excel 表另存为 txt 文件，**用制表符分隔** ，在弹出对话框中点击 “确定” 保存文件。

![Step1](/images/LSMW/LSMW7.png)

用文本方式打开已保存好的文件（“物料数据.txt”），显示出数据已准备好，各字段之间是以 Tab 键分隔的 ( **记住 Tab 键这点很重要，以后需要对应设置** )。

![Step1](/images/LSMW/LSMW8.png)

### Step2:创建批处理对象

一个批处理对象是个树形结构，包括三层：Project、Subproject、Object，我们分称为项目、子项目、对象。本步骤就是创建一个批处理对象 (Object)。如果此对象 (Object) 已存在，可以不用创建，用输入或选择的方法找到指定的对象。

使用Tcode:LSMW创建批处理对象：输入需要创建的项目对象名称。批处理对象命名分为三层，分别为项目名 (Project)、子项目名 (Subproject)、对象名 (Object)。点击创建，输入相应的短文本描述，点击确认按钮。

![Step2](/images/LSMW/LSMW9.png)

创建完成的Project。

![Step2](/images/LSMW/LSMW10.png)

点击 “Project Documentation” 按钮显示对象命名信息。一个批处理对象的三层结构：项目、子项目、对象。当然在一个项目 (Project) 下，可以创建多个子项目 (Subproject)，一个子项目 (Subproject) 也可以创建多个对象 (Object)。点击返回按钮返回管理界面。

![Step2](/images/LSMW/LSMW11.png)

### Step3:屏幕录像并定义变量

此步进入屏幕录像阶段，这是技术性、经验性很强的步骤，在录屏过程中做到尽量少的屏幕转换；输入项不要弹出查询窗口；对于滚动列表应该使用 PageUP、PageDown 键翻页，而不能用鼠标点击滚动行。

此步骤除了录屏外，还需要对录屏里的字段进行整理，并将某些字段由常量变换为变量。

#### Recording操作

在管理界面点击菜单 `goto → recordings` 进入屏幕录像界面。

![Step3](/images/LSMW/LSMW12.png)

进入了录像界面，点击新建按钮，在弹出的对话框中，需输入录像名称 (Recording) 及描述 (Description)。

![Step3](/images/LSMW/LSMW13.png)

点击确认按钮，进入下一屏，弹出对话框中输入需要录像的Tcode：MM01。点击确认按钮进入事物代码的操作录像。

![Step3](/images/LSMW/LSMW14.png)

录像的数据是准备数据中的第 1 个（当然可以是任意一个），物料编号为 “T3-AT-050”，物料名称为 “A 型 T 三通 50mm”，计量单位为 PC，物料组为 “0501”。

选择行业领域（M 机械工程）、物料类型 (FERT 成品) 等相应的值，按回车键会让选择物料组，输入正确的物料组。

![Step3](/images/LSMW/LSMW15.png)

选择Select view(s)进入选择组织级别视图。选择 “基本视图 1”、“基本视图 1” 两个选择项，按回车键进入下一屏幕。如果需要选择的视图靠后而不能显示（例如会计视图），请用 PageDown 键翻页，而不能使用鼠标点击滚动行，这是因为录屏后可存入 PageUp、PageDown 等键信息，但不能存入鼠标点击滚动行的动作。

![Step3](/images/LSMW/LSMW16.png)

维护完视图数据后，点击保存即可。

![Step3](/images/LSMW/LSMW17.png)

录像后返回 LSMW 的操作界面。在此开始整理录像的字段，并将某些字段由常量变更为变量。

操作界面是一个树形结构，它表示刚才录像的数据，录像后的信息分为三层：事物代码 (本例为”MM01 创建物料”)、录像屏幕 (如 SAPLMGMM 0060)、字段信息（如”RMMG1-MBRSH”）。

![Step3](/images/LSMW/LSMW18.png)

我们将树收缩后详细查看，就会看到录像中的每一个屏幕在这里都对应了一段数据。

- SAPLMGMM 0060对应录像时的初始屏幕。里面BDC_OKCODE对应"/00"代表回车键，其他数据都是输入数据。

  ![Step3](/images/LSMW/LSMW19.png)

- SAPLZMNNR 0010对应自定义的增强输入物料组信息，其中WA_MAT-MATKL代表了输入的物料组。

  ![Step3](/images/LSMW/LSMW20.png)

- SAPLMGMM 0070对应组织级别对话框，值中的两个 “X” 代表选择了第一个和第二个选择项。

  ![Step3](/images/LSMW/LSMW21.png)

- SAPLMGMM 4004对应基本数据1界面，可以看到输入的物料描述，单位，物料组等。

  ![Step3](/images/LSMW/LSMW22.png)

- SAPLSP01 0300对应的是最后弹出的保存确认框，上面 “=YES” 代表按了确认按钮。

  ![Step3](/images/LSMW/LSMW23.png)

#### 定义变量操作

本例中的变量有物料描述，物料组。计量单位是默认值 PC，不需要定义为变量。

用鼠标选中物料描述处，点击 “Default” 按钮定义为变量。显示定义了一个变量，变量名称为 “MAKTX”，描述为 “Material Description”。基本计量单位是常量值 PC，可以不用设为变量

![Step3](/images/LSMW/LSMW24.png)

如需要改变，双击此行，弹出对话框可以修改变量的名称、描述 及默认值。

![Step3](/images/LSMW/LSMW25.png)

在第二个 4004 屏幕（基本视图 2）中又有一个 MAKTX，需要选中后按字段删除按钮。如果不删除，那么它就成为常量值，所有物料创建到基本数据 2 的屏幕时，它的物料描述就都会保存不变，这就会造成错误。

![Step3](/images/LSMW/LSMW27.png)

SAPLMGMM 4004（第二个）屏幕删除字段后的界面。

![Step3](/images/LSMW/LSMW26.png)

以上变量设定完成后按保存按钮退出，返回到管理界面。

### Step4:定义对象属性

在以下步骤中，我们将从管理界面点击执行进入到分步操作界面。在管理界面点击执行按钮，进入分步操作界面。

![Step4](/images/LSMW/LSMW28.png)

这一步骤是定义对象的属性，主要是将对象指明录像的名称。用鼠标双击 `Maintain Object Attributes` 进入到定义对象属性界面。

如果进入的界面是显示状态，请点击 `Display <-> Change` 按钮，进入编辑状态。

![Step4](/images/LSMW/LSMW29.png)

 这个界面只需要按图所示选中 “Batch Input Recording” 项，并选择录像名就可以了。由于我们只有一个录像，一按弹出键就会显示；如果我们有多个录像，则弹出一个对话框让我们选择。

按保存按钮后按返回按钮返回分步操作界面。这时分步操作的界面的右部显示了一行，表示最后操作的日期、时间和操作者。

### Step5:定义源表结构名称

此步骤定义源表的结构名称，在分步操作界面用鼠标双击 `Maintain Source Structures` 进入操作界面。

输入源表定义名称及描述，按确认按钮退出。

![Step5](/images/LSMW/LSMW30.png)

按保存按钮保存后并退出分步操作界面。

### Step6:定义源表字段结构

此步骤是在 LSMW 对象中定义源表的数据结构，也就是定义第 1 个步骤数据准备时的字段信息。

在分步操作界面，用鼠标双击 `Maintain Source Fields`，进入定义源表字段结构界面。

选中源数据结构名称（”MANTR_BASIS”）点击维护按钮进入字段编辑界面。

![Step6](/images/LSMW/LSMW31.png)

共有 4 列，需要分别填写：

- 字段名 (Field Name)：输入源表中的字段名，详见 excel 表中的表头
- 类型 (Type)：数据类型，C 为字符型
- 长度 (Length)：字段长度，可尽量大一些
- 描述 (Field Description)：字段描述，可选项

![Step6](/images/LSMW/LSMW32.png)

填写的内容就是步骤 1 数据准备时的表字段，上部为 Excel 表信息，下部为本步骤中填写的字段信息。Excel 表中有什么字段，那么设计的源表就按顺序填写什么字段，并给出字段类型 (一般为字符型 C) 和字段长度（相对大一些为好）。

以上填写清楚按保存按钮保存并按返回按钮返回本步骤开始界面，界面上显示已创建的字段信息。

![Step6](/images/LSMW/LSMW33.png)

再按返回按钮退回到分步操作界面。

### Step7:源表结构和录像关联

本步骤定义源表结构与录像之间的关系。在分步操作界面双击`Maintain Structure Relations`条目进入操作界面。

![Step7](/images/LSMW/LSMW34.png)

由于只有一个录像与一个源表结构，系统自动对应，左边是屏幕录像设定的名称（步骤 3），右边是源表设计时的名称（步骤 5、步骤 6）。如有多个需选择对应。

按返回按钮返回分步操作界面。

### Step8:源表字段与录像字段关联

本步骤需要将源表的字段结构与录像中定义的变量相关联，称为字段映射（Filed Mapping），并可以设定转换规则（Conversion Rules）。

在分步操作界面用鼠标双击 `Maintain Field Mapping and Conversion Rules`，进入源表及录像字段关联操作界面。

![Step8](/images/LSMW/LSMW35.png)

在录像 MMBASIC 中定义的 3 个变量，如不记得请查看步骤 3。选中 “MAKTX” 字段，点击 “Source Field” 按钮，弹出源表字段列表对话框。

![Step8](/images/LSMW/LSMW36.png)

在所示的源表字段列表对话框中，选中录像中 “MAKTX” 字段对应的源表字段 “MNAME“，按确认按钮退出。此时屏幕弹出对话框，提示源表字段比目标表字段长，可不必管理，继续按确认按钮退出。

![Step8](/images/LSMW/LSMW37.png)

依次类推，将物料组分别对应，全部完成后点击保存按钮，再按返回按钮返回分步操作界面。

### Step9:固定值，转换条件，用户定义

在此步骤中可以设定录像中字段的值来源，除对应源表字段外，还可以设定为固定值、转换条件、或是更为复杂的用户定义（用 ABAP 编程）。一般使用可跳过此节。

如需进入请在分步操作界面用鼠标双击 `Maintain Fixed Values, Translations, User-Defined Routines`。

![Step9](/images/LSMW/LSMW38.png)

### Step10:指定源表文件

以上步骤已完成数据准备和模板定义，以下将进入数据导入阶段。本步骤指定源表的文件，也就是在步骤 1 中生成的文本文件（“物料数据.txt”）。在分步操作界面用鼠标双击`Specify Files` 进入操作界面。

![Step10](/images/LSMW/LSMW39.png)

在本步骤中要指定三个值，其中一个需要手工指定，两个自动生成。手工指定的 “Legacy Data”，自动生成的是 “Imported Data”、“Converted Data”，这两个数据文件都在本机上。

- “Imported Data” 设定了导入的数据文件名
- “Converted Data” 设定了转换的数据文件名

用鼠标先指定 “Legacy Data” 行，再用鼠标点击创建按钮，屏幕弹出设定源表文件的对话框，需要输入源表文件名，及源表文件的属性设置。

![Step10](/images/LSMW/LSMW40.png)

- “File” 项输入源表的文件名（步骤 1 中的 “物料数据.txt” 文件）
- “Name“ 项输入说明，可为任意值，但不可为空值
- “Delimiter” 指定文件的分隔符，我们的文件的分隔符是 Tab 键，所以选中 Tabulator
- “Field Name At Start Of File” 项指定第一行是否有字段名，我们的 txt 文件的第一行是字段名，所以需要选中
- “Field Order Matches Source Structure Definition” 项指定字段顺序是否与源表数据相同，我们进行选中处理

全部填写和选择完成后，按确认按钮退回到操作界面。

![Step10](/images/LSMW/LSMW41.png)

### Step11:指定文件

本步骤是指定源表数据结构和对应的数据文件（.txt）。在分步操作界面用鼠标双击 `Assign Files`，进入操作界面。

![Step11](/images/LSMW/LSMW42.png)

由于批导入对象只定义了一个源表数据结构，并在上一步骤定义了一个数据文件 (物料数据.txt)，所以系统自动进行了对应处理，

在图上点击黄色的 “MANTR_BASIS”，再点击 “Assignment” 按钮，弹出对话框告知文件已指定了源表结构，此步骤可以不用操作。

按返回按钮返回分步操作界面。

### Step12:导入数据

此步骤是将源表数据读取进本机的系统文件，也就是步骤 10 指定源表文件中的 “Imported Data” 指定的文件。在分步操作界面用鼠标双击 `Import Data`进入操作界面。

![Step12](/images/LSMW/LSMW43.png)

第一行填写要读取的起止行数，如不填则全读取，按执行按钮执行。系统弹出一个安全对话框，询问是否授权访问数据文件，点击 “允许” 按钮继续。

![Step12](/images/LSMW/LSMW44.png)

执行完毕后会显示执行结果，表示正确导入了 8 行数据。再按返回按钮返回到分步操作界面。

![Step12](/images/LSMW/LSMW45.png)

### Step13:显示导入的数据

本步骤就是显示上一步骤导入的数据。在分步操作界面用鼠标双击 `Display Imported Data`，弹出对话框。

![Step13](/images/LSMW/LSMW46.png)

“From Line” 项和 “To Line” 要求填写显示的开始行数和结束行数，如不填写则显示全部。按确认按钮进入显示数据界面。

![Step13](/images/LSMW/LSMW47.png)

显示了上一步骤导入数据，共有 8 行。用鼠标双击任意一行，比如第 1 行，显示详细信息。

![Step13](/images/LSMW/LSMW48.png)

 显示了一行数据的详细信息，包括字段名（“Field Name”）、字段描述（“Field Text”）、字段值（“Field Value”）。字段是源表中的字段，而不是录像中的字段。

连续按返回按钮返回分步操作界面。

### Step14:转换数据

本步骤是将读进系统文件的数据进行转换，存放在步骤 10 指定源表文件 “Converted Data” 指定的转换文件中。本步骤和下一步骤显示可以查看转换是否正确，如不正确可返回到以前步骤进行操作。本步骤操作的数据不会在 SAP 系统中真正执行。

在分步操作界面用鼠标双击 `Convert Data`，进入操作界面。

![Step14](/images/LSMW/LSMW49.png)

要求输入转换的开始和结束行数，如不填写则全部转换。按执行按钮，执行完毕屏幕显示转换结果。

![Step14](/images/LSMW/LSMW50.png)

### Step15:显示转换数据

本步骤就是显示上一步的以预转换结果。在分步操作界面用鼠标双击 `Display Converted Data`，弹出对话框。

填入显示的开始行和结束行，如不填则全部显示。

![Step15](/images/LSMW/LSMW51.png)

按确认按钮进入显示转换数据界面。

![Step15](/images/LSMW/LSMW52.png)

用鼠标双击任意一行，比如第 1 行，显示详细信息。

![Step15](/images/LSMW/LSMW53.png)

显示了一行转换过来数据的详细信息，包括字段名（“Fld Name”）、字段描述（“Fld Text”）、字段值（“FldValue”）。

字段名中头两行条目分别是录像的名称（“MMBASIS”）和录像的事务代码（“MM01”），后续的行是录像中定义的变量 (“NAKTX”、“NATKL”)。

### Step16:创建转换任务

此步骤开始实际转换。本步骤是创建一个转换任务但不实际转换，并将转换的数据存放到 SAP 服务器端。

在分步操作界面用鼠标双击 `Create Batch Input Session`，进入操作界面。

![Step16](/images/LSMW/LSMW54.png)

在 Keep Batch Input Folder (s) 项打上勾。按执行按钮，运行后显示对话框。

![Step16](/images/LSMW/LSMW55.png)

创建成功，再按确认按钮返回到分步操作界面。

### Step17:执行转换任务

本步骤进行实际的转换。除在 LSMW 集成环境操作外，此步骤也可在前台运行 `SM35` 进入。

在分步操作界面用鼠标双击 `Run Batch Input Session`，进入操作界面。

![Step17](/images/LSMW/LSMW56.png)

显示已创建的转换任务，尚未执行。用鼠标选中此任务，并按处理按钮，弹出执行选择对话框。

![Step17](/images/LSMW/LSMW57.png)

对话框中，运行模式 Processing Mode 有三个可选项

- 处理／前台：每个事物代码运行在前台，可一步一步运行，可在运行时修改，可看其效果，并可以修改，也可中途退出，但速度慢，一般用于测试。
- 仅显示错误：后台运行，错误时显示到前台。
- 不可见：后台运行，错误时也不报出，在全部运行完后可通过查看转换结果看到错误。

本次操作选择 “不可见”，再选中专家方式。按 “处理” 按钮执行。

由于此次转换任务是在 SAP 服务器后台运行，在运行期间 SAP GUI 可退出。

### Step18:查看执行结果

等任务执行完毕我们可以查看批处理的结果。也可以在执行过程中查看，当然数据是不完整的，但可以看到已执行部分的情况。

和上一步一样，在分步操作界面用鼠标双击`Run Batch Input Session` 再次进入转换界面，显示任务状态。

![Step18](/images/LSMW/LSMW58.png)

用鼠标选中此任务，双击条目或选中条目后点击 “分析” 按钮显示转换结果。

![Step18](/images/LSMW/LSMW59.png)

双击左屏索引号为 1 的行，显示详细情况。

![Step18](/images/LSMW/LSMW60.png)

显示此行数据操作的各个屏幕的编号，6 个编号代表 6 个屏幕，与步骤 3 屏幕录像后的数据相同。我们用鼠标双击屏幕号为 “0060” 的条目，显示此屏幕的详细信息。

![Step18](/images/LSMW/LSMW61.png)

按返回按钮返回到初始界面后，点击日志创建标签页面。

![Step18](/images/LSMW/LSMW62.png)

界面中显示了任务执行过程中的全部信息，目前的转换号 (“Transaction”) 是 1，见左上角。由于信息量较大不易区分，我们选中 `Transaction` 项，只显示一行数据的转换信息。

![Step18](/images/LSMW/LSMW63.png)

- 消息的第一行显示：The material group 0501 does not exist.
- 原来是物料组0501不存在，换用存在的物料组即可 

如果有多行数据错误，按此方法查找原因并总结经验。一个数据批处理任务完成。

连续按返回按钮返回主界面。