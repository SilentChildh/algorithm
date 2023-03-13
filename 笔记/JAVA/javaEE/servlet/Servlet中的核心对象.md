## 1. 继承关系

**自定义servlet类继承HttpServlet，HttpServlet继承了GenericServlet，GenericServlet实现了Servlet**



## 2. SerlvetConfig对象

- **每个servlet对象都有一个SerlvetConfig对象**



### 2.1 获取SerlvetConfig对象

~~~java
// 实际上调用的是GenericServlet的方法
SerlvetConfig serlvetConfig = this.getServletConfig();// 返回SerlvetConfig
~~~



### 2.2 获取局部初始化参数——init-param

~~~java
SerlvetConfig serlvetConfig = this.getServletConfig();// 先获取SerlvetConfig对象
// 实际上调用的是GenericServlet的方法
// 在GenericServlet的该方法内，实际上实现获取由tomcat创建的ServletConfig再调用对应的获取方法。
serlvetConfig.getInitParameter(String name);// 返回String 
serlvetConfig.getInitParameterNames();// 返回String[]
~~~



### 2.3 获取web.xml中的Servlet-name

~~~java
SerlvetConfig serlvetConfig = this.getServletConfig();// 先获取SerlvetConfig对象
String servletName = serlvetConfig.getServletName();// 通过SerlvetConfig对象得到对应的自定义Servlet-name的值
~~~



## 3. ServletContext对象

**应用域对象**

- 一个项目只有一个ServletRequest对象
- 所有serlvet对象共享一个ServletContext对象

### 3.1 获取ServletContext

~~~java
// 实际上调用的是GenericServlet的方法，
// 在方法内部是通过SerlvetConfig对象来得到ServletContext对象
ServletContext servletContext = this.getServletContext();
~~~

### 3.2 获取全局初始化参数context-param

~~~java
ServletContext servletContext = this.getServletContext();// 先获取servletContext对象
// 与获取局部初始化参数一样，只不过访问的对象从SerlvetConfig对象变为了ServletContext对象而已
servletContext.getInitParameterNames();// 返回String[]
servletContext.getInitParameter(name);// 返回String
~~~

### 3.3 获取应用根路径

建议写程序时不要将应用根路径写死，而是动态获取

~~~java
ServletContext servletContext = this.getServletContext();// 先获取servletContext对象
servletContext.getContextPath();// 返回String--->/项目名
~~~

### 3.4 获取文件绝对路径

~~~java
ServletContext servletContext = this.getServletContext();// 先获取servletContext对象
servletContext.getRealPath(String fileName);// 传入文件名返回String，默认返回out目录下的地址
~~~

### 3.5 日志

日志文件放在对应由idea创建tomcat副本下的log目录。

~~~java
// catalina ----- 服务器端控制台信息
// localhost ---- ServletContext对象的log方法记录的信息
// localhost_access_log 浏览器访问记录日志

//日志方法 无返回值
ServletContext servletContext = this.getServletContext();// 先获取servletContext对象
servletContext.log(String message);
servletContext.log(String message, Throwable t);
~~~

### 3.6 增加全局元素

在内存中操作

~~~java
ServletContext servletContext = this.getServletContext();// 先获取servletContext对象
servletContext.setAttribute(String name, Object obj); // 无返回值
servletContext.getAttribute(String name); // 返回Object对象
servletContext.removeAttribute(String name);// 无返回值
~~~



## 4. HttpServletRequest对象

**请求域对象**

- 每次请求产生一个ServletRequest对象
- 一个ServletRequest对象对应一个ServletResponse对象

### 4.1 获取用户请求/提交数据

~~~java
//为了方便V数组只有一个元素。作用传入K获取V数组的第一个元素。返回String 
request.getParameter(String name); // 最常用。
//传入K得到V数组。返回String[]
request.getParameterValues(String name);// 第二常用。


Map<String, String[]> getParameterMap();//得到Map集合。K存放name，V存放value
Enumeration<String> getParameterNames();//得到K
~~~

### 4.2 增加绑定数据

~~~java
request.setAttribute(String name, Object obj);// 绑定数据. 无返回值
request.getAttribute(String name);// 获取绑定数据， 返回Object
request.removeAttribute(String name);// 解除绑定， 无返回值
~~~

### 4.3 其他方法

前四种均返回String，最后一种无返回值,且tomcat10已经默认utf-8，没必要使用

~~~java
request.getRemoteAddr();// 获取客户端/浏览器的ip地址

request.getMethod();// 获取请求方式（get、post...）

request.getRequestURI();// 获取请求的uri（带项目路径）

getServletPath();// 获取servlet对象的路径（不带项目路径，即配置中的url-pattern），以/开头

request.setCharacterEncoding();// 设置请求体的字符集，用于处理post请求的乱码问题。tomcat10不会出现乱码问题，默认是utf-8
~~~

### 4.4 转发请求

~~~java
// path即在web.xml种注册的url
request.getRequestDispatcher(String path).forward(request, response);// path以/开头
~~~



## 5. Response对象

~~~java
//设置文本配置后在获取打印流
response.setContentType("text/html;charset=UTF-8");
PrintWriter out = response.getWriter();
~~~



## 6. HttpSession对象

**会话域对象**

### 6.1 创建/获取

~~~java
// 如果获取不到则新建
HttpSession session = request.getSession()
~~~

### 6.2 获取

~~~java
// 如果获取不到返回null
HttpSession session = request.getSession(false);
~~~

### 6.3 绑定数据

~~~java
// 域对象都有三个方法，用于绑定数据
setAttribute(String name, Object obj);
getAttribute(String name);
removeAttribute(String name);
~~~

### 6.4 删除

~~~java
// 销毁session对象
session.invalidate();
~~~



## 7. Cookie对象

~~~java
// 发送cookie
response.addCookie(cookie);
// 获取cookie
Cookie[] cookies = request.getCookies();// 返回数组

// 创建cookie
Cookie cookie = new Cookie(String name, String value);

// 设置cookie的有效时间
cookie.setMaxAge(int time);// 单位为sec

// 设置关联路径
cookie.setPath(路径);// 只要是该路径下的请求（包括子路径），都会发送cookie

// 获取cookie的name和value, 返回字符串
String name = cookie.getName();
String value = cookie.getValue();
~~~

### 7.1 删除

~~~java
// 1.在指定路径、域名下获取cookie
Cookie[] cookie = request.getCookies();
// 2.遍历得到指定cookie
for (Cookie cookie : cookies) {
    // 3.得到后设置有效时间为0，即删除
    if (cookie.getName().equals("...")) {
		cookie.setMaxAge(0);
        // 4.如果不想继续删除其他同名的cookie，就break退出遍历
        // break;
    }
}
~~~

### 7.2 设置

~~~java
// 1.创建cookie
Cookie cookie = new Cookie("...", "....");
// 2.设置有效时间
cookie.setMaxAge(num1 * num2 * num3);
// 3.可设置指定路径、域名，也可以默认。默认路径包括该级的上一级。
// 4.将cookie放置到response中
response.addCookie(cookie);
~~~







## 8. 常用的两个路径

~~~java
// 上下文对象获取项目路径
ServletContext servletContext = this.getServletContext();// 先获取servletContext对象
servletContext.getContextPath();// 返回String--->/项目名

// 请求对象获取资源路径
request.getServletPath();// 获取servlet对象的路径（不带项目路径，即配置中的url-pattern），以/开头
~~~



## 9. 资源跳转

~~~java
// 转发
request.getRequestDispatcher(String path).forward(request, response);

// 重定向
response.sendRedirect(绝对路径);
response.sendRedirect(request.getContextPath() + "资源路径")
~~~

## 10. 关于三个域对象

请求域可以获取其他两个域对象

~~~java
// 获取应用域对象
getServletContext();
// 获取会话域对象
getSession();
getSession(boolean);
~~~

会话域可以获取应用域对象

~~~java
// 获取应用域对象
getServletContext();
~~~



# 开发步骤

1. 描绘用户体验流程
2. 依照流程制作前端静态页面（该处html中可以利用`./`等等在本地可以获取得到的相对路径）
3. idea搭建开发环境
4. 根据前端页面，一个一个的完成功能，实现动态页面（该处已经在web上，而不是本地了，故相对地址要根据部署的情况来决定——Deployment中的Application Context）



## 制作功能步骤

从前端开始写

1. 编写html中的超链接，因为这是用户点击的按钮，即开始使用一个功能。

    1. `<a href="/项目名/url-pattern">`，此处以`/`开头 + 项目名（部署时填写的名）+ web.xml中注册的url

2. 编写web.xml文件，注册功能，即注册servlet

    ~~~xml
    <servlet>
        <servlet-name>list</servlet-name>
        <servlet-class>html.insert.html</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>list</servlet-name>
        <url-pattern>/list</url-pattern>
    </servlet-mapping>
    ~~~

    1. url-pattern以`/`开头，后面尽量见名知意（servlet-name也是要见名知意）
    2. servlet-name命名为功能名
    3. url-pattern一定要包含servlet-name

3. 编写自定义Servlet类，继承HttpServlet，重写doXxx方法

4. 在doXxx方法中实现响应的方法，如对数据库操作....

    1. 超链接中的应用根路径（即`/项目名`）可以实现动态

        ~~~java
        // 利用上下文对象
        ServletContext servletContext = this.getServletContext();
        String contextPath = servletContext.getContextPath(String fileName);
        // 然后传入contextPath即可
        ~~~

        


​    

#     登录/注册功能

1. 用户表
    1. 用户名、密码
2. 登陆页面
    1. 登录表单，用户名、密码
    2. 提交按钮，post请求
3. 编写对应的servlet完成登陆/注册业务，内部利用模板设计模式
    1. 成功，跳转指定页面
    2. 失败，跳转失败页面
4. 利用会话域保存登录状态（区分要新建和不新建的情况）
    1. 登录成功时，才创建会话对象
    2. 登陆成功后，要对会话对象绑定登录数据后，才能跳转到其他业务页面。因为会话对象是当前会话唯一的，但当前会话可能进行的业务不是唯一。如果不绑定数据，就无法形成业务验证
    3. 在每个业务前要通过会话域是否存在和会话域的绑定数据是否存在来验证状态



