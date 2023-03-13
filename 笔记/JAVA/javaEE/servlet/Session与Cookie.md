# B/S结构的会话（session）机制

## 会话的概念

从打开浏览器到关闭浏览器，为一次会话。

## 会话作用

session主要作用就是在服务器保存会话状态

Http协议是无状态协议：请求时BS连接，请求结束BS断开。（该协议保证节省资源，降低服务器压力）



## 会话对象（会话域）

对于每个会话，服务器都可以获取得到，在java中为session对象，对应类为HttpSession，位于jakarta.servlet.http.HttpSession。属于servlet规范

1. 一个会话一个session对象
2. session对象是在服务器端创建的
    1. 访问不同的服务器则创建不同的session对象
3. 一次会话可以存在多次请求

## 会话对象的生命周期

1. 存在超时机制，在指定时间内没有发送请求，则销毁会话对象
2. 手动销毁（安全）

## 会话对象方法

~~~java
// 如果获取不到则新建
HttpSession session = request.getSession();

// 如果获取不到返回null
HttpSession session = request.getSession(false);

// 域对象都有三个方法，用于绑定数据
setAttribute(String name, Object obj);
getAttribute(String name);
removeAttribute(String name);

// 销毁session对象
session.invalidate();

// 获取应用域对象
getServletContext();
~~~

## 实现原理

在服务器中，存在Map集合，其中保存了BS中会话--session之间的关系。K保存会话id，V保存session对象。

1. 在用户首次发送请求时，服务器会创建session对象，并标记有JSESSIONID，将这对键值对放入Map中
2. 同时在响应时，将JSESSIONID=Key打包为Cookie返回给浏览器。
3. 浏览器会将cookie保存在浏览器的缓存中。（故当关闭浏览器，缓存释放，下次打开浏览器发送请求就找不到session对象了）
4. 接下来再次发送请求时，会自动将缓存中Cookie发送给服务器，服务器会再通过该数据查找到对应的session对象



# Cookie

> Cookie禁用，是浏览器拒收服务器传来的Cookie。
>
> 但是浏览器还是可以通过其他方法访问到该对象（url重写机制）：在url后加上(;JSESSIONID=xxxxxxxxxxx...)即可。
>
> 但是该机制开发成本大，故网站一般都不允许该方法。（禁用Cookie后网页都无法正确打开）

## 保存方式

1. 浏览器内存中
2. 硬盘文件中

## 用处

1. 在浏览器保存会话状态

## 关于免登录

1. 浏览器会保存cookie，cookie中保存了用户名和密码等信息，并将cookie保存在硬盘文件中
2. 再次访问时，浏览器会自动关联cookie给服务器
3. 服务器再从cookie中获取信息验证

失效：

1. 规定时间后自动销毁

2. 手动销毁

3. 改密码

     

## 组成

http规定：name=value;，name和value都是字符串

每一个cookie都关联一个路径，保证对应到正确的服务器

当浏览器发送请求时，会自动的携带该路径下的cookie传给服务器。

## java中的Cookie类

位于jakarta.servlet.http.Cookie

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

## 关于cookie的有效时间

1. 不设置有效时间，默认保存在内存中。
2. 当时间大于0，则cookie保存在文件中
3. 时间等于0，则删除cookie。（多应用于删除浏览器上同名的cookie）
4. 时间小于0，则令cookie不被浏览器存储。意思是cookie保存在内存中，而不是文件中。（相当于不设置cookie的有效时间）

## 关于cookie的关联路径

1. 默认情况下，在路径的上一级路径及其子路径下，发送请求，都会发送cookie。如`http://localhost:8080/myapp/123`，那么在`/myapp`下的路径请求时，都会发送cookie
2. 设置路径后，该路径及其子路径，发送请求都会带上cookie。如`http://localhost:8080/myapp`，那么在`/myapp`下的路径请求时，都会发送cookie

## 同一Cookie认定

name、domain、path这三个参数的值都相同，才属于同一cookie；
name、domain、path这三个参数只要有一个的值不同，都不属于同一cookie；

## Cookie的删除

cookie机制没有提供删除cookie的方法，因此通过设置该cookie即时失效实现删除。
max-age为0，或者Expires为一个已过期时间，则表示删除该cookie。

