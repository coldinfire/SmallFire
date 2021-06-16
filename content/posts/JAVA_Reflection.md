---
title: "JAVA 反射"
date: 2017-10-21
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - javabasic

---

### 反射

Java 反射机制 (Reflection) 在程序 **运行时**，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性。

这种动态的获取信息以及动态调用对象的方法的功能称为 **java 的反射机制**。

#### 反射机制的应用场景

- 在运行时判断任意一个对象所属的类
- 在运行时构造任意一个类的对象
- 在运行时判断任意一个类所具有的成员变量和方法(含构造方法)
- 在运行时调用任意一个对象的方法
- 生成动态代理

#### 反射机制实现

`java.lang.reflect` 包内包含实现 Java 反射机制的类。

- Class类：类对象
- Constructor类：类的构造器对象
- Method类：类的方法对象
- Field类：类的属性对象
- Array类：提供了动态创建数组，以及访问数组的元素的静态方法

#### 反射机制的缺点

- 执行效率低
  - 反射的操作主要通过 JVM 执行，所以时间成本会高于直接执行相同操作
  - 编译器难以对动态调用的代码提前做优化
- 容易破坏类结构
  - 反射操作绕过了源码，容易干扰类原有的内部逻辑

### 反射使用

反射机制的实现主要通过操作`java.lang.Class`类；该对象存放着对应类型对象的运行时信息。

使用步骤：

- 获取目标类型的 Class 对象

- 通过 Class 对象分别获取 Constructor 类对象、Method 类对象 和 Field 类对象

- 通过 Constructor 类对象、Method 类对象 和 Field 类对象分别获取类的构造函数、方法和属性的具体信息，并进行后续操作

- ```java
  // 获取类的 Class 对象实例
  Class clazz = Class.forName("类的全路径名");
  // 根据 Class 对象实例获取 Constructor 对象
  Constructor constructor = clazz.getConstructor();
  // 使用 Constructor 对象的 newInstance 方法获取反射类对象
  Object object = constructor.newInstance();
  // 获取方法的 Method 对象
  Method method = clazz.getMethod("method_name", int.class);
  // 使用 invoke 方法调用方法
  method.invoke(object, 4);
  ```

#### 获取反射中的 Class 对象

要获取一个类或调用一个类的方法，我们首先需要获取到该类的 Class 对象。

- 使用`Class.forName("类的全路径名")` 静态方法：根据类所在全路径名获取 Class 对象
- 使用`类对象.class` 方法：任何数据类型都有一个静态的 class 属性
- 使用`类对象 getClass()`方法：返回对象运行时类，需要先创建对象

#### 通过反射创建类对象

通过反射创建类对象主要有两种方式

- 通过 Class 对象的 newInstance() 方法
- 通过 Constructor 对象的 newInstance() 方法：Object newInstance(Object... initargs)

通过 Constructor 对象创建类对象可以选择特定构造方法，而通过 Class 对象则只能使用默认的无参数构造方法

#### 通过反射获取类属性、方法、构造器

Class 对象获取类属性

- Field getField(String fieldName)：获取单个 public 字段 
- Field getDeclaredField(String fieldName)：获取单个字段(包括私有的)
- Field[] getFields()：获取 Class 类的所有 public 访问权限的属性，以及其所继承的父类
- Field[] getDeclaredFields()：获取包括私有属性在内的所有属性
- Field 对象方法
  - void set(Object obj,Object value)：设置指定对象对应字段的值

Class 对象获取方法

- Method getMethod(String methodName,Class<?>... parameterTypes):获取单个 public 方法，Class 形参的类型对象
- Method getDeclaredMethod(String name,Class<?>... parameterTypes)：获取单个方法，Class 形参的类型对象
- getMethods()：获取所有 public 访问权限的方法，包括从父类继承的
- getDeclaredMethods()：获取所有本类的方法(不包括继承的)
- Method 类方法
  - Object invoke(Object obj,Object... args)：obj 要调用方法的对象；args 调用方式时所传递的实参

Class 对象获取构造器

- Constructor getConstructor(Class... parameterTypes)：获取单个 public 构造方法
- Constructor getDeclaredConstructor(Class... parameterTypes)：获取单个构造方法
- Constructor[] getConstructors()：获取所有 public 访问权限的构造方法
- Constructor[] getDeclaredConstructors()：获取所有的构造方法
- Construtor 对象方法
  - Object constructor.newInstance(Object... initargs)：创建类对象

