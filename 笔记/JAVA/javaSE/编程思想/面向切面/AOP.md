# 横切逻辑代码



![横切逻辑代码](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/28/1725a6b2d5a2c5ab~tplv-t2oaga2asx-zoom-in-crop-mark:4536:0:0:0.awebp)

## 1. 存在的问题

1. 代码重复问题
2. **横切逻辑代码**和**业务代码**混杂在一起，代码臃肿，不变维护





## 2. **解决问题**

AOP 另辟蹊径，提出横向抽取机制，将**横切逻辑代码和业务逻辑代码分离**

> 代码拆分比较容易，难的是如何在不改变原有业务逻辑的情况下，悄无声息的将横向逻辑代码应用到原有的业务逻辑中，达到和原来一样的效果。





# AOP

## 1. 面向切面编程

**切** ：

1. 指的是横切逻辑，
2. **原有业务逻辑代码不动**，
3. **只能操作横切逻辑代码**，
4. 所以面向横切逻辑

**面** ：

1. 横切逻辑代码往往要**影响的是很多个方法**，
2. 每个方法如同一个点，多个点构成一个面。
3. 这里有一个面的概念





## 2. 解决问题

1. 在不改变原有业务逻辑的情况下，添加横切逻辑代码，
2. 根本上解耦合，即业务方法体内无需添加横切逻辑
3. 避免横切逻辑代码重复，一种横切逻辑代码只用写一次。



## 3. OOP与AOP

### 3.1 OOP问题

1. 对于业务代码来说，当有重复代码出现时，可以就将其封装出来然后复用。

2. 我们通过分层、分包、分类来规划不同的逻辑和职责，就像之前讲解的三层架构。

3. 但这里的复用的都是**核心业务逻辑**，并不能复用一些**辅助逻辑，即横切逻辑。**

4. 比如：日志记录、性能统计、安全校验、事务管理，等等。这些边缘逻辑往往贯穿你整个核心业务，传统 OOP 很难将其封装。

    ~~~java
    public class UserServiceImpl implements UserService {
        @Override
        public void doService() {
            System.out.println("---安全校验---");
            System.out.println("---性能统计 Start---");
            System.out.println("---日志打印 Start---");
            System.out.println("---事务管理 Start---");
    
            System.out.println("业务逻辑");
    
            System.out.println("---事务管理 End---");
            System.out.println("---日志打印 End---");
            System.out.println("---性能统计 End---");
        }
    }
    ~~~



### 3.2 AOP解决

1. OOP 是至上而下的编程方式，犹如一个树状图，A调用B、B调用C，或者A继承B、B继承C。
2. 这种方式对于业务逻辑来说是合适的，通过调用或继承以复用。
3. 而辅助逻辑就像一把闸刀横向贯穿所有方法（故也称横切逻辑），如：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2a5e5843fb8540d99917ad03237b90c7~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

> 每一层都会执行相同的辅助逻辑，所以大家将这些辅助逻辑称为层面或者切面。



### 3.3 总结

1. AOP 不是 OOP 的对立面，它是对 OOP 的一种补充。
2. OOP 是纵向的，AOP 是横向的，两者相结合方能构建出良好的程序结构。
3. AOP 技术，**让我们能够不修改原有代码，便能让切面逻辑在所有业务逻辑中生效**。





## 4. 横切实现

1. 代理模式用来增加或增强原有功能再适合不过了，但切面逻辑的难点不是**不修改原有业务**，而是**对所有业务生效**。
2. 对一个业务类增强就得新建一个代理类，对所有业务增强，每个类都要新建代理类，这无疑是一场灾难。
3. 而且这里只是演示了一个日志打印的切面逻辑，如果我再加一个性能统计切面，就得新建一个切面代理类来代理日志打印的代理类，一旦切面多起来这个代理类嵌套就会非常深。



1. 面向切面编程（Aspect-oriented programming，缩写为 AOP）正是为了解决这一问题而诞生的技术。

2. 使用注解+反射完成横切

    ~~~java
    @Aspect // 声明一个切面
    @Component
    public class MyAspect {
        // 原业务方法执行前
        @Before("execution(public void com.rudecrab.test.service.*.doService())")
        public void methodBefore() {
            System.out.println("===AspectJ 方法执行前===");
        }
    
        // 原业务方法执行后
        @AfterReturning("execution(* com.rudecrab.test.service..doService(..))")
        public void methodAddAfterReturning() {
            System.out.println("===AspectJ 方法执行后===");
        }
    }
    ~~~

