# 协议规范

1. BrowserServer与WebServer之间遵守HTTP协议
2. WebServer与WebServlet之间遵守Servlet协议
3. WebServlet与DBServer之间遵守JDBC协议



# servlet配置信息

## 1. web.xml的相关配置

### 1.1 常用配置

~~~xml
<?xml version="1.0" encoding="UTF-8"?>

<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee
                      https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
  version="5.0"
  metadata-complete="true">
    <!--配置web欢迎页面-->
    <welcome-file-list>
        <!--不能以”/“开头，默认从web目录下开始查找-->
        <welcome-file>xxx.html</welcome-file>
        <!--可以多个页面，当上面的找不到资源，就显示下面的-->
        <welcome-file>xxx.html</welcome-file>
	</welcome-file-list>
    
    <!--配置上下文参数信息，用ServletConfig获取-->
    <context-param>
        <param-name>...</param-name>
        <param-value>...</param-value>
    </context-param>

    <context-param>
        <param-name>...</param-name>
        <param-value>...</param-value>
    </context-param>
    
    <!--设置会话超时时长（minute，默认时间就是30分钟）-->
    <session-config>
        <session-timeout>30</session-timeout>
    </session-config>
    
	<!--servlet描述信息-->
    <!--一格servlet对应一个servlet-mapping-->
    <servlet>
        <!--这里的名字与下面的要一致-->
    	<servlet-name>...</servlet-name>
        <servlet-class>全限定类名</servlet-class>
        
        <!--在服务器启动时就创建servlet，按索引顺序执行创建，索引以0开始-->
        <load-on-startup>索引</load-on-startup>
        
        <!--Servlet对象初始化信息-->
        <init-param>
        	<param-name>driver</param-name>
            <param-value>com.mysql.jdbc.Driver</param-value>
        </init-param>
        <init-param>
        	<param-name>user</param-name></param-name>
            <param-value>root</param-value>
        </init-param>
    
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



### 1.2 设置欢迎页面

不能加斜杠`/`，因为在默认访问项目时，都会自动在最后加上`/`。即避免出现双斜杠的问题

在局部配置：web目录下的web.xml文件中

~~~xml
<welcome-file-list>
    <!--直接填写文件即可，默认从web目录下开始查找-->
	<welcome-file>xxx.html</welcome-file>
    
    <!--可以多个页面，当上面的找不到资源，就显示下面的-->
	<welcome-file>xxx.html</welcome-file>
</welcome-file-list>
~~~

在全局配置：CATALINA_HOME/conf/web.xml文件中有默认的欢迎界面

~~~xml
<welcome-file-list>
	<welcome-file>index.html</welcome-file>
	<welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
</welcome-file-list>
~~~



## 2. Servlet注解

### 2.1 使用

在自定义Servlet类上加上`@WebServlet`，位于jakarta.servlet.annotation.WebServlet。

注解对象的使用：`@注解(属性=值, 属性=值, @注解(属性=值, ...), ...)`

当注解的属性是数组时，若数组内只有一个元素，那么可以省略{}，直接赋值即可

当注解的属性是value时，value属性名可以省略，以下面两个为例：

~~~java
//以下都是可以的
@WebServlet(value = "路径")
@WebServlet("路径")
@WebServlet(value = {"路径", ...})
@WebServlet(urlPatterns = "路径")
@WebServlet(urlPatterns = {"路径", ...})
~~~



### 2.2 注解中常用的属性：

~~~java
    String name() default "";
	//以下的两个属性作用没有区别，只是名字不同
    String[] value() default {};
    String[] urlPatterns() default {};//当只有一个路径时，可以不用={"", ""}，而是直接赋值=""

    int loadOnStartup() default -1;

    WebInitParam[] initParams() default {};
~~~



### 2.3 示例

WebServlet的使用：

~~~java
@WebServlet(name = "insert", urlPatterns = {"/insert"}, loadOnStartup = 0,
    initParams = {
    @WebInitParam(name = "url", value = "jdbc:mysql://localhost/db_02?rewriteBatchStatement=true"),
    @WebInitParam(name = "", value = ""),
    @WebInitParam(name = "", value = "")})
~~~



# 原始Servlet对象

## 1. Servlet的service方法

~~~java
public void service(ServletRequset requst, ServletResponse response) throws ServletException, IOException{
    // 在获取流对象之前，设置信息类型
    response.setContentType("text/html");// 设置相应的内容是普通文本或者html代码
	PrintWriter out = response.getWriter();// 输出流，负责属输出字符串到浏览器
    // 将信息直接输出到浏览器
    out.print("hello");
    out.print(“<h1>world</h1>”);
     // 无需刷寻或关闭，该部分交由tomcat负责。
}
~~~

## 2. 关于Servlet对象

1. 创建、调用、销毁等等都是由WebServer负责的，程序员无权干涉

2. 服务器又称WEB容器

3. WEB容器管理Servlet对象的死活，存放在一个HashMap中

4. HashMap存放了请求路径与servlet对象的键值对

5. 一般启动服务器时，不会创建servlet对象

6. 特殊情况下，可以在web.xml文件中加上标签，可以在启动时就创建对象

    ~~~xml
        <servlet>
            <!--这里的名字与下面的要一致-->
        	<servlet-name>...</servlet-name>
            <servlet-class>全限定类名</servlet-class>
            <!--按索引顺序执行创建，索引以0开始-->
            <load-on-startup>索引</load-on-startup>
        </servlet>
    ~~~

7. 保证多线程共用一个对象资源

## 3. 生命周期

1. 默认情况下，服务器启动时，不会创建servlet对象
2. 用户发出第一次请求后，
    1. 对应的servlet对象被实例化（执行无参构造），不建议编写构造器，直接使用默认无参即可。
    2. 创建实例后，立马调用init()方法
    3. 然后调用service()方法
3. 后续然后再发送请求时，直接调用service()方法
    1. 说明servlet对象是单例的，但Servlet类不是单例模式，故称为假单例。
    2. 具体来看是因为Servlet对象是由WEB容器管理的，服务器只会创建一个Servlet对象，因此出现假单例。
    3. 单例模式是私有构造器。
4. 当关闭服务器时，先调用destroy()方法，再销毁对象。

5. init方法常用于初始化连接数据库、初始化线程池...
    1. init是为了防止要编写构造器时使无参构造器消失，而做出的一个安全替换
6. destroy方法，用于资源关闭、保存等





## 4. 模板设计模式

1. 在模板类（通常是抽象类）的模板方法（通常是final，且protected）中定义核心算法的框架（即包含了许多的语句、方法（包含抽象方法）等等大体执行顺序）
2. 其中具体的方法实现细节交由子类实现（一般来说就是重写抽象方法）



## 5. GenericServlet底层技巧

- 使用了适配器模式

- 模板设计模式。当子类继承父类，父类中某方法不想被重写时，该方法加上final。若子类有需求，需要加上自己的特殊方法（即像重写方法），且方法名又是一样的话。那么在父类需要额外重载一个同名的方法即可。子类只需重写该父类重载的方法。

~~~java
public Father {
    public final void init(...) {
        // ...
        this.init();//保证子类重写的init可以被调用
    }
    public void init() {
        // ...
    }
}
public Son extends Father {
    @Override
    public void init() {
        
    }
}
~~~

在tomcat中，调用的只会是实现了Servlet的类，因此，在init(...)方法中必须加上this.init()才能保证重写的init()被调用

## 6. HttpServlet

其中无参的service()方法运用了模板设计模式。在该方法中，定义了执行的顺序，即算法框架。

具体的实现细节交由子类完成。

若重写有参的service方法，那么无法享受405错误检测

需要什么请求方式就重写对应的方法，如重写doGet、doPost。假设不重写doGet方法，那么请求方式就必须是post，否则405报错.

源码的设计方式：

重点就是通过if条件语句，来判断调用doXxx方法。

~~~java
protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String method = req.getMethod();
        long lastModified;
        if (method.equals("GET")) {
            lastModified = this.getLastModified(req);
            if (lastModified == -1L) {
                this.doGet(req, resp);
            } else {
                long ifModifiedSince;
                try {
                    ifModifiedSince = req.getDateHeader("If-Modified-Since");
                } catch (IllegalArgumentException var9) {
                    ifModifiedSince = -1L;
                }

                if (ifModifiedSince < lastModified / 1000L * 1000L) {
                    this.maybeSetLastModified(resp, lastModified);
                    this.doGet(req, resp);
                } else {
                    resp.setStatus(304);
                }
            }
        } else if (method.equals("HEAD")) {
            lastModified = this.getLastModified(req);
            this.maybeSetLastModified(resp, lastModified);
            this.doHead(req, resp);
        } else if (method.equals("POST")) {
            this.doPost(req, resp);
        } else if (method.equals("PUT")) {
            this.doPut(req, resp);
        } else if (method.equals("DELETE")) {
            this.doDelete(req, resp);
        } else if (method.equals("OPTIONS")) {
            this.doOptions(req, resp);
        } else if (method.equals("TRACE")) {
            this.doTrace(req, resp);
        } else {
            String errMsg = lStrings.getString("http.method_not_implemented");
            Object[] errArgs = new Object[]{method};
            errMsg = MessageFormat.format(errMsg, errArgs);
            resp.sendError(501, errMsg);
        }

    }
~~~

## 7. 解决类爆炸问题

1. 遵守思想：一个类完成一个业务，一个方法解决一个业务需求/功能
2. 一个类中使用模板设计模式，在类中定义一个主方法，构建算法总体框架，其他方法为主要完成业务的do方法
    1. 即按HttpServlet的service方法来重写自己的service方法
    2. 在自己的service方法内也利用if来判断，执行什么具体业务需求







# ServletConfig对象

## 1. 关于对象

1. 是Servlet规范中的一员，为jakarta.servlet.ServletConfig接口
2. 一个Servlet对象对应一个ServletConfig对象
3. ServletConfig对象中包装的信息是tomcat解析的web.xml中的标签信息

在配置文件中可以添加初始化Servlet对象信息

~~~xml
    <servlet>
        <!--这里的名字与下面的要一致-->
    	<servlet-name>...</servlet-name>
        <servlet-class>全限定类名</servlet-class>
        <!--初始化信息-->
        <init-param>
        	<param-name>driver</param-name>
            <param-value>com.mysql.jdbc.Driver</param-value>
        </init-param>
        <init-param>
        	<param-name>user</param-name></param-name>
            <param-value>root</param-value>
        </init-param>
		<init-param>
        	<param-name>...</param-name>
            <param-value>...</param-value>
        </init-param>
		<init-param>
        	<param-name>...</param-name>
            <param-value>...</param-value>
        </init-param>
    </servlet>
~~~



## 2. 获取Servlet-name

`config.getServletName()`

## 3. 获取初始化参数

在<init-param></init-param>内的信息，会被tomcat自动封装到ServletConfig对象中

1. 通过getInitParameterNames()获得所有参数的名，返回Enumeration<Strin>集合
2. 可以通过getInitParameter(String name)获得参数值

~~~java
// 得到ServletConfig对象
ServletConfig config = super.getServletConfig();
// 获取初始化参数名集合
Enumeration<String> initParameterNames = config.getInitParameterNames();
// 遍历初始化参数名，与迭代器一致
while(initParameterNames.hasMoreElements()) {
    String parameterName = initParameterNames.nextElement();
    System.out.println(parameterName);// 打印参数名
    String parameterValue = config.getInitParameter(parameterName);// 通过参数名获取参数值
    System.out.println(parameterValue);// 打印参数值
}
~~~

在继承GenericServlet后，直接调用`super.getInitParameter(String name)`和`super.getInitParameterNames()`即可。

## 4. 获取ServletContext

~~~java
ServletContext application = config.getServletContext();
~~~

在继承senericServlet后，直接调用`super.getServletContext()`即可



# ServletContext

**应用域对象**

1. 仅存在一个ServletContext对象，一个webapp对应一个ServletContext。（一个应用对应一个ServletContext）
2. 由服务器管理。服务器启动时创建，关闭时销毁
3. 该对象保存了web.xml文件所有信息
4. 所有Servlet对象共享唯一的ServletContext对象
5. 如果所有用户共享一份数据，且数据量少、修改少，则可以放在ServletContext下。
6. ServletContext相当于一个缓存，用户直接从缓存中读取数据，减少io操作，提升系统性能。

## 1. 配置上下文的初始化参数

~~~xml
<context-param>
	<param-name>...</param-name>
	<param-value>...</param-value>
</context-param>

<context-param>
	<param-name>...</param-name>
	<param-value>...</param-value>
</context-param>
~~~

## 2. 获取初始化参数

~~~java
getInitParameterNames();
getInitParameter(name);
~~~

## 3. 获取应用根路径

不要将应用根路径写死，而是动态获取

方法：`getContextPath()`;

## 4. 获取文件绝对路径

方法:`getRealPath(String fileName)`



## 5. 日志

日志文件放在对应由idea创建tomcat副本下的log目录。

~~~java
// catalina ----- 服务器端控制台信息
// localhost ---- ServletContext对象的log方法记录的信息
// localhost_access_log 浏览器访问记录日志

//日志方法
log(String message);
log(String message, Throwable t);
~~~

## 6. 绑定数据

~~~java
setAttribute(String name, Object obj);
getAttribute(String name);
removeAttribute(String name);
~~~





# HttpServletRequest

**请求域对象**

1. RequestFacade实现了HttpServletRequest
2. 由tomcat创建
3. 封装了请求中的全部信息
4. 每个请求对象对应每个响应对象
5. 尽量使用请求域对象，而不是应用域对象

~~~java
//获取用户提交数据
String getParameter(String name);//为了方便V数组只有一个元素。作用传入K获取V数组的第一个元素。最常用
String[] getParameterValues(String name);//传入K得到V数组。第二常用
Map<String, String[]> getParameterMap();//得到Map集合。K存放name，V存放value
Enumeration<String> getParameterNames();//得到K


//CRUD
//对专属的servlet进行crud
setAttribute(String name, Object obj);// 绑定数据
getAttribute(String name);// 获取绑定数据
removeAttribute(String name);// 解除绑定

// 获取应用域对象
getServletContext();
// 获取会话域对象
getSession(boolean);

//其他方法
getRemoteAddr();// 获取客户端/浏览器的ip地址
setCharacterEncoding();// 设置请求体的字符集，用于处理post请求的乱码问题。tomcat10不会出现乱码问题，默认是utf-8
getMethod();// 获取请求方式（get、post...）
getRequestURI();// 获取请求的uri（带项目路径）
getServletPath();// 获取servlet对象的路径（不带项目路径，即配置中的url-pattern），以/开头
~~~







# 资源跳转



## 1. 转发机制

转发机制是在服务器内进行访问，故路径是**相对路径**即可

1. 获得转发器对象：`httpServletRequest.getRequestDispatcher(String path)`;（path即注册类的url-pattern，或者是其他资源路径，只要路径正确即可）

    该步骤是告知路径供tomcat去完成资源的管理

2. 调用转发器的方法，完成转发`dispatcher.forward(httpServletRequest, httpServletResponse)`

    该步骤传入同一次的请求对象与响应对象，保证请求内容不发生改变

3. 合起来：`httpServletRequest.getRequestDispatcher(String path).forward(httpServletRequest, httpServletResponse)`

转发另一个资源的不一定是serlvet，只要是服务器中合法的资源即可

路径以`/`开始，不加项目名

> 就算转发了，最终在浏览器上访问的地址不变

## 2. 重定向

进行重定向后，实际上是返回一个uri给浏览器，而浏览器会重新对这个uri进行访问，且这次的访问将会是全新的请求

1. `response.sendRedirect(绝对路径)`或者`response.sendRedirect(request.getContextPath() + "资源路径")`



## 3. 选择

1. 当servlet中绑定了数据，该数据需要给下一个servlet使用时，那么选择转发
2. **其他情况均选择重定向。**

## 4. 注意

当使用转发时，若是对数据库操作，且未采取验证措施，那么一直刷新页面，会造成多次执行数据库的操作。

当使用重定向时，所有有关响应的操作都应在重定向之前完成。否则响应将丢失这些额外的操作。





# servlet共享数据

1. 将数据放在应用域中。（但是作用范围太大）
2. 将数据放在请求域中。在需要时利用转发机制完成共享请求域中的数据。 

在点击后，跳转时，若需要添加请求数据，则要在路径后加上`?name=value&xame=value&...`而对于此处是可以实现动态传递的。即一般name是固定不变得，只需要通过方法动态获取value值即可。








