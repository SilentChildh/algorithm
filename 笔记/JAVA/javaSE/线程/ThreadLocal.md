

### 2.1 认识

本质上就是一个Map集合，K是当前线程，V数据类型是泛型，具体值可以自行传入。**即为线程提供一个线程私有的变量副本**

> **ThreadLocal提供的只是一个浅拷贝，如果变量是一个引用类型，那么就要考虑它内部的状态是否会被改变，想要解决这个问题可以通过重写ThreadLocal的initialValue()函数来自己实现深拷贝**

### 2.2 作用

对当前线程绑定数据（当前线程共享数据）。

若V不是集合，那么一个线程只能绑定一个数据；

反之，可以将绑定的数据都放到集合中，使得可以绑定多个数据

### 2.3 方法

~~~java
// 先创建ThreadLocal
ThreadLocal<...> threadLocal = new ThreadLocal<>();

threadLocal.set(T value);// 对当前线程绑定一个value数据
... t = get();// 获取当前线程的绑定数据
threadLocal.remove();// 删除当前线程的绑定数据

// 创建一个线程局部变量， 其初始值通过调用给定的 supplier 生成
static <S> ThreadLocal <S> withlnitial(Supplier< ? extends S>supplier);
~~~

> `withInitial` 方法只在第一次调用 `get` 方法时有用，它会返回一个初始值。
>
> 如果你在调用 `get` 方法之前使用了 `set` 方法设置了一个值，那么 `get` 方法将返回你设置的值，而不是通过 `withInitial` 方法提供的初始值

### 2.4 示例

用于JdbcUtils

~~~java
// 创建
private static ThreadLocal<Connection> threadLocal = new ThreadLocal<>();

public static Connection getConnection() {
    Connection connection = null;
    try {
        if (threadLocal.get() == null) {
            threadLocal.set(DriverManager.getConnection(url, user, password));
        }
    } catch (SQLException e) {
        throw new RuntimeException(e);
    }
    return threadLocal.get();
}

public static void close(ResultSet res, Statement statement, Connection connection) {
    try {
        if(res != null) {
            res.close();
        }
        if(statement != null) {
            statement.close();
        }
        if(connection != null) {
            // 在线程池中，多个线程存在重复使用的情况，故线程销毁时，一定要将TreadLocal里的值remove掉。
            if (threadLocal.get() != null) {
                threadLocal.remove();
            }
            connection.close();
        }
    } catch (SQLException e) {
        throw new RuntimeException(e);
    }
}
~~~

### 对比锁

1. 首先，它们的应用场景与实现思路就不一样，**锁更强调的是如何同步多个线程去正确地共享一个变量**
2. **ThreadLocal则是为了解决同一个变量如何不被多个线程共享**。
3. 从性能开销的角度上来讲，如果锁机制是用时间换空间的话，那么ThreadLocal就是用空间换时间。

### 设计的思想

1. 有一种普遍的方法是通过一个全局的线程安全的Map来存储各个线程的变量副本，
2. 但是这种做法已经完全违背了ThreadLocal的本意，设计ThreadLocal的初衷就是为了避免多个线程去并发访问同一个对象，尽管它是线程安全的。

1. 而在每个Thread中存放与它关联的ThreadLocalMap是完全符合ThreadLocal的思想的，
2. 当想要对线程局部变量进行操作时，只需要把Thread作为key来获得Thread中的ThreadLocalMap即可。
3. 这种设计相比采用一个全局Map的方法会多占用很多内存空间，
4. 但也因此不需要额外的采取锁等线程同步方法而节省了时间上的消耗