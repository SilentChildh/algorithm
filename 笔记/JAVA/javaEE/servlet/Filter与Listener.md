# Filter

## 用处

1. 过滤脏敏感字符（绿一些敏感的字符串）；
2. 避免中文乱码（统一设置请求和响应的编码）；
3. 权限验证（规定只有带指定Session或Cookie的请求，才能访问资源）；
4. 用于实现自动登录；

## 使用步骤

1. 实现一个接口：jarkata.servlet.Filter

2. 实现这个接口当中所有的方法。

    1. init方法：在Filter对象第一次被创建之后调用，并且只调用一次。
    2. doFilter方法：只要用户发送一次请求，则执行一次。发送N次请求，则执行N次。在这个方法中编写过滤规则。
        1. 调用chain.doFilter(request, response); 完成多重过滤，并在最后执行servlet类的方法
    3. destroy方法：在Filter对象被释放/销毁之前调用，并且只调用一次。

    ~~~java
    import jakarta.servlet.*;
    import java.io.IOException;
    
    public class MyFilter implements Filter {
        @Override
        public void init(FilterConfig filterConfig) throws ServletException {
        }
        @Override
        public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
    
        }
        @Override
        public void destroy() {
        }
    }
    ~~~

    

3. 第二步：在web.xml文件中对Filter进行配置。这个配置和Servlet很像。

    1. 依靠filter-mapping标签的配置位置，越靠上优先级越高。

4. 或者使用注解：@WebFilter({"路径"})。一般不使用注解

    1. 优先级根据类名的字典序

## 关于xml配置

~~~xml
<filter-mapping>
    <filter-name>FirstFilter</filter-name>
    <!--指定拦截指定路径的资源URL-->
    <url-pattern>/aa/*</url-pattern>
</filter-mapping>
<filter-mapping>
    <filter-name>FirstFilter</filter-name>
    <!--指定拦截指定的ServletName-->
    <servlet-name>Servlet2</servlet-name>
    <!--调度程序-->
    <!--<dispatcher>REQUEST</dispatcher>-->
</filter-mapping>
~~~



1. `<filter-name/>`指定某个过滤器的名字
2. `<url-pattern/>`指定过滤器所拦截的资源路径URL，“/*”表示所有的Web资源都需要途径该过滤器，“*.xxx”表示拦截访问xxx后缀的资源的请求。
3. `<servlet-name/>`指定过滤器所拦截的某个Servlet的名字。
4. `<dispatcher/>`指定过滤器在拦截时所应用的调度模式，一共有五个可选配置：FORWARD, REQUEST, INCLUDE, ASYNC, ERROR。
    1. `FORWARD`：如果目标资源是通过RequestDispatcher的forward()方法访问的，那么该过滤器将被调用，除此之外，该过滤器不会被调用。
    2. `REQUEST`：当用户直接通过普通路径访问资源时，Web容器将会调用过滤器。如果目标资源是通过RequestDispatcher的include()或forward()方法访问时，那么该过滤器就不会被调用。这是默认的模式。
    3. `INCLUDE`：如果目标资源是通过RequestDispatcher的include()方法访问的，那么该过滤器将被调用，除此之外，该过滤器不会被调用。
    4. `ERROR`：如果目标资源是通过声明式的异常处理机制调用的，那么该过滤器将被调用。除此之外，过滤器不会被调用。
    5. `SYNC`：意味着过滤器将在从异步上下文AsyncContext的调用下应用。



## 过滤器的生命周期

1. `诞生`：过滤器的实例是在web应用被加载时就完成的实例化，并调用init方法初始化的。servlet 容器在实例化Filter后只调用`init`方法一次。在要求过滤器执行任何过滤工作之前，init 方法必须成功完成。区别于Servlet，过滤器会全部立即初始化。
2. 每个过滤器在init初始化方法都会传递一个 FilterConfig 对象，从该对象可以获取其初始化参数。并可通过FilterConfig获取 ServletContext对象，比如可以使用该对象加载筛选任务所需的资源。
3. `存活`：和应用的生命周期一致的。在内存中是单例的。针对拦截范围内的资源，每次访问都会调用void doFIlter(request,response.chain)进行拦截。
4. `死亡`：应用被卸载时，Filter将被调用，此时会调用destroy方法，该方法只会被调用一次。

## 注意：

- Servlet对象默认情况下，在服务器启动的时候是不会新建对象的。
- Filter对象默认情况下，在服务器启动的时候会新建对象。
- Servlet是单例的。Filter也是单例的。（单实例。）

## 责任链设计模式

- 核心思想：

    在程序运行阶段，动态的组合程序的调用顺序。

- 优点：
    在程序编译阶段不会确定调用顺序。因为Filter的调用顺序是配置到web.xml文件中的，只要修改web.xml配置文件中filter-mapping的顺序就可以调整Filter的执行顺序。显然Filter的执行顺序是在程序运行阶段动态组合的。那么这种设计模式被称为责任链设计模式。



# Listener

## 认识

- 在Servlet中，所有的监听器接口都是以“Listener”结尾。
- 特殊的时刻如果想执行这段代码，你需要想到使用对应的监听器。
- 当某个特殊的事件发生（特殊的事件发生其实就是某个时机到了。）之后，被web服务器自动调用。

## 使用步骤

1. 编写一个类实现ServletContextListener接口。并且实现里面的方法。

2. 配置xml或者注解（直接添加在自定义监听器类上添加@WebListener即可）

    **HttpSessionBindingListener无需配置或注解**

    ~~~xml
    <listener>
        <listener-class>com.bjpowernode.javaweb.listener.MyServletContextListener</listener-class>
    </listener>
    ~~~

## 各类监听器

### 按包分类

1. jakarta.servlet包下：
    1. ServletContextListener
    2. ServletContextAttributeListener
    3. ServletRequestListener
    4. ServletRequestAttributeListener
2. jakarta.servlet.http包下：
    1. HttpSessionListener
    2. HttpSessionAttributeListener
        1. 该监听器需要使用@WebListener注解进行标注。
        2. 该监听器监听的是什么？是session域中数据的变化。只要数据变化，则执行相应的方法。主要监测点在session域对象上。
    3. HttpSessionBindingListener
        1. 该监听器不需要使用@WebListener进行标注。
        2. 假设User类实现了该监听器，那么User对象在被放入session的时候触发bind事件，User对象从session中删除的时候，触发unbind事件。
        3. 假设Customer类没有实现该监听器，那么Customer对象放入session或者从session删除的时候，不会触发bind和unbind事件。
    4. HttpSessionIdListener
        1. session的id发生改变的时候，监听器中的唯一一个方法就会被调用。
    5. HttpSessionActivationListener
        1. 监听session对象的钝化和活化的。
        2. 钝化：session对象从内存存储到硬盘文件。
        3. 活化：从硬盘文件把session恢复到内存。

### 常用Listener

1. 应用域

    ServletContextListener。内含两个方法，分别在创建和销毁时调用。

    ServletContextAttributeListener。内含三个方法，分别在为域对象执行相关绑定数据时调用。

2. 请求域

    ServletRequestListener

    ServletRequestAttributeListener

3. 会话域

    HttpSessionListener

    HttpSessionAttributeListener

4. 特殊

    HttpSessionBindingListener。内含两个方法，用于实体类，而不是自定义的监听器类。方法调用时机是：当该实体类被绑定/被解绑会话域时，调用方法。



> 各类监听器可以从参数的事件类方法获取域对象