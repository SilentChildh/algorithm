主要位于java.sql和javax.sql包中，后者主要针对数据源

# 连接数据库

## 方式一

硬编码连接

~~~java
//在底层，类加载时，就已经注册了驱动了
/*
            static {
            try {
                DriverManager.registerDriver(new Driver());
            }
            catch (SQLException var1) {
                throw new RuntimeException("Can't register driver!");
            }
        */
//在5.1.6版本的jar包下，在META-INF/services下有个java.sql.Driver文件。
//即自动注册驱动，于是也不用Class.froName()了
Class.forName("com.mysql.jdbc.Driver");

//可以直接进行连接
Connection connection =
    DriverManager.getConnection("jdbc:mysql://localhost/db_01", "root", "root");

System.out.println(connection);

//关闭连接
connect.close();
~~~

## 方式二

通过配置文件进行连接

~~~java
Properties properties = new Properties();
properties.load(new FileInputStream("E:/Git/3122004572-huanghehua/任务/Test/src/222.properties"));

Class.forName(properties.getProperty("driver"));

Connection connection =
           		 DriverManager.getConnection(properties.getProperty("url"), properties.getProperty("user"), properties.getProperty("password"));
        System.out.println(connection);


//关闭连接
connect.close();
~~~

## 方式三

资源绑定

~~~java
private static ResourceBundle bundle = ResourceBundle.getBundle("resources.jdbc");//源根下的包名.配置文件名
private static String url = bundle.getString("url");
private static String user = bundle.getString("user");
private static String password = bundle.getString("password");

//.....
~~~



# 对于RsultSet

ResultSet是接口，接收的实际上是JDBC42ResultSet类。

其中rowData字段，接收RowDataStatic类。

该类里有rows字段，接收ArrayList类。

其中elementDatac字段继续接收，最终可以搜到二进制数据。

~~~java
ResultSet;//接收查询结果集的引用。类似于迭代器的操作形式。
res.close();//关闭资源

//最开始光标位置可以看作是在表头，不指向记录
res.next();//向下移动光标，如果查询不到记录，则返回false
res.previous();//向上移动,同上

res.getInt/String/Date/...(int column);//返回指定列数（从1开始）的结果。
res.getInt/String/Date/...(String coulumnLabel);//返回指定列名的结果。
~~~

元信息

列数以1开始

~~~java
ResultSetMetaData metaData = resultSet.getMetaData();
int getColumnCount() throws SQLException;// 获取列数量
boolean isAutoIncrement(int column) throws SQLException;// 是否自增
String getColumnName(int column) throws SQLException;//  获取列名
String getColumnLabel(int column) throws SQLException;// 和getColumnName效果一致
~~~





# Statement

## 认识

Statement对象用于执行静态的SQL语句，并返回其生成的结果。

对数据库访问、对sql语句操作可以通过三种方式：

1. Statement。但存在SQL注入问题。
2. PreparedStatement。预处理。因无SQL注入问题而取代Statement。
3. CallableStatement。存储过程。

SQL注入问题：系统、程序未对用户输入的数据进行充分地检查，从而在输入数据的过程中，注入了非法的SQL语句或命令，恶意攻击数据库。



## Statement

### 连接数据库

~~~java
Statement statement = connection.createStatement();//获取操作的接口
statement.close();//关闭资源
~~~

### 操作sql方法

~~~java
int rows = statement.executeUpdate(String sql);//执行dml操作，返回受影响的行数
ResultSet res = statement.executeQuery(String sql);//执行select操作，返回查询结果集。
execute(String sql);//执行任意sql语句，返回boolean。
~~~

### 示例

~~~java
void create5() throws SQLException, IOException, ClassNotFoundException {

    Properties properties = new Properties();
    properties.load(new FileInputStream("E:/Git/3122004572-huanghehua/任务/Test/src/222.properties"));

    Class.forName(properties.getProperty("driver"));

    Connection connection =
        DriverManager.getConnection(properties.getProperty("url"), properties.getProperty("user"), properties.getProperty("password"));
    System.out.println(connection);

    String sql1 = "select * from t1";
    Statement statement = connection.createStatement();
    ResultSet res = statement.executeQuery(sql1);

    while(res.next()) {
        System.out.println(res.getInt(1) +  "\t"+ res.getInt(2) +
                           "\t" + res.getInt(3) + "\t"  + res.getString(4));
    }

    res.close();
    statement.close();
    connection.close();
}
~~~



## PreparedStatement

是Statement的子接口。

### 连接数据库

~~~java
PreparedSatement pre = connection.prepareStatement(String sql);//需要填入sql语句
pre.close();//关闭资源
~~~



### 新增方法

~~~java
setXXXX(int index, XXXX value);//给第index个（从1开始算）占位符（？）设置指定值。
~~~



### 操作SQL方法

~~~java
//无需参数
pre.executeUpdate();
pre.executeQuery();
pre.execute(String sql);

    
//若填入参数，实际上调用的是父类的操作方法，此时SQL语句不可以有占位符`?`
~~~



### 示例

~~~java
void testPreStament() throws ClassNotFoundException, IOException, SQLException {
    Properties properties = new Properties();
    properties.load(new FileInputStream("E:/Git/3122004572-huanghehua/任务/Test/src/222.properties"));

    Class.forName(properties.getProperty("driver"));

    Connection connection =
        DriverManager.getConnection(properties.getProperty("url"), properties.getProperty("user"), properties.getProperty("password"));
    System.out.println(connection);

    String sql = "select * from t1 where math = ? OR PE = ?";
    PreparedStatement pre = connection.prepareStatement(sql);

    pre.setInt(1, 10);
    pre.setInt(2, 20);

    //受影响行数
    //System.out.println(pre.executeUpdate());


    ResultSet res = pre.executeQuery();

    while(res.next()) {
        System.out.println(res.getInt(1) +  "\t"+ res.getInt(2) +
                           "\t" + res.getInt(3) + "\t"  + res.getString(4));
    }

}
~~~



### 好处

1. 利用占位符`?`，从而避免字符串`+`的拼接，进而减少语法错误，且清晰明了。
2. 减少SQL注入问题
3. 减少编译次数，效率较高。





# jdbcutils

~~~java
package com.jdbc_.jdbcutils;

import java.io.FileInputStream;
import java.io.IOException;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;


/**
 * 用于JDBC与数据库的连接与关闭
 * @author silent_child
 * @version 1.0
 **/

public class Connection {
    private static String url;
    private static String user;
    private static String password;
    //private static String filePath = "E:\\Git\\3122004572-huanghehua\\任务\\Test\\src\\222.properties";

    static {
        Properties p = new Properties();

        try {
            p.load(new FileInputStream("E:\\Git\\3122004572-huanghehua\\任务\\Test\\src\\222.properties"));
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
        url = p.getProperty("url");
        user = p.getProperty("user");
        password = p.getProperty("password");

    }

    /**
     * 返回数据库的连接
     * @return java.sql.Connection - java规范的连接接口
     * @thorws RuntimeException - 提示数据库连接出错。后续可进行处理，也可以默认程序关闭失败。
     **/
    
    public static java.sql.Connection getConnection() {

        try {
            return DriverManager.getConnection(url, user, password);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }

    /**
     * 用于关闭所有可能已开启的资源
     * @param resSet 用于接收ResultSet对象;
     * @param statement 用于接收Statement对象;
     * @param connection 用于接收java.sql.Connection对象
     * @thorws RuntimeException - 提示数据库访问出错。后续可进行处理，也可以默认程序关闭失败。
     **/

    public static void close(ResultSet resSet, Statement statement, java.sql.Connection connection) {
        try {
            if(resSet != null) {
                resSet.close();
            }
            if(statement != null) {
                statement.close();
            }
            if(connection != null) {
                connection.close();
            }
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }

}
~~~



~~~java
package com.jdbc_.jdbcutils;

import org.junit.jupiter.api.Test;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class Use {
    @Test
    public void DML() {
        Connection connection = null;
        PreparedStatement pre = null;



        String sql = "insert into t1 values (?, ?, ?, ?)";
        try {
            connection = com.jdbc_.jdbcutils.Connection.getConnection();
            pre = connection.prepareStatement(sql);
            pre.setInt(1, 2);
            pre.setInt(2, 100);
            pre.setInt(3, 100);
            pre.setString(4, "黄诃华");
            pre.executeUpdate();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
        finally {
            com.jdbc_.jdbcutils.Connection.close(null, pre, connection);
        }
    }

    @Test
    public void DQL() {
        Connection connection = null;
        PreparedStatement pre = null;
        ResultSet res = null;

        String sql = "select * from t1";
        connection = com.jdbc_.jdbcutils.Connection.getConnection();
        try {
            pre = connection.prepareStatement(sql);
            res = pre.executeQuery();

            while(res.next()) {
                System.out.println(res.getInt("id") + "\t" +
                        res.getInt("math") + "\t" +
                        res.getInt("PE") + "\t" +
                        res.getString("name"));
            }
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
        finally {
            com.jdbc_.jdbcutils.Connection.close(res, pre, connection);
        }
    }
}
~~~



# 事务

在获取到`Connection`对象后，每次操作一此sql语句，自动commit。

利用`setAutoCommit(boolean)`来取消自动提交。

在执行完一组sql语句后，可以通过Connection对象的`commit()`方法来提交。

需要回滚时，可以通过Connection对象的`rollback()`方法来回滚。



常见使用：

~~~java
try{
    connection.setAutoCommit(flase);//开启事务
    //...
    
    //...
    
    connection.commit();//若无异常提交事务
}
catch(Exception e) {
    //...
    try{
        connection.rollbakc();//进行回滚
    }
    catch(Exception e) {
        throw new RuntimeException(e);
    }
    
    throw new RuntimeException(e);//抛出异常
}
finally{
    ...close();//关闭资源
}
~~~



# 批处理

## 配置

在url中，需要添加`rewriteBatchedStatements=true`

## 方法

~~~java
pre.addBatch();//批量添加记录到集合中
pre.executeBatch();//批量执行集合中的sql语句
pre.clearBatch();//清空集合中的记录


~~~





# 数据库连接池

传统连接问题：

1. 多次连接执行，会占用资源。
2. 可能会出现内存泄露、程序崩溃



## 认识

1. 缓冲机制。连接池内存放已经连接数据库的一些对象（即已通过验证，即拿即用）。
2. java程序可以在连接池尚有可用连接时进行获取，即java与连接池获得连接。
3. 使用完则解除java与连接池的联系。即连接池中与数据库的连接是可复用的。
4. 当连接池可用连接不足，但java请求获得连接时，java会进入等待队列。

javax.sql.DataSource是java提供的规范接口

## C3P0



### c3p0-config.xml简易配置

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<c3p0-config>
  <!--配置连接池1-->
  <named-config name="db_01">           <!--数据源名称(数据库)代表连接池-->

    <property name="driverClass">com.mysql.jdbc.Driver</property>    <!--驱动类-->

    <property name="jdbcUrl">jdbc:mysql://localhost:3306/mysql?rewriteBatchStatement=true</property>  <!--url-->

    <property name="user">root</property>            <!--用户名-->

    <property name="password">root</property>      <!--密码-->

    <property name="initialPoolSize">10</property>  <!--初始化的连接数-->

    <property name="acquireIncrement">5</property>  <!--每次增长的连接数-->


    <property name="maxPoolSize">100</property>  <!--最大的连接数-->

    <property name="minPoolSize">10</property>   <!--最小的连接数-->

    <property name="maxStatements">5</property>   <!--最多存在可执行操作数量-->

    <property name="maxStatementsPerConnection">2</property>  <!--每个连接最多同时进行两个操作-->

  </named-config>

  <!--配置连接池2,可以配置多个数据库-->
  <!--named-config name="db_01"-->

  <!--   -->

  <!--named-config-->

</c3p0-config>

~~~

### 连接方式

~~~java
void test1() throws SQLException {
    DataSource c = new ComboPooledDataSource("db_01");//填入连接池的名，获得连接池对象
    Connection connection = null;

    for(int i = 0; i<= 1100; i++) {
        connection = c.getConnection();//核心方法getConnection();//以声明在javax提供的DataSource接口下
        connection.close();
    }
}
~~~

## Druid

### druid.properties建议配置

~~~properties
driverClassName=com.mysql.jdbc.Driver

#URL连接数据库的URL，其中travel（以下面例子来说）为连接的数据库，后面的参数可不改但不删
url=jdbc:mysql://localhost:3306/mysql?rewriteBatchStatement=true

characterEncoding=utf-8

#安装mysql时候设置的用户与密码
username=root
password=root

#初始化物理连接的个数
initialSize=10

#空闲时，小连接数
minIdle=5

#最大连接池数量
maxActive=20

#获取连接时最大等待时间
maxWait=5000


#用来检测连接是否有效的sql
validationQuery=SELECT 1

#保证安全性！
testWhileIdle=true
~~~

### 连接方式

~~~java
void testDruid() throws Exception {
    Properties p = new Properties();

    p.load(new FileInputStream("E:\\Git\\3122004572-huanghehua\\任务\\Test\\src\\druid.properties"));

    DataSource dataSource = DruidDataSourceFactory.createDataSource(p);//获得连接池对象

    Connection connection = dataSource.getConnection();//核心
    connection.close();
}
~~~

### 部分封装

~~~java
package com.jdbc_.druidutils;

import com.alibaba.druid.pool.DruidDataSourceFactory;

import javax.sql.DataSource;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

public class DruidUtil {
    private static DataSource dataSource = null;
    static {
        Properties p = new Properties();
        try {
            p.load(new FileInputStream("E:\\Git\\3122004572-huanghehua\\任务\\Test\\src\\druid.properties"));
            dataSource = DruidDataSourceFactory.createDataSource(p);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    public static Connection getConnection() throws SQLException {
        return dataSource.getConnection();
    }

    public void close(ResultSet resSet, Statement statement, Connection connection) {
        try {
            if(resSet != null) {
                resSet.close();
            }

            if (statement != null) {
                statement.close();
            }
            if(connection != null) {
                connection.close();
            }
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
~~~





# ResultSet To ArrayList<?>

~~~java
ArrayList<?> testSelectToArrayList() {
    Connection connection = null;
    PreparedStatement preparedStatement = null;
    ResultSet resultSet = null;
    List<com.jdbc_.testdruid.Test> list = new ArrayList();

    String sql = "select * from test";

    try {
        connection = DruidUtil.getConnection();
        preparedStatement = connection.prepareStatement(sql);

        resultSet = preparedStatement.executeQuery();

        while(resultSet.next()) {

            list.add(new com.jdbc_.testdruid.Test
                     (
                         resultSet.getInt("id"), resultSet.getString("name")
                     )
                    );
        }

    } catch (SQLException e) {
        throw new RuntimeException(e);
    } finally {
        DruidUtil.close(resultSet, preparedStatement, connection);
    }

    return (ArrayList<?>) list;
}
~~~



# Apache_Dbutils

## 查询

~~~java
void test() throws SQLException {
    //获取连接
    Connection connection = DruidUtil.getConnection();

    //创建查询对象
    QueryRunner queryRunner = new QueryRunner();


    String sql = "select * from test where id < ?";

    //QueryRunner类中存在query方法。
    //参数1：传入连接
    //参数2：传入查询语句
    //参数3：传入一个BeanListHandler，该List实现类通过反射获取到指定对象的属性，进而将表内的数据，填充到数据结构中
    //参数4：可变参数，是传递给占位符`?`的值
    //该语句底层自动关闭ResultSet 和 PreparedStatement
    //最终返回ArrayList
    List<com.jdbc_.testdruid.Test> list =
        queryRunner.query(connection, sql,
                          new BeanListHandler<>(com.jdbc_.testdruid.Test.class), 50);

    for(com.jdbc_.testdruid.Test test: list){
        System.out.println(test);
    }
    DruidUtil.close(null, null, connection);
}
~~~

## 查询单行



~~~java
Object obj = queryRunner.query(connection, sql,
                        new BeanHandler<>(Object.class), 50);
~~~



## 查询单行单列



~~~java
Object obj = queryRunner.query(connection, sql,
                        new ScalarHandler(), 10);
~~~



## DML

~~~java
public void testDML() throws SQLException {
    //获取连接
    Connection connection = DruidUtil.getConnection();

    //创建查询对象
    QueryRunner queryRunner = new QueryRunner();
    
    //4.这里组织sql完成update、insert、delete
    String sql = "update actor set name = ? where id = ?";
    //（1）执行dml 操作是 queryRunner.update
    //（2）返回的值是受影响的行数
    int rows = queryRunner.update(connection, sql, "张三丰", 1);
    
    System.out.println(affectedRow > 0?"执行成功":"执行没有影响到表");
    
    //释放资源
    DruidUtil.close(null, null, connection);
}
~~~

