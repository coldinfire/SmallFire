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

![流类图](/images/JAVA/JAVA_IO_Stream.png)

#### IO 流分类

根据数据处理的不同类型分为：字节流和字符流。

字节流和字符流的区别：

- 读写单位的不同：字节流以字节（8bit）为单位。字符流以字符为单位，根据码表映射字符，一次可能读多个字节

- 处理对象不同：字节流可以处理任何类型的数据，如图片、avi等，而字符流只能处理字符类型的数据

根据数据流向不同分为：输入流和输出流

- 输入流表示把数据从其他设备上读取到内存中的流
- 输出流表示把数据从内存中写出到其他设备上的流
- 程序中需要对于传输数据的不同特性而使用不用的流

输入流与输出流使用步骤：

| 使用输入流步骤               | 使用输出流步骤               |
| :--------------------------- | :--------------------------- |
| 设定输入流的数据源           | 设定输出流目的地             |
| 创建指向数据源的输入流       | 创建指定目的地的输出流       |
| 使用输入流读取数据源中的数据 | 使用输出流把数据写入到目的地 |
| 关闭输入流                   | 关闭输出流                   |

### 文件类 File

File 类是对文件系统中文件以及文件夹进行封装的对象，可以通过对象的思想来操作文件和文件夹。只能对文件本身进行操作，不能对文件内容进行操作。

File 类保存文件或目录的各种数据信息，包括文件名、文件长度、最后修改时间、是否可读、获取当前文件的路径名、判断文件是否存在、获取当前目录中的文件列表、创建、删除文件和目录等方法。

File类常用方法：

File的常用方法主要分为获取功能、获取绝对路径和相对路径、判断功能、创建删除功能的方法。

- File.separator：separator 方法会自动获取不同平台的分隔符

| Method                                                       |
| :----------------------------------------------------------- |
| File(String pathname)：通过将给定的路径名字符串转换为抽象路径名来创建新 File实例 |
| File(String parent, String child)：根据父路径名字符串和子路径名字符串创建新 File 实例 |
| File(File parent, String child)：从父抽象路径名和子路径名字符串创建新的 File实例 |
| boolean createNewFile() ：文件不存在，创建一个新的空文件并返回true，文件存在，不创建文件并返回false |
| boolean mkdir()：创建此抽象路径名指定的目录，父级目录不存在，则创建失败 |
| boolean mkdirs()：创建此抽象路径名指定的目录，包括所有必需但不存在的父目录 |
| boolean delete()：删除此抽象路径名表示的文件或目录，不能删除非空文件夹 |
| boolean renameTo(File dest)： 重新命名此抽象路径名表示的文件 |
| boolean setReadOnly()：标记此抽象路径名指定的文件或目录，从而只能对其进行读操作 |
| boolean exists()：测试此抽象路径名表示的文件或目录是否存在   |
| boolean isDirectory()：测试此抽象路径名表示的文件是否是一个目录 |
| boolean isFile()：测试此抽象路径名表示的文件是否是一个标准文件 |
| boolean isAbsolute()：测试此抽象路径名是否为绝对路径名       |
| boolean canExecute()/canRead()/canWrite：测试应用程序是否可以执行/读取/修改此抽象路径名表示的文件 |
| String getName()：返回此 File 表示的文件或目录的名称         |
| String getPath()：获取相对路径，构造方法中传入的那个字符串   |
| String getAbsolutePath()：返回此抽象路径名的绝对路径名字符串 |
| String[] list()：返回一个 String 数组，表示该 File 目录中的所有子文件或目录 |
| File[] listFiles()：返回一个 File 数组，表示该 File 目录中的所有的子文件或目录，**指定的必须是目录且存在** |

### 字节流

字节流的输入与输出对应图：

![字节流](/images/JAVA/JAVA_IO_Byte.png)

#### 输入字节流 InputStream

InputStream 是所有输入字节流的父类，可以读取字节信息到内存中。它定义了字节输入流的基本共性功能方法。

![输入字节流](/images/JAVA/JAVA_IO_InputStream.png)

FileInputStream 类常用的方法：

| Method                          | Description                      |
| :------------------------------ | :------------------------------- |
| FileInputStream(File file);     | 通过传入的File对象创建对象       |
| `FileInputStream(String.name);` | 通过传入的文件系统路径名创建对象 |

使用字节数组读取：

```java
public class FileInputStreamDemo {
    public static void main(String[] args) {
        FileInputStream fis = null;
        try {
            // 使用文件名称创建流对象
            fis = new FileInputStream("src/file/demo1.txt");
            // 定义变量，用来判断字节数组的有效个数
            int len;
            // 定义字节数组，作为装字节数据的容器
            byte[] bys = new byte[1024];
            // 循环读取
            while ((len = fis.read(bys)) != -1) {
                // 每次读取后,把数组的有效字节部分，变成字符串打印
                System.out.println(new String(bys, 0, len));
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                fis.close();    // 关闭资源
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}

```

#### 输出字节流 OutputStream

OutputStream 是所有输出字节流的父类，将指定的字节信息写出到目的地。它定义了字节输出流的基本共性功能方法。

![输出字节流](/images/JAVA/JAVA_IO_OutputStream.png)

FileOutputStream 类常用方法：

| Method                                        | Description                                                  |
| :-------------------------------------------- | :----------------------------------------------------------- |
| FileOutputStream(File file);                  | 创建一个向指定File对象表示的文件中写入数据的文件输出流       |
| FileOutputStream(File file, boolean append);  | 创建一个向指定File对象表示的文件中写入数据的文件输出流，append 设置是否使用追加模式添加数据 |
| `FileOutputStream(String name);`              | 创建一个向具有指定名称的文件中写入数据的输出文件流           |
| FileOutputStream(String name,boolean append); | 创建一个向具有指定名称的文件中写入数据的输出文件流，append 设置是否使用追加模式添加数据 |

使用字节数组 write 方法：Windows换行是 `\r\n`；Linux 是 `\n`

```java
public class FileOutputStreamDemo {
    public static void main(String[] args) throws IOException {
        FileOutputStream outputStream = null;
        try {
            // 使用文件名称创建流对象
            outputStream = new FileOutputStream("src/file/demo1.txt");
            // 字符串转换为字节数组
            byte[] b = "abcde".getBytes();
            // 写出从索引1开始，3个字节。索引1是b，3个字节，也就是bcd。
            fos.write(b,1,3);
        }catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                outputStream.close();  // 关闭资源
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 字符流

字符流的由来：因为数据编码的不同，因而有了对字符进行高效操作的流对象，字符流本质其实就是基于字节流读取时，去查了指定的码表，而字节流直接读取数据会有乱码的问题（读中文会乱码）。

字符流以`字符`为单位读写数据，字符流`专门用于处理文本`文件。如果处理纯文本的数据优先考虑字符流。

字符流的输入与输出对应图：

![字符流](/images/JAVA/JAVA_IO_Char.png)

#### 字符输入流Reader

Reader 是所有的输入字符流的父类，可以读取字符信息到内存中。它定义了字符输入流的基本共性功能方法。

Reader的主要方法：

- int read()：从输入流中读取单个字符。读取到文件末尾，返回`-1`
- abstract void close()：关闭流，并且释放该流占用的所有系统资源
- int read(char[] cbuf)：从当前位置读取 cbuf.length 个字符到 cbuf 数组中，但实际读取的字符个数取决于流中剩余字符个数。返回值为实际读取的字符个数
- abstract int read(char[] cbuf, int off, int len)：从当前位置读取 len 个字符到 cbuf 中，第一个字符存放在cbuf[off] 中，第二个字符存放在 cbuf[off+1] 中，依次类推。实际读取的字符的个数取决于流中剩余的字符个数，返回值为实际读取的字符个数

使用字符数组读取数据：

```java
public class ReaderDemo {
    public static void main(String[] args) {
        Reader reader = null;
        try {
            reader = new FileReader("src/file/demo1.txt");
            int len;
            char[] c = new char[1024];
            while ((len = reader.read(c)) != -1) {
                System.out.println(new String(c));
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
		} finally{
            try {
                reader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

#### 字符输出流Writer

Writer 是所有输出字符流的父类，将指定的字符信息写出到目的地。它同样定义了字符输出流的基本共性功能方法。

Writer的主要方法：

- Writer append(char c)：追加字符c到此Writer流中，追加到尾部。返回值为追加后的此 Writer
- Writer append(CharSequence csq)：追加字符序列 csq 到此 Writer 流中，追加到尾部，返回值为追加后的此 Writer
- Writer append(CharSequence csq,int start,int end)：追加字符序列 csq 的子序列到 Writer 流中,子序列为序列 csq 的 start 到 end 部分，追加到尾部，返回值为追加后的此序列
- void flush()：刷新该流的缓冲
- abstract void close()：关闭流，在关闭之前先调用flush()
- void write(String str)：将字符串写入到流中
- void write(String str, int off, int len)： 给定字符串中起始于偏移量 off 处取 len 个字节写到目的地
- void write(int c)：写入单个字符
- void write(char[] cbuf)：写入字符数组
- void write(char[] cbuf, int off, int len)：写入字符数组的某一部分,off数组的开始索引,len写的字符个数

FileWriter 写出数据：

```java
public class FileWriterDemo {
    public static void main(String[] args) {
        Writer writer = null;
        try {
            writer = new FileWriter("src/file/demo2.txt");
            char[] cbuf = "abcdefghigh".toCharArray();
            writer.write(cbuf);
            writer.flush();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                writer.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 缓冲流

缓冲流在创建流对象时，会创建一个内置的默认大小的缓冲区数组，通过缓冲区读写，减少系统IO次数，从而提高读写的效率。缓冲流是对文件流处理的一种流，增强了读写文件的能力。

缓冲流读写方法与基本的流是一致的。

- flush()：从缓冲区把内容写出，刷新缓冲区，流对象可以继续使用


- close()：是将内容从缓冲区内写出并且关闭相应的流

#### 字节缓冲流

BufferedInputStream：字节缓冲输入流

- BufferedInputStream(InputStream in)：创建一个使用默认大小（8bit）的缓冲输入流
- BufferedInputStream(InputStream in, int size)：创建一个新的缓冲输入流，使用指定大小 size

BufferedOutputStream：字节缓冲输出流

- BufferedOutputStream(OutputStream out)：创建一个使用默认大小（8bit）的缓冲输出流
- BufferedOutputStream(OutputStream out, int size)：创建一个新的缓冲输出流，使用指定大小 size

字节缓冲流使用：

```java
public class BufferedStreamDemo {
    public static void main(String[] args) throws FileNotFoundException {
        // 创建流对象
        BufferedInputStream bis = null;
        BufferedOutputStream bos = null;
        try {
            bis = new BufferedInputStream(new FileInputStream("src/file/demo1.txt"));
            bos = new BufferedOutputStream(new FileOutputStream("src/file/demo2.txt"));
            int len;   // 读写数据
            byte[] bytes = new byte[2048];
            while ((len = bis.read(bytes)) != -1) {
                bos.write(bytes, 0 , len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally{
            try {
                bis.close();
                bos.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

#### 字符缓冲流

BufferedReader：字符缓冲输入流

- BufferedReader(Reader in)： 创建一个使用默认大小输入缓冲区的缓冲字符输入流
- BufferedReader(Reader in, int size)： 创建一个使用指定大小输入缓冲区的缓冲字符输入流
- String readLine()：读取一行数据。读取到最后返回 null

BufferedWriter：字符缓冲输出流

- BufferedWriter(Writer out)： 创建一个使用默认大小输出缓冲区的缓冲字符输出流
- BufferedWriter(Writer out, int size)： 创建一个使用指定大小输出缓冲区的新缓冲字符输出流
- void newLine()：换行，由系统属性定义符号

字符缓冲流的使用：

```java
public class BufferedWriteDemo {
    public static void main(String[] args) {
        BufferedReader br = null; // 创建流对象 源
        BufferedWriter bw = null; // 输出目标
        try {
            br = new BufferedReader(new FileReader("a.txt"));
            bw = new BufferedWriter(new FileWriter("b.txt"));
            String line = null;   // 读取数据
            while ((line = br.readLine()) != null) {
                bw.write(line);   // 写出拼接文本
                bw.newLine();     // 写出换行
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                // 释放资源
                br.close();
                bw.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 转换流

使用转换流解决编码异常问题。

#### InputStreamReader类：字节流到字符流的桥梁

读取字节，并使用指定的字符集将其解码为字符。它的字符集可以由名称指定，也可以接受平台的默认字符集。

- InputStreamReader(InputStream in)：创建一个使用默认字符集的字符流

- InputStreamReader(InputStream in, String charsetName)：创建一个指定字符集的字符流

示例代码：

```java
public class InpuStreamReaderDemo {
    public static void main(String[] args) {
        // 定义文件路径,文件为gbk编码
        String fileName = "a.txt";
        InputStreamReader isr = null;
        InputStreamReader isr2 = null;
        try {
            // 创建流对象,默认UTF8编码
            isr = new InputStreamReader(new FileInputStream(fileName));
            // 创建流对象,指定GBK编码
            isr2 = new InputStreamReader(new FileInputStream(fileName), "GBK");
            int read; // 定义变量,保存字符
            // 使用默认编码字符流读取,乱码
            while ((read = isr.read()) != -1) {
                System.out.print((char) read);
            }
            // 使用指定编码字符流读取,正常解析
            while ((read = isr2.read()) != -1) {
                System.out.print((char) read);// 哥敢摸屎
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
		} catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                isr.close();
                isr2.close();
            } catch (IOException e) {
               e.printStackTrace();
            }
        }
    }
}
```

#### OutputStreamWriter类：字符流到字节流的桥梁

使用指定的字符集将字符编码为字节。它的字符集可以由名称指定，也可以接受平台的默认字符集。

- OutputStreamWriter(OutputStream in)：创建一个使用默认字符集的字符流
- OutputStreamWriter(OutputStream in, String charsetName)：创建一个指定字符集的字符流

### 序列化

序列化：将一个对象存放到某种类型的永久存储器上称为保持。

Java 提供了一种对象`序列化`的机制。用一个字节序列可以表示一个对象，该字节序列包含该`对象的数据`、`对象的类型`和`对象中存储的属性`等信息。字节序列写出到文件之后，相当于文件中*持久保存*了一个对象的信息。

该字节序列还可以从文件中读取回来，重构对象，对它进行*反序列化*。`对象的数据`、`对象的类型`和`对象中存储的属性`信息，都可以用来在内存中创建对象。

![Serializable](/images/JAVA/JAVA_IO_Serializable.png)

#### Serializable

如果你希望类能够序列化和反序列化，必须实现 java.io.Serializable 接口。不实现此接口的类将不能使任何状态序列化或反序列化，会抛出`NotSerializableException` 。

该类的所有属性必须是可序列化的。如果有一个属性不需要可序列化的，则该属性必须注明是瞬态的，使用`transient` 关键字修饰。

#### ObjectOutputStream

将Java对象的原始数据类型写出到文件(序列化),实现对象的持久存储。

- ObjectOutputStream(OutputStream out)：创建一个指定 OutputStream 的 ObjectOutputStream
- void writeObject (Object obj)：将指定的对象写出

ObjectOutputStream 能够让你把对象写入到输出流中，而不需要每次写入一个字节。

#### ObjectInputStream

反序列化流，将使用 ObjectOutputStream 序列化的原始数据恢复为对象。ObjectInputStream 可以从输入流中读取 Java 对象，而不需要每次读取一个字节。

- ObjectInputStream(InputStream in)：创建一个指定 InputStream 的 ObjectInputStream
- Object readObject ()：读取一个对象。

当 JVM 反序列化对象时，必须是能够找到 class 文件的类。如果找不到该类的 class 文件，则抛出一个 `ClassNotFoundException` 异常。

当 JVM 反序列化对象时，能找到 class 文件，但是 class 文件在序列化对象之后发生了修改，那么反序列化操作也会失败，抛出一个`InvalidClassException`异常。

### 打印流

#### 打印流分类

字节打印流 PrintStream，字符打印流 PrintWriter。

打印流特点：

- 只操作目的地,不操作数据源
- 可以操作任意类型的数据
- 可以直接操作文件

