

## 简单的文件操作

### 创建文件对象

~~~java
new File(String pathname);//根据路径
new File(File parent, String child);//根据父目录文件 + 子路径
new FIle(String parent, String child);//根据父目录 + 子路径
~~~

### 创建新文件

创建时，需抛出异常

~~~java
file.createNewFile();//根据文件对象创建新文件
~~~

### 获取文件信息

~~~java
file.getName();//获取文件名称
file.getAbsolutePath();//获取文件的绝对路径
file.getParent();//获取文件的父级目录
file.length();//获取文件的字节大小
file.exists();//文件是否存在
file.isFile();//是否是文件
file.isDirectory();//是否是目录
~~~



### 创建目录和文件删除

~~~java
mkdir();//创建一级目录
mkdirs();//创建多级目录
delete();//删除 空目录 或 文件
~~~

## IO流

## 分类

按数据单位：字节流的二进制文件和字符流的文本文件

按数据流的流向：输入流和输出流

流的角色：节点流和处理流/包装流

![](https://img-blog.csdnimg.cn/01399f5e466147a69952416b3e7cf020.png)

### InputStream

是所有字节输入流的超类

其下的常用子类为：文件输入流`FileInputStream`、缓冲字节输入流`BufferedInputStream`(`FiltereInputStream`的子类)、对象字节流`ObjectInputStream`



## 节点流与处理流

节点流：可以从特定的数据源读写数据

处理流：也称包装流。是连接在已存在的流之上的流，为程序提供更强大的读写功能。

![](https://img-blog.csdnimg.cn/9a6c1b8cc592494c8f02ba47ac0d9041.png)

## 访问文件的节点流

### FileInputStream

~~~java
new FileInputStream(String/File);//创建对象

int readData = 0;
while((readData = fileInputStream.read()) != -1) {//循环读取，直到文件末尾，然后返回-1
    //读取的数据在readData中
}
//利用read(byte[] b)，提高读取效率
int readLen = 0;
byte[] buf = new buf[8];//指定一次读取的字符数量
while((readLen = fileInputStream.read(buf) != -1) {//返回读取到的字符数量
    //读取的数据在buf数组中
    //System.out.print(new String(buf, 0, readLen));//打印读入数据
    
}

fileInputStream.close();//关闭文件资源

~~~



### FileOututStrream

~~~java
new FileOutputStream(Strint/File, boolean);//创建对象，以及是否追加（true），覆盖（false）

fileOutputStream,write(int);//写入一个字符
fileOutputStream.write(byte[] b, int, int);//写入字符串，指定写入起始偏移量和长度
//可通过String中的getByte()成员方法将字符串转换为byte数组。
fileOutoutStram.write(byte[] b/string.getByte());//写入字符串

close();
~~~

### FileIn与FileOut的搭配

~~~java
package com;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;

public class Stream {
    public static void main(String[] args) {
        String srcPath = "e:/Git";
        String destPath = "e:/Git/Github";
        FileInputStream fileInputStream = null;
        FileOutputStream fileOutputStream = null;

        try {
            fileInputStream = new FileInputStream(srcPath);
            fileOutputStream = new FileOutputStream(destPath);

            byte[] buf = new byte[1024];
            int readLen = 0;
            while((readLen = fileInputStream.read(buf)) != -1) {
                //在循环写入时，一定是三个参数
                //因为循环读取时，byte数组并不是清空再接收，而是数据覆盖
                //所以在最后可能会出现数据量小于数组容量的情况，那么实际上接收的是数组的前半段，而后半段是上一轮接收过的数据
                fileOutputStream.write(buf, 0, readLen);
            }

        } catch (IOException e) {
            throw new RuntimeException(e);
        }
        finally {
            try {
                if(fileInputStream != null) fileInputStream.close();
                if(fileOutputStream != null) fileOutputStream.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
    }
}
~~~

### FileReader

~~~java
new FileReader(String/File);//创建对象

read();//读取字符
read(char[]);//读取字符串，返回字符长度
//相关API
new String(char[]);
new String(char[], int ,int);//指定起始偏移量和长度

//循环读取的模板与InputStream一致
//单个字符则用int readData接收
//字符串则用 char[] buf数组接收。System.out.print(new String(buf, 0, readLen));

close();
~~~

### FileWriter

~~~java
new FileWriter(String/File, boolean);//创建对象，是否追加

write(int);//写入单个字符
write(char[]);
write(char[], int, int);
write(String);
write(String, int ,int);
//相关API
string.toCharArray();//字符串转换为字符数组

close();
~~~





## 缓冲流

### 特点

处理流连接在已存在的流之上的流。

处理流可以消除不同流之间的差异，同时也提供了更方便的方法完成读写功能

处理流使用了修饰器设计模式，不会数据源相连。

### 性能

1. 增加缓冲
2. 提供一次性输入输出大数据

### BufferedReader

内含Read属性，用于接收其他流。

~~~java
new BufferedReader(new FileInputStream(String/File));

String line;//按行接收数据,效率高

while((line = bufferedReader.readLine()) != null) {//按行读取，不包括换行符，末尾时返回null
    System.out.println(line);
}

bufferedReader.close();//关闭处理流，自动关闭被包装的流
~~~

### BufferedWriter

~~~java
new BufferedWriter(new FileOutputStream(String/File, boolean));

write(String);
newLine();//根据操作系统添加换行符

close();
~~~

### Buffered读写示例

~~~java
import java.io.*;
public class BufferedCopy_ {
    public static void main(String[] args) {
        String srcFilePath = "e:\\a.java";
        String destFilePath = "e:\\a2.java";
        BufferedReader br = null;
        BufferedWriter bw = null;
        String line;
        try {
            br = new BufferedReader(new FileReader(srcFilePath));
            bw = new BufferedWriter(new FileWriter(destFilePath));
            //说明: readLine 读取一行内容，但是没有换行
            while ((line = br.readLine()) != null) {
                //每读取一行，就写入
                bw.write(line);
                //插入一个换行
                bw.newLine();
            }
            System.out.println("拷贝完毕...");
        } 
        catch (IOException e) {
            
        } 
        finally {
            //关闭流
            try {
                if(br != null) br.close();
                if(bw != null) bw.close();
            } 
            catch (IOException e) {
                
            }
        }
    }
}
~~~

### BufferedInputStream与BufferedOutputStream

内含缓冲数组

~~~java
package com;
import java.io.*;
public class BufferedCopy02 {
    public static void main(String[] args) {
        String srcFilePath = "e:\\a.java";
        String destFilePath = "e:\\a3.java";

        //创建BufferedOutputStream对象BufferedInputStream对象
        BufferedInputStream bis = null;
        BufferedOutputStream bos = null;
        try {
            //因为 FileInputStream  是 InputStream 子类
            bis = new BufferedInputStream(new FileInputStream(srcFilePath));
            bos = new BufferedOutputStream(new FileOutputStream(destFilePath));
            //循环的读取文件，并写入到 destFilePath
            byte[] buff = new byte[1024];
            int readLen = 0;
            //当返回 -1 时，就表示文件读取完毕
            while ((readLen = bis.read(buff)) != -1) {
                bos.write(buff, 0, readLen);
            }
            System.out.println("文件拷贝完毕~~~");
        } 
        catch (IOException e) {
        } 
        finally {
            //关闭流 , 关闭外层的处理流即可，底层会去关闭节点流
            try {
                if(bis != null) bis.close();
                if(bos != null) bos.close();
            catch (IOException e) {
            }
        }
    }
}
~~~

### 总结

二进制文件的读写操作继承于父类，主要的提升是缓冲的存在。

文本文件的读写操作主要的提升是提供了额外功能：按行读取和添加换行符的操作。

## 对象流

关于序列化详解的[文章](https://www.cnblogs.com/9dragon/p/10901448.html)

### 序列化与反序列化

序列化就是保存数据时，保存数据的值和类型

反序列化就是恢复数据时，恢复数据的值和类型

## IDEA内置序列化版本号

`Settings->Editor->Inspections->JVM languages->Serialzable class without serialVersionUID`



### 序列化机制

通过实现`Serializable`或`Externalizable`接口来成为可序列化。

一般用`Serializable`接口实现，因为这个接口中不需要重写方法，而另一个需要重写方法。

### ObjectOutputStream

提供序列化功能

~~~java
new ObjectOutputStream(new FileOutputStream(String/File, boolean));

writeInt(100);
writeBoolean(true);
writeDouble(3.14);
writeChar('H');
writeUTF("hello");
writeObject(自定义对象);

close();
~~~





### ObjectInputStream

提供反序列化功能

~~~java
new ObjectInputStream(new FileInputStream(String/File));

readInt();
readBoolean();
readDouble();
readChar();
readUTF();
readObject();

close();
~~~



### 序列化与反序列化方法的执行顺序

针对Serializable

1. `writeResolve()`，任意目标对象替换序列化对象

2. `writeObject()`，序列化对象

3. `readObejct()`，反序列化对象

4. `readReplace()`，任意目标对象替换反序列化对象

    1. 如在单例模式中，就可以用到该方法。防止反序列化对单例的破坏。

    

    



### 注意事项

使用：

1. 读写的顺序要一致
2. 序列化和反序列化对象需要实现`Serializable`接口
3. 序列化对象中，默认将所有属性进行序列化。除了`static`和`transient`修饰的成员
    1. **序列化保存的是对象的状态**，静态变量属于类的状态，因此 序列化并不保存静态变量。

4. 序列化对象中，其中用于接收其他类对象的属性，所接收的类也得实现`Serializable`
5. 序列化也具备继承性。父类是可序列化，那么子类也可序列化
6. 反序列化对象时，一定是通过二进制数组转换而来

细节：

1. 反序列化得到的对象不是通过构造器得到的，而是由jvm生成
2. 已自定义序列化版本号，在序列化后，修改源码
    1. 源码新增成员，那么反序列化时，该成员赋上默认值
    2. 源码减少成员，那么反序列化时，忽略该成员



1. 序列化版本号JVM会帮我们默认生成，我们也可以自己指定或者使用IDE进行自动生成。
2. 如果我们让JVM为我们默认生成，如果我们对序列化对应的模板类进行更改，序列号也会随着发生改变。
3. “改变”包括方法签名、修饰符、字段、类名、继承关系等结构上的变化。
    1. 如果内容（方法的内容、属性的默认值等）、顺序（声明的顺序）、空格的修改并不会新生成序列号（已经经过实验）。
    2. 只有当影响到class文件本身结构发生的变化的修改，才会使得生成一个新的版本号

### java序列化算法

1. 所有保存到磁盘的对象都有一个<u>序列化版本号</u>
2. 当程序试图序列化一个对象时，会先检查此对象是否已经序列化过，只有此对象从未（在此虚拟机）被序列化过，才会将此对象序列化为字节序列输出。
3. 如果此对象已经序列化过，则直接输出编号即可。

- 如果序列化一个可变对象（对象内的内容可更改）后，更改了对象内容，再次序列化，并不会再次将此对象转换为字节序列，而只是保存序列化编号。

![](https://img2018.cnblogs.com/blog/1603499/201905/1603499-20190521180352659-740977206.jpg)



### 建议

- 程序创建的每个JavaBean类都实现Serializeable接口。
- 序列化类中建议建议添加序列化版本号`private static final long serialVersionUID`，提高版本兼容性
    - 自定义了序列号版本号后，就不会出现序列化算法导致的问题——成员的值修改后反序列化无效、成员类型修改后反序列化失败。因为自定义后，class文件中版本号一定是一致的。

### java序列化原理

序列化只保存对象的类型信息和属性类型和属性值，三部分数据。

1. Java序列化的顺序首先按照从子类到父类的顺序，将类型的元信息、属性类型的元信息进行序列化。(针对数据类型)
2. 然后按照从父类到子类的顺序，将成员的字面量进行序列化。如果遇到引用类型，则会进入新的层次的递归。直到所有字面量数据被保存起来。(针对数据的值)



1. 序列化流中本质上是没有保存任何引用数据的，都是字面量数据。
2. 因此当进行反序列化化时，其实本质上是为空对象进行字面量赋值，如果涉及引用变量则递归进行字面量赋值。



### java反序列化原理？？？？？？？？？？？？？？？？？？？？？？？？？？？？

反序列化时，必须有序列化对象的class文件，因为需要用到反射。

反序列化过程：

1. 读出序列化的数据到ObjectStreamClass类型的desc对象中
2. 通过反射，获取对应的对象
3. 再调用native方法，将desc赋值给对应的对象。
4. 最后返回实例

### 对于Serializable的继承性

- 由于Serializable对象完全以它存储的二进制位为基础来构造，并不会调用任何构造函数，因此Serializable类无需默认构造函数。
- 一种情况除外：父类没有实现serializable但是子类实现了，如果此时父类如果没有定义默认构造函数，就会报错。因为反序列化时，往往是从超类对象开始构造，
    1. 虽然此时字节流中是没有存放任何父类信息的。因为序列化时，未保存父类信息。
    2. 但是反序列化创建对象时，必须拿到父类继承下来的属性。因此反序列化时会调用父类的默认构造方法拿到这部分。
    3. 这时就会反射调用空参构造器先拿到一个空对象，然后再慢慢地为填充成员注入值（可以类比Spring Bean的创建过程）

## 标准输入输出流

~~~java
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.util.Scanner;
public class InputAndOutput {
    public static void main(String[] args) {
        //System 类 的 public final static InputStream in = null;
        // System.in 编译类型   InputStream
        // System.in 运行类型   BufferedInputStream
        // 表示的是标准输入 键盘
        System.out.println(System.in.getClass());

        //老韩解读
        //1. public final static PrintStream out = null;
        //2. 编译类型 PrintStream
        //3. 运行类型 PrintStream
        //4. 表示标准输出 显示器
        System.out.println(System.out.getClass());
        System.out.println("hello");
        Scanner scanner = new Scanner(System.in);
        System.out.println("输入内容");
        String next = scanner.next();
        System.out.println("next=" + next);
        
        
        
        //我们可以去修改打印流输出的位置/设备
        System.setOut(new PrintStream("e:\\f1.txt"));//此时输出到制定文件中
        System.out.println("hello");
    }
}
~~~

## 打印流

只有输出流，

~~~java
package com.hspedu.printstream;
import java.io.IOException;
import java.io.PrintStream;
/**
 * 演示PrintStream （字节打印流/输出流）
 */
public class PrintStream_ {
    public static void main(String[] args) throws IOException {
        PrintStream out = System.out;
        //在默认情况下，PrintStream 输出数据的位置是 标准输出，即显示器
        out.print("john, hello");
        //因为print底层使用的是write(), 所以我们可以直接调用write进行打印/输出，本质是一样的
        out.write("韩顺平,你好".getBytes());
        out.close();
    }
}
~~~

~~~java
package com.hspedu.transformation;
import java.io.FileWriter;
import java.io.IOException;
import java.io.PrintWriter;
/**
 * 演示 PrintWriter 使用方式
 */
public class PrintWriter_ {
    public static void main(String[] args) throws IOException {
        //PrintWriter printWriter = new PrintWriter(System.out);
        PrintWriter printWriter = new PrintWriter(new FileWriter("e:\\f2.txt"));
        printWriter.print("hello");
        printWriter.close();//flush + 关闭流, 才会将数据写入到文件..
    }
}
~~~



## 转换流

~~~java
BufferedReader bufRead = new InputStreamReader(new FileInputStreasm(String/File), "utf-8");
String s = bufRead.readLine();
bufRead,close();

BUfferedWriter bufWrite = new OutputStreamWriter(new FileOutputStream(String/File),"utf-8");
bufWrite.write("hello");
bufWrtite.close();
~~~

## Properties

是专门用于读写配置文件的集合类

配置文件格式：`键=值`。其中键值对之间没有空格，值不需要引号，默认类型String。

~~~java
new Properties();//创建对象

properties.load(Reader/InputStream);//加载配置文件到properties对象

list(PrintStream/PrintWriter);//指定显示数据的设备位置

getProperty(key);
seteProperty(key, value);//key存在时，替换value

store(Writer/OutputStream, String);//保存对象中的数据到配置文件中，其中String作为标注放在配置文件开头

~~~

~~~java
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;
import java.util.Properties;
public class Properties02 {
    public static void main(String[] args) throws IOException {
        //使用Properties 类来读取mysql.properties 文件
        //1. 创建Properties 对象
        Properties properties = new Properties();
        //2. 加载指定配置文件
        properties.load(new FileReader("src\\mysql.properties"));
        //3. 把k-v显示控制台
        properties.list(System.out);
        //4. 根据key 获取对应的值
        String user = properties.getProperty("user");
        String pwd = properties.getProperty("pwd");
        System.out.println("用户名=" + user);
        System.out.println("密码是=" + pwd);
    }
}
~~~





# URL

1. URL：统一资源定位符

    1. `协议://主机:端口/路径/请求`；`protocol://host:port/path/query`

    对于java中：

    ~~~java
    URL url = ...;
    url.getProtocol();// 获取协议
    url.getHost();// 获取主机
    url.getPort();// 获取端口
    url.getPath();// 获取路径
    url.getQuery();// 获取请求
    url.getFile());// 获取路径+请求
    ~~~

2. URI：统一资源标识符
