---
title: "JAVA IO"
date: 2017-10-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - IO
---



### JAVA IO 流

`Java.io` 包几乎包含了所有操作输入、输出需要的类。所有这些流类代表了输入源和输出目标。

流是一组有顺序的，有起点和终点的字节集合，是对数据传输的总称或抽象。即数据在设备间传输称之为流。

流的本质是数据传输，根据数据传输的特性将流区分为各种类，方便更直观的进行数据操作。

#### 流类图结构

![流对象](/images/JAVA/JAVA_IO_Stream.png)

#### IO 流分类

根据数据处理的不同类型分为：字节流和字符流

字节流和字符流的区别：

- 读写单位的不同：字节流以字节（8bit）为单位。字符流以字符为单位，根据码表映射字符，一次可能读多个字节。

- 处理对象不同：字节流可以处理任何类型的数据，如图片、avi等，而字符流只能处理字符类型的数据。

根据数据流向不同分为：输入流和输出流

- 输入流表示从一个源读取数据
- 输出流表示向一个目标写数据
- 程序中需要对于传输数据的不同特性而使用不用的流

使用输入流步骤

- 设定输入流的源
- 创建指向源的输入流
- 使用输入流读取源中的数据
- 关闭输入流

使用输出流步骤

- 设定输出流目的地
- 创建指定目的地的输出流
- 使用输出流把数据写入到目的地
- 关闭输出流

### 文件类 File

File 类是对文件系统中文件以及文件夹进行封装的对象，可以通过对象的思想来操作文件和文件夹。

File 类保存文件或目录的各种数据信息，包括文件名、文件长度、最后修改时间、是否可读、获取当前文件的路径名、判断文件是否存在、获取当前目录中的文件列表、创建、删除文件和目录等方法。

File类常用方法：

File.separator：separator 方法会自动获取不同平台的分隔符

| Method                                                       |
| :----------------------------------------------------------- |
| File(String pathname)：通过将给定路径名字符串转换为抽象路径名来创建一个新 File 实例 |
| File(URI uri) ：通过将给定的 file: URI 转换为一个抽象路径名来创建一个新的 File 实例 |
| File(String parent, String child)：根据 parent 路径名字符串和 child 路径名字符串创建一个新 File 实例 |
| boolean createNewFile() ：当且仅当不存在具有此抽象路径名指定名称的文件时，不可分地创建一个新的空文件 |
| boolean mkdir()：创建此抽象路径名指定的目录                  |
| boolean mkdirs()：创建此抽象路径名指定的目录，包括所有必需但不存在的父目录 |
| boolean delete()：删除此抽象路径名表示的文件或目录           |
| void deleteOnExit()：在虚拟机终止时，请求删除此抽象路径名表示的文件或目录 |
|                                                              |
|                                                              |
|                                                              |
|                                                              |
|                                                              |



### 字节流

字节流的输入与输出对应图

![字节流](/images/JAVA/JAVA_IO_Byte.png)

#### 输入字节流 InputStream

InputStream 是所有输入字节流的父类，它是一个抽象类。

FileInputStream 类常用的方法：

| Method                                | Description                                                  |
| :------------------------------------ | :----------------------------------------------------------- |
| FileInputStream(File file);           | 通过传入的File对象创建对象                                   |
| FileInputStream(String.name);         | 通过传入的文件系统路径名创建对象                             |
| void close();                         | 关闭流，并且释放该流占用的所有系统资源                       |
| long skip();                          | 从源中读取单个字节的数据，该方法返回字节值(0-255)，未读取出字节返回-1 |
| int read(byte b[]);                   | 从输入流中读取一定数量的字节，并将其存储在缓冲区数组 b中，返回实际读取的字节数目。到达尾部返回 -1 |
| int read(byte[] b, int off, int len); | 从 off 指定位置将输入流中最多 len 个字节读入 byte 数组。返回实际读取的字节数目。到达尾部返回-1 |

#### 输出字节流 OutputStream

OutputStream 是所有输出字节流的父类，它是一个抽象类。

FileOutputStream 类常用方法：

| Method                                        | Description                                                  |
| :-------------------------------------------- | :----------------------------------------------------------- |
| FileOutputStream(File file);                  | 创建一个向指定File对象表示的文件中写入数据的文件输出流       |
| FileOutputStream(File file, boolean append);  | 创建一个向指定File对象表示的文件中写入数据的文件输出流，append 设置是否使用追加模式添加数据 |
| FileOutputStream(String name);                | 创建一个向具有指定名称的文件中写入数据的输出文件流           |
| FileOutputStream(String name,boolean append); | 创建一个向具有指定名称的文件中写入数据的输出文件流，append 设置是否使用追加模式添加数据 |
| void write(int n);                            | 输出流调用该方法向目的地写入单个字节                         |
| void write(byte b[]);                         | 输出流调用该方法向目的地写入一个字节数组                     |
| void write(byte b[],int off,int len);         | 给定字节数组中起始于偏移量off处取len个字节写到目的地         |
| void close();                                 | 关闭输出流；并且释放该流占用的所有系统资源                   |



### 字符流

字符流的输入与输出对应图

![字符流](/images/JAVA/JAVA_IO_Char.png)

#### 字符输入流Reader

Reader 是所有的输入字符流的父类，它是一个抽象类。

Reader的主要方法：

- int read()：从流中读取单个字符。

- abstract void close()：关闭流，并且释放该流占用的所有系统资源。

- int read(char[] cbuf)：从当前位置读取 cbuf.length 个字符到 cbuf 数组中，但实际读取的字符个数取决于流中剩余字符个数。返回值为实际读取的字符个数。

- abstract int read(char[] cbuf, int off, int len)：从当前位置读取 len 个字符到 cbuf 中，第一个字符存放在cbuf[off] 中，第二个字符存放在 cbuf[off+1] 中，依次类推。实际读取的字符的个数取决于流中剩余的字符个数，返回值为实际读取的字符个数。

#### 字符输出流Writer

Writer 是所有输出字符流的父类，它是一个抽象类。

Writer的主要方法：

- Writer append(char c)：追加字符c到此Writer流中，追加到尾部。返回值为追加后的此Writer。

- Writer append(CharSequence csq)：追加字符序列 csq 到此 Writer 流中，追加到尾部，返回值为追加后的此 Writer。

- Writer append(CharSequence csq,int start,int end)： 
  追加字符序列 csq 的子序列到 Writer 流中,子序列为序列 csq 的 start 到 end 部分，追加到尾部，返回值为追加后的此序列。

- abstract void close()：关闭流，在关闭之前先调用flush()。

- void write(String str)：将字符串写入到流中。

- void Write(String str, int off, int len)： 给定字符串中起始于偏移量 off 处取 len 个字节写到目的地。

#### 字符缓冲流

缓冲流先将数据缓存起来，然后一起写入或读取出来。缓冲流是对文件流处理的一种流，增强了读写文件的能力。够更高效的读写信息。

flush()：从缓冲区把文件写出

close()：是将文件从缓冲区内写出并且关闭相应的流

BufferedReader 字符输入缓冲流

- BufferedReader(Reader in)： 创建一个使用默认大小输入缓冲区的缓冲字符输入流。

- BufferedReader(Reader in, int size)： 创建一个使用指定大小输入缓冲区的缓冲字符输入流。

BufferedWriter 字符输出缓冲流

- BufferedWriter(Writer out)： 创建一个使用默认大小输出缓冲区的缓冲字符输出流。

- BufferedWriter(Writer out, int size)： 创建一个使用指定大小输出缓冲区的新缓冲字符输出流。

### 序列化

序列化：将一个对象存放到某种类型的永久存储器上称为保持

#### Serializable

如果你希望类能够序列化和反序列化，必须实现 java.io.Serializable 接口。

#### ObjectInputStream

ObjectInputStream 能够让你从输入流中读取 Java 对象，而不需要每次读取一个字节。你可以把 InputStream 包装到 ObjectInputStream 中，然后就可以从中读取对象了。

#### ObjectOutputStream

ObjectOutputStream 能够让你把对象写入到输出流中，而不需要每次写入一个字节。你可以把 OutputStream 包装到 ObjectOutputStream 中，然后就可以把对象写入到该输出流中了。