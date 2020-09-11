---

title: "SAP 异常处理"
date: 2019-07-09
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### SAP 异常处理

**异常**是在程序执行期间出现的问题。 当异常发生时，程序的正常流程中断，程序应用程序异常终止，这是不推荐的，因此这些异常将被处理。

#### **异常处理方式：**

**RAISE** - 提出异常，表示发生了一些异常情况。 通常，异常处理程序会尝试修复错误或找到替代解决方案。

**TRY** - TRY 块包含要处理其异常的应用程序编码。 此语句块按顺序处理。 它可以包含进一步的控制结构和程序调用或其他 ABAP 程序。 它后面是一个或多个 catch 块。

**CATCH** - 程序在要处理问题的程序中的地方使用异常处理程序捕获异常。 CATCH 关键字表示捕获异常。

**CLEANUP** - 每当在 TRY 块中发生异常，而 TRY 块未被同一 TRY - ENDTRY 结构的处理程序捕获时，将执行 CLEANUP 块的语句。 在 CLEANUP 子句中，系统可以将对象恢复到一致状态或释放外部资源。 也就是说，可以对 TRY 块的上下文执行清除工作。

#### 抛出异常

可以在方法中的任何点，函数模型，子例程抛出异常信息。

引发异常原因：

- ABAP运行时系统引发的异常
- 程序中写的异常代码：同是创建异常对象和抛出异常 `RAISE EXCEPTION exep`

#### 捕捉异常

假设块将引发异常，则方法使用 TRY 和 CATCH 关键字的组合捕获异常。 TRY - CATCH 块放置在可能生成异常的代码周围。 以下是使用 TRY - CATCH 的语法:cx_root可以捕获一切异常。

```JS
DATA lv TYPE i.
DATA lv_message TYPE string.
DATA lr_exc TYPE REF TO cx_root.
TRY .
  lv = 100 / 0.
CATCH cx_sy_arithmetic_error into lr_exc.
  lv_message = lr_exc->get_text( ).
  WRITE:/ lv_message.
CATCH cx_root INTO lr_exc.
  lv_message = lr_exc->get_text( ).
  WRITE:/ lv_message.
ENDTRY.
```

#### 异常的属性和方法

| 编号 | 属性和说明                                                   |
| ---- | ------------------------------------------------------------ |
| 1    | **Textid**：用于定义异常的不同文本，并且也影响方法 get_text 的结果 |
| 2    | **Previous**：此属性可以存储原始异常，允许您构建异常链       |
| 3    | **get_text**：这将根据异常的系统语言将文本表示作为字符串返回 |
| 4    | **get_longtext**：这会将异常的文本表示的长变体作为字符串返回 |
| 5    | **get_source_position**：给出引发异常的程序名和行号          |







