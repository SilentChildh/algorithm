

# 什么是 IoC

IoC （Inversion of control ）控制反转/反转控制。它是一种思想不是一个技术实现。描述的是：Java 开发领域对象的创建以及管理的问题。

例如：现有类 A 依赖于类 B

- **传统的开发方式** ：往往是在类 A 中手动通过 new 关键字来 new 一个 B 的对象出来
- **使用 IoC 思想的开发方式** ：不通过 new 关键字来创建对象，而是通过 IoC 容器(Spring 框架) 来帮助我们实例化对象。我们需要哪个对象，直接从 IoC 容器里面获取即可。

从以上两种开发方式的对比来看：我们 “丧失了一个权力” (创建、管理对象的权力)，从而也得到了一个好处（不用再考虑对象的创建、管理等一系列的事情）



>这个设计思想就是 **将原本在程序中手动创建对象的控制权，交由IoC 容器来管理。**
>
> IoC 在其他语言中也有应用，并非 Spring 特有。
>
>**IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个 Map（key，value）,Map 中存放的是各种对象。**
>
>IoC 最常见以及最合理的实现方式叫做依赖注入（Dependency Injection，简称 DI）。

# 为什么叫控制反转

**控制** ：指的是对象创建（实例化、管理）的权力

**反转** ：控制权交给外部环境（Spring 框架、IoC 容器）

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/28/1725a71315d1da13~tplv-t2oaga2asx-zoom-in-crop-mark:4536:0:0:0.awebp)



# IOC

## 传统问题

- 创建了许多重复对象，造成大量资源浪费；
- 更换实现类需要改动多个地方；
- 创建和配置组件工作繁杂，给组件调用方带来极大不便。

> 这些问题的出现都是同一个原因：**组件的调用方参与了组件的创建和配置工作**。
>
> 其实调用方只需关注组件如何调用，至于这个组件如何创建和配置又与调用方有什么关系呢？
>
> - 就好比我去餐馆只需点菜，饭菜并不需要我亲自去做，餐馆自然会做好给我送过来。
> - 如果我们编码时，有一个「东西」能帮助我们创建和配置好那些组件，我们只负责调用该多好。这个「东西」就是容器。



## 解决问题

IoC 的思想就是两方之间不互相依赖，由第三方容器来管理相关资源。这样有什么好处呢？

1. 对象之间的耦合度或者说依赖程度降低；
2. 资源变的容易管理，对象交给容器后默认是单例的；比如你用 Spring 容器提供的话很容易就可以实现一个单例。同时避免资源浪费问题。
3. 若要更换实现类，只需更改 Bean 的声明配置，即可达到无感知更换：

> 现在组件的使用和组件的创建与配置完全分离开来。调用方只需调用组件而无需关心其他工作，这极大提高了我们的开发效率，也让整个应用充满了灵活性、扩展性。



## IoC容器

Tomcat 是 Servlet 容器，只负责管理 Servlet。我们平常**使用的组件则需要另一种容器来管理**，这种容器我们称之为 **IoC 容器**。

有了 IoC 容器，我们可以将对象交由容器管理，交由容器管理后的对象称之为 Bean。

调用方不再负责组件的创建，要使用组件时直接获取 Bean 即可：

~~~java
@Component
public class UserServiceImpl implements UserService{
    @Autowired // 获取 Bean
    private UserDao userDao;
}
~~~

调用方只需按照约定声明依赖项，所需要的 Bean 就自动配置完毕了。

就好像在调用方外部注入了一个依赖项给调用方使用，所以这种方式称之为 依赖注入（Dependency Injection，缩写为 DI）。

> **控制反转和依赖注入是一体两面，都是同一种开发模式的表现形式**。IoC 最常见以及最合理的实现方式叫做依赖注入（Dependency Injection，简称 DI）。
>
> **IoC 容器实际上就是个 Map（key，value）,Map 中存放的是各种对象。**





# 线程问题

[掘金](https://juejin.cn/post/6844903509037416455)

交由Spring管理的大多数对象其实都是一些**无状态的对象**，

这种不会因为多线程而导致状态被破坏的对象很适合Spring的默认scope，

> **每个单例的无状态对象都是线程安全的（也可以说只要是无状态的对象，不管单例多例都是线程安全的，不过单例毕竟节省了不断创建对象与GC的开销）。**
>
> **无状态的对象即是自身没有状态的对象，自然也就不会因为多个线程的交替调度而破坏自身状态导致线程安全问题**
>
> 无状态对象包括我们经常使用的DO、DTO、VO这些只作为数据的实体模型的贫血对象，
>
> 还有Service、DAO和Controller，这些对象并没有自己的状态，它们只是用来执行某些操作的。
>
> 例如，每个DAO提供的函数都只是对数据库的CRUD，而且每个数据库Connection都作为函数的局部变量（局部变量是在用户栈中的，而且用户栈本身就是线程私有的内存区域，所以不存在线程安全问题），用完即关（或交还给连接池）。
>
> 

**Spring根本就没有对bean的多线程安全问题做出任何保证与措施**。

对于每个bean的线程安全问题，根本原因是每个bean自身的设计。**不要在bean中声明任何有状态的实例变量或类变量，**

**如果必须如此，那么就使用ThreadLocal把变量变为线程私有的，**

**如果bean的实例变量或类变量需要在多个线程之间共享，那么就只能使用synchronized、lock、CAS等这些实现线程同步的方法了。**



# 实现IoC

1. 需要创建对应的接口
2. 接口的实现类
3. 需要工厂类

## 基于Xml

### 创建javabean对象

1. id表示命名
2. class表示类路径



### 注入javabean的属性

1. 使用set方法。在xml文件中使用propertiy标签name、value属性
2. 使用构造器。在xml文件中使用constructor-arg标签和name、value属性

需要注意的地方：

1. 注入的字面量
    1. 注入的属性是null
    2. 包含特殊符号（CDATA）
2. 注入其他javabean
    1. property标签内的属性是name和ref，ref指向另一个bean的id
    2. 也可以直接在property标签下创建子标签，即在编写一个bean标签

