# JSP

## 认识

1. jsp就是JavaServer Pages（基于java语言实现的服务端页面）
2. jsp是13个规范之一
3. jsp是java程序，本质还是servlet
    1. 访问jsp文件实际上会由服务器先生成java文件，再由jvm编译为class文件。所以最后实际上执行的是class文件
    2. 由服务器生成的java源码中只有一个xxx_jsp类，该类继承HttpBase，HttpBase继承HttpServlet。故jsp类就是一个servlet类。
    3. 故第一次访问jsp文件缓慢
4. 每一个web容器都会内置一个jsp翻译引擎
5. 在jsp文件中直接编写文本，文本会在java文件的service方法中写在out.write()内。即当作普通字符串输出到浏览器上。

## 语法

### 乱码

jsp的page指令，解决响应时的乱码问题

在jsp文件的首行加上`<%@page contentType="text/html;charset=UTF-8"%>`

### 写java语句

1. `<% java语句 %>`，在`<%%>`内部写的语句被视为java语句，直接放在service方法里

    1. 该语法内部可以直接使用out.write()，即可以当作jsp的输出语句。（out是jsp的九大内置对象之一，九大内置对象只能在service方法里使用）

2. `<%--  --%>`该注释不会被编写到java文件中，`<!---->`该注释会编写到java文件中

3. `<%!  %>`，在内部写的语句，直接放在jsp类中。

    不建议使用。servlet类中写静态变量、示例变量都会在线程问题