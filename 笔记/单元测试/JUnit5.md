# JUnit5

博客：[csdn](https://blog.csdn.net/weixin_44170221/article/details/106463482?ops_request_misc=&request_id=&biz_id=102&utm_term=junit%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-3-106463482.142^v71^js_top,201^v4^add_ask&spm=1018.2226.3001.4187)   [掘金](https://juejin.cn/post/6844903943948992520#heading-3)

## JUnit框架

**JUnit 5 = JUnit Platform + JUnit Jupiter + JUnit Vintage**

- **JUnit Platform**： 用于JVM上启动测试框架的基础服务，提供命令行，IDE和构建工具等方式执行测试的支持。

- **JUnit Jupiter**：包含 JUnit 5 新的编程模型和扩展模型，主要就是用于编写测试代码和扩展代码。

- **JUnit Vintage**：用于在JUnit 5 中兼容运行 JUnit3.x 和 JUnit4.x 的测试用例。



![](https://upload-images.jianshu.io/upload_images/15975224-9e2a07ac2438ac54.jpeg?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

## 常见用法

| 注解                                     | 作用                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| **@DisplayName("")**                     | 定义一个测试类并指定用例在测试报告中的展示名称。使用在类（称为测试类）上和方法（称为测试方法）上。 |
| **@BeforeAll **和 **@AfterAll**          | 定义了整个测试类在开始前以及结束时的操作。只能修饰静态方法。用于在测试过程中所需要的全局数据和外部资源的初始化和清理。 |
| **@BeforeEach** 和 **@AfterEach**        | 修饰普通方法。在每个测试用例方法开始前和结束时执行。主要是负责该测试方法所需要的运行环境的准备和销毁。 |
|                                          |                                                              |
| @Disabled                                | 禁用执行测试（方法不会执行，但会有单独的标注信息打印）。可用于类和方法。一般对类使用时，是在对多个测试类组合测试的时候。 |
| @Nested                                  | 按内部类的形式对测试类进行逻辑分组。嵌套的类也可以用@DisplayName 标记。 |
| @RepeatedTest(value = NUMBER, name = "") | 重复性测试。value 代表执行次数，name代表每次在测试报告的名称。 |
| @Test                                    |                                                              |
|                                          |                                                              |

| 断言                                                       | 作用                                           |
| ---------------------------------------------------------- | ---------------------------------------------- |
| assertTimeoutPreemptively()                                | 超时操作的测试，到达指定时间，立即停止         |
| assertTimeout()                                            | 调用函数一定会执行完毕                         |
|                                                            |                                                |
| assertThrows                                               | 异常测试,是异常及其子类都可                    |
| assertThrowsExactly                                        | 异常必须完全一致                               |
| assertDoesNotThrow                                         | 不抛出异常                                     |
|                                                            |                                                |
| assertNull(Object object)                                  | 对象是否为空                                   |
| assertNotNull(Object object)                               | 对象是否不为空                                 |
|                                                            |                                                |
| assertEquals(long expected, long actual)                   | long类型的值是否相等                           |
| assertEquals(double expected, double actual, double delta) | 指定精度的double值是否相等                     |
|                                                            |                                                |
| assertFalse(boolean condition)                             | 条件是否为假                                   |
| assertTrue(boolean condition)                              | 条件是否为真                                   |
|                                                            |                                                |
| assertSame(Object expected, Object actual)                 | 两个对象引用是否引用同一对象（即对象是否相等） |
| assertNotSame(Object unexpected, Object actual)            | 两个对象引用是否不引用统一对象(即对象不等)     |
|                                                            |                                                |

> assertTimeoutPreemptively() 与 assertTimeout() 的区别在于，一旦超时，在assertTimeoutPreemptively() 中被调用的方法会被立刻终止，并返回失败；但是 assertTimeout() 中调用的函数会执行完毕，再返回失败。
>
> 可以使用 `java.time.Duration` 中的 `Duration.ofXxxx()` 方法进行时间的控制。
>
> 也可以搭配Duration.of()用`java.time.temporal`中的ChronoUnit枚举类中常量来控制时间
>
> 可执行方法同样使用 Lambda 表达式



> 关于assertThrowsExactly和assertThrows，如果没有引发异常，或者引发了其他类型的异常，则此方法将失败。
