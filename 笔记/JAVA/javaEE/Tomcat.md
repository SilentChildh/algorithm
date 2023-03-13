# Tomcat

简单来说就是一个容器，用于管理servlet对象。

## 1. 相关路径

bin：存放服务器的目录。比如启动和关闭Tomcat

conf：存放服务器的配置文件。在server.xml文件中可以配置端口号，默认是8080.

lib：存放服务器的核心程序。存放jar包。

logs：存放服务器的日志目录。服务器启动等信息都会在这个目录下生成日志文件。

temp：存放服务器的临时文件。

webapps：存放webapp（web application：即web应用）

work：存放jsp文件编译后的  java文件和class文件

## 2. 配置环境

1. 需要配置path，对应bin目录
2. 配置JAVA_HOME，对应java的根目录
3. 配置CATALINA_HOME,对应tomcat根目录



## 3. 目录结构

在webapps目录下，创建newwebapp目录

~~~
newwebapp
	|--WEB-INF（该目录下的资源是受保护的，无法从外界访问）
		|--classes
		|--lib
		|--web.xml
	|--html
	|--css
	|--javascript
	|--image
~~~



## 4. 模拟tomcat底层创建servlet对象

模拟部分，其中还有创建ServletContext对象、ServletConfig对象、ServletRequest对象、SerlvetResponse对象等

~~~~java
public class Tomcat {
    public static void main(String args[]) {
        // 反射创建
        Class clazz = Class.forName("...");
        Object obj = clazz.newInstance();
        // 向下转型
        Servlet servlet = (Servlet)obj;
        // 创建ServletConfig对象
        servletConfig servletConfig = new ServletConfigImpl();//实际上是StandardWrapperFacade();是ServletConfig接口的实现类
        // 调用Servlet的init()方法
        servlet.init(servletConfig);
        // 调用servlet的service()方法
        // ...
    }
}
~~~~

## 5. 主要对象的创建顺序

1. ServletContext对象（web应用唯一）
2. ServletConfig对象（web应用唯一）
3. ServletRequest对象、SerlvetResponse对象





# Tomcat乱码问题

~~~java
// Tomcat10之后，request请求体当中的字符集默认就是UTF-8，不需要设置字符集，不会出现乱码问题。
// Tomcat9前（包括9在内），如果前端请求体提交的是中文，后端获取之后出现乱码，怎么解决这个乱码？执行以下代码。
request.setCharacterEncoding("UTF-8");

// 在Tomcat9之前（包括9），响应中文也是有乱码的，怎么解决这个响应的乱码？
response.setContentType("text/html;charset=UTF-8");
// 在Tomcat10之后，包括10在内，响应中文的时候就不在出现乱码问题了。
~~~







# 开发流程

## 1. 原始的开发流程



1. 在webapps目录下新建目录（即具体webapp的名字）

2. 在具体的webapp下再新建目录，命名为`WEB-INF`(全部大写)

3. 在WEB-INF目录下新建目录，命名为`classes`（全部小写）该目录存放字节码文件

4. 在WEB-INF目录下新建目录，命名为lib（全部小写）。存放第三方jar包

5. 在WEB-INF目录下新建文件，命名为web.xml。该文件描述了请求路径与Servlet类之间的映射关系。

    以下为最基本的信息。

    ~~~xml
    <?xml version="1.0" encoding="UTF-8"?>
    
    <web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee
                          https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
      version="5.0"
      metadata-complete="true">
    
    </web-app>
    ~~~

6. 编写java程序，实现Servlet接口。

    1. java源码可以在任意位置写
    2. 要重写五个方法
    3. class字节码文件得放在classes目录下

7. 编译java程序

    1. 将servlet-api.jar包放在CLASSPATH环境变量中。以便正常编译

8. 将class字节码文件拷贝到classes目录下

9. 在web.xml文件中编译配置文件。即注册Servlet类。

    ~~~xml
    <?xml version="1.0" encoding="UTF-8"?>
    
    <web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee
                          https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
      version="5.0"
      metadata-complete="true">
    	<!--servlet描述信息-->
        <!--一格servlet对应一个servlet-mapping-->
        <servlet>
            <!--这里的名字与下面的要一致-->
        	<servlet-name>...</servlet-name>
            <servlet-class>全限定类名</servlet-class>
        </servlet>
        <!--servlet映射信息-->
        <servlet-mapping>
            <!--这里的名字与上面的要一致-->
        	<servlet-name>...</servlet-name>
            <!--该位置填写路径。必须以/开头-->
            <!--该url就是外界访问的路径-->
            <url-pattern>/.../.../...</url-pattern>
        </servlet-mapping>
    </web-app>
    ~~~

    通过yrl找到name，从name找到class

10. 访问：`http://ip:端口/项目名/url-pattern`.

    1. 项目名即具体webapp目录的名称

11. url太复杂的问题

    1. html文件放在WEB-INF目录外，即web目录下
    2. 在使用超链接时，链接WEB-INF内的web.xml中的url-pattern
    3. 由于html文件存放的位置，因此在使用相对路径来链接时，一般都以`/项目名/url-pattern`为href
        1. 实际上还是先获取/项目的路径
        2. 然后通过web.xml文件访问url-pattern路径
        3. 最后顺藤摸瓜找到class文件
    4. 外界访问时，就可以通过访问html页面，然后点击链接即可跳转至指定页面。

12. 无需编写main方法，tomcat服务器启动时就是调用main方法。只需要编写Servlet的实现类，然后注册到web.xml文件中即可.

## 2. idea内开发

1. 创建新模板，即新项目
2. 右键，`Add Framework Surpport`，添加Web Application
3. Run/Debug Configurations 配置部署
    1. Tomcat Server
    2. Name可以改为项目名
    3. 在Deploy添加artifacts
4. 在WEB-INF目录下新建lib，将依赖jar包放入
5. 自定义的servlet类继承HttpServlet
6. 将servlet类注册到web.xml中，或者是注解中@WebServlet
7. 根据需求重写对应的请求方法（doGet、doPost）
    1. 实际上还是由tomcat调用service方法，但被HttpServlet重写的该方法内部会调用doXxx方法
    2. 故当不需要，或者不确定调用doXxx方法时，可以自己重写service方法