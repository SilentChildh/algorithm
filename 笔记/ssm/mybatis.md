# MyBatis认识

## JDBC不足：

1. SQL语句写死在Java程序中，不灵活。改SQL的话就要改Java代码。违背开闭原则OCP。
2. 手动将数据库记录封装成实体类是繁琐的，需要许多的setXxxx()。
3. 手动将实体类的各字段赋值给sql语句中的占位符`?`进行查询是繁琐的，同样需要许多的setXxxx()。

## mybatis的特点

支持定制化 SQL、存储过程、基本映射以及高级映射

1. 避免了几乎所有的 JDBC 代码中手动设置参数（即无需手动将记录转为实体类）以及获取结果集（即无需给`?`传参）
2. 支持XML开发，也支持注解式开发。【为了保证sql语句的灵活，所以mybatis大部分是采用XML方式开发。】
3. 提供了XML标签，支持动态SQL的编写。
4. 将接口和 Java 的pojo类映射成数据库中的记录
5. 体积小好学：两个jar包，两种XML配置文件。
6. 完全做到sql解耦合。
7. 提供了基本映射标签。提供了高级映射标签。

# 早期mybatis

## mybatis的简易使用步骤

### 基础步骤

在maven工程下

1. pom.xml中打包方式为jar，不需要war，因为myabatis封装的是jdbc

2. pom.xml中引入依赖mybatis和mysql依赖

    ~~~xml
    <!--mybatis核心依赖-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.10</version>
    </dependency>
    <!--mysql驱动依赖-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.30</version>
    </dependency>
    ~~~

    

3. 在resources目录下创建唯一的mybatis-config.xml核心配置文件

    1. 文件名字不是固定的
    2. 核心配置文件存放的位置也可以随意。这里选择放在resources根下，相当于放到了类的根路径下。

    ~~~xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration
            PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>
        <environments default="development">
            <environment id="development">
                <transactionManager type="JDBC"/>
                <dataSource type="POOLED">
                    <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                    <property name="url" value="jdbc:mysql://localhost:3306/powernode"/>
                    <property name="username" value="root"/>
                    <property name="password" value="root"/>
                </dataSource>
            </environment>
        </environments>
        <mappers>
            <!--sql映射文件创建好之后，需要将该文件路径配置到这里-->
            <mapper resource=""/>
        </mappers>
    </configuration>
    ~~~

    

4. 在resources根目录下新建XxxxMapper.xml配置文件

    1. sql语句最后结尾可以不写“;”
    2. 文件的名字不是固定的。
    3. 文件的位置也是随意的。这里选择放在resources根下，相当于放到了类的根路径下。
    4. 将CarMapper.xml**文件路径**配置到mybatis-config.xml：

    ~~~xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <!--namespace先随意写一个-->
    <mapper namespace="car">
        <!--insert sql：保存一个汽车信息-->
        <insert id="insertCar">
            insert into t_car
                (id,car_num,brand,guide_price,produce_time,car_type) 
            values
                (null,'102','丰田mirai',40.30,'2014-10-05','氢能源')
        </insert>
    </mapper>
    ~~~

    ~~~xml
    <mapper resource="CarMapper.xml"/>
    ~~~

 ### 关于名字和路径

1. 文**件名是出现在程序中**的，文件名如果修改了，对应这里的java程序也改一下就行。
2. 路径使用相对路径时，可以采用底层的类加载器来获取对应的资源使得**可移植性强**

~~~java
InputStream is = Thread.currentThread().getContextClassLoader().getResourceAsStream("mybatis-config.xml");
~~~

- 在mybatis中提供了一个类：Resources【org.apache.ibatis.io.Resources】，该类可以从类路径当中获取资源，我们通常使用它来获取输入流InputStream，
- 该方法的底层就是调用类加载器的方法，见上↑。

~~~java
// 这种方式只能从类路径当中获取资源，也就是说mybatis-config.xml文件需要在类路径下。
InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
~~~





## mabatis提供的方法

### 基本方法

SqlSessionFactoryBuilder对象 ---> SqlSessionFactory对象 ---> sqlSession

1. SqlSessionFactoryBuilder可以创建多个SqlSessionFactory
    1. SqlSessionFactoryBuilder就是mybatis-config.xml文件中的environments
    2. SqlSessionFactory就是mybatis-config.xml文件中的environment

~~~java
// 1.创建SqlSessionFactoryBuilder对象
SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();

// 2.创建SqlSessionFactory对象，build方法内传入InputStream对象
SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(Resources.getResourceAsStream("mybatis-config.xml"));

// 3.创建SqlSession对象
sqlSession = sqlSessionFactory.openSession();

// 4.执行SQL，参数为XxxMapper.xml文件中的id。如<insert id="insertCar">
int count = sqlSession.insert("insertCar");
5.提交
sqlSession.commit();
// 回滚
sqlSession.rollback();

// 关闭资源
sqlSession.close();
~~~

### 实例

~~~java
package com.powernode.mybatis;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;

/**
 * 比较完整的第一个mybatis程序写法
 * @author 老杜
 * @since 1.0
 * @version 1.0
 */
public class MyBatisCompleteCodeTest {
    public static void main(String[] args) {
        SqlSession sqlSession = null;
        try {
            // 1.创建SqlSessionFactoryBuilder对象
            SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
            // 2.创建SqlSessionFactory对象
            SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(Resources.getResourceAsStream("mybatis-config.xml"));
            // 3.创建SqlSession对象
            sqlSession = sqlSessionFactory.openSession();
            // 4.执行SQL
            int count = sqlSession.insert("insertCar");
            System.out.println("更新了几条记录：" + count);
            // 5.提交
            sqlSession.commit();
        } catch (Exception e) {
            // 回滚
            if (sqlSession != null) {
                sqlSession.rollback();
            }
            e.printStackTrace();
        } finally {
            // 6.关闭
            if (sqlSession != null) {
                sqlSession.close();
            }
        }
    }
}
~~~



### mybatis工具类的封装

~~~java
package com.powernode.mybatis.utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

/**
 * MyBatis工具类
 *
 * @author 老杜
 * @version 1.0
 * @since 1.0
 */
public class SqlSessionUtil {
    private static SqlSessionFactory sqlSessionFactory;

    /**
     * 类加载时初始化sqlSessionFactory对象
     */
    static {
        try {
            SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
            sqlSessionFactory = sqlSessionFactoryBuilder.build(Resources.getResourceAsStream("mybatis-config.xml"));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 每调用一次openSession()可获取一个新的会话，该会话支持自动提交。
     *
     * @return 新的会话对象
     */
    public static SqlSession openSession() {
        return sqlSessionFactory.openSession(true);
    }
}
~~~

## mybatis的CRUD

### 占位符的使用（insert为例）

- 第一种，利用map

1. **在Java程序中，将数据放到Map集合中**
2. 在sql语句中使用 #{map集合的key} 来完成传值，#{} 等同于JDBC中的 ? ，#{}就是占位符
3. 当#{}中的key不存在，那么记录的是null

~~~java
package com.powernode.mybatis;

import com.powernode.mybatis.utils.SqlSessionUtil;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.HashMap;
import java.util.Map;

/**
 * 测试MyBatis的CRUD
 * @author 老杜
 * @version 1.0
 * @since 1.0
 */
public class CarMapperTest {
    @Test
    public void testInsertCar(){
        // 准备数据
        Map<String, Object> map = new HashMap<>();
        map.put("k1", "103");
        map.put("k2", "奔驰E300L");
        map.put("k3", 50.3);
        map.put("k4", "2020-10-01");
        map.put("k5", "燃油车");
        // 获取SqlSession对象
        SqlSession sqlSession = SqlSessionUtil.openSession();
        // 执行SQL语句（使用map集合给sql语句传递数据）
        int count = sqlSession.insert("insertCar", map);
        System.out.println("插入了几条记录：" + count);
    }
}
~~~

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace先随便写-->
<mapper namespace="car">
    <insert id="insertCar">
        insert into t_car(car_num,brand,guide_price,produce_time,car_type) values(#{k1},#{k2},#{k3},#{k4},#{k5})
    </insert>
</mapper>
~~~

- 第二种利用pojo对象

1. 定义一个pojo类（按javabean规范编写）
2. sql语句中#{}里面填写的是属性名
3. 当属性名不存在，会报错，抛出异常

~~~java
@Test
public void testInsertCarByPOJO(){
    // 创建POJO，封装数据
    Car car = new Car();
    car.setCarNum("103");
    car.setBrand("奔驰C200");
    car.setGuidePrice(33.23);
    car.setProduceTime("2020-10-11");
    car.setCarType("燃油车");
    // 获取SqlSession对象
    SqlSession sqlSession = SqlSessionUtil.openSession();
    // 执行SQL，传数据
    int count = sqlSession.insert("insertCarByPOJO", car);
    System.out.println("插入了几条记录" + count);
}
~~~

~~~xml
<insert id="insertCarByPOJO">
  <!--#{} 里写的是POJO的属性名-->
  insert into t_car(car_num,brand,guide_price,produce_time,car_type) values(#{carNum},#{brand},#{guidePrice},#{produceTime},#{carType})
</insert>
~~~

- 实际上，mybatis在加载时，是通过#{}里的属性名，然后将其首字母转为大写，然后再拼接`get`得到`getXxx`的字符串，再通过反射去调用pojo类中的方法。



- 其实传参数的时候有一个属性parameterType，这个属性用来指定传参的数据类型，不过这个属性是可以省略的

~~~xml
<insert id="insertCar" parameterType="java.util.Map">
  insert into t_car(car_num,brand,guide_price,produce_time,car_type) values(#{carNum},#{brand},#{guidePrice},#{produceTime},#{carType})
</insert>

<insert id="insertCarByPOJO" parameterType="com.powernode.mybatis.pojo.Car">
  insert into t_car(car_num,brand,guide_price,produce_time,car_type) values(#{carNum},#{brand},#{guidePrice},#{produceTime},#{carType})
</insert>
~~~



### delete与update的操作与insert大同小异

不多赘述

### select

#### 查询一条记录

1. 需要在标签上使用resultType属性，从而指定查询后得到的对象。值为**对象的全限定类名**
2. 查询的sql语句中，字段名得对应上pojo类中得属性名，故此处可以使用as设置别名
    1. 底层就是将sql语句用mysql进行查询，返回来的结果是根据别名来命名字段的
    2. 然后mybatis再通过记录中的字段名来为pojo类中的属性赋值
    3. 故若记录上的字段名与pojo类的属性名不一致，就无法正常赋值。赋值进的是null
3. 在java语句中，查询单条结果的方法是`selectOne(String , ...)`，返回的是一个对象

~~~~java
@Test
public void testSelectCarById(){
    // 获取SqlSession对象
    SqlSession sqlSession = SqlSessionUtil.openSession();
    // 执行SQL语句
    Object car = sqlSession.selectOne("selectCarById", 1);
    System.out.println(car);
}
~~~~

~~~xml
<select id="selectCarById" resultType="com.powernode.mybatis.pojo.Car">
  select * from t_car where id = #{id}
</select>
~~~

#### 查询多条记录

1. 前面的步骤基本一致
2. java中，查询多条记录的方法是`selectList(String)`，返回的是List集合

~~~xml
<!--虽然结果是List集合，但是resultType属性需要指定的是List集合中元素的类型。-->
<select id="selectCarAll" resultType="com.powernode.mybatis.pojo.Car">
  <!--记得使用as起别名，让查询结果的字段名和java类的属性名对应上。-->
  select
    id, car_num as carNum, brand, guide_price as guidePrice, produce_time as produceTime, car_type as carType
  from
    t_car
</select>
~~~

~~~java
@Test
public void testSelectCarAll(){
    // 获取SqlSession对象
    SqlSession sqlSession = SqlSessionUtil.openSession();
    // 执行SQL语句
    List<Object> cars = sqlSession.selectList("selectCarAll");
    // 输出结果
    cars.forEach(car -> System.out.println(car));
}
~~~

# 关于配置文件

## Sql.xml规范

1. 编写Sql语句的xml文件命名应该是XxxMapper.xml（与对应接口类命名一致）。将该文件看作是一个java类即可
2. Mapper.xml中，namespace命名空间应该是该Mapper对应接口的全限定类名。
3. 在xml中，对应crud的id应该是对应接口内的方法名

### namespace

编写sql语句的xml文件中根元素的属性

1. 在sql语句的xml文件中，有一个属性namespace
2. 该属性用指定命名空间，防止sql的xml文件中sql语句的id与其他xml文件中语句的id重合
3. 故在java代码中完整的sqlId写法为：`namespace.id`，即在命名空间和sql语句id中间加上`.`



## settings



## typeAliases

别名机制

1. 在mybatis-config.xml文件中加入标签<typeAliases>
2. 该标签下该子标签<typeAlias>
3. <typeAliases>标签内属性有type和alias
    1. type为pojo的全限定类名
    2. alias为别名。不显式定义该属性时，别名为简单类名，即最后一个`.`后面的类名。（别名不区分大小写）
4. 在Mapper.xml文件中的select语句的resultType属性中，直接使用别名即可
5. namespace不可以使用别名



1. 在<typeAliases>内也存在子标签<package>
2. `<package name="全限定包名"/>`，通过该标签可以完成对整个包下的所有类进行起别名。别名就是简单类名。不区分大小写



## properties

1. mybatis-config.xml文件中的property
    1. 位于根元素configuration下的properties标签中，可以配置全局的property元素，**待由其他property来调用**
    2. 位于environments-environment-dataSource元素下的property元素，可以设定该数据源的配置。可以获取全局property的值
2. 单独的.properties文件。该文件中正常填写键值对。待由其他文件调用。
    1. 在mybatis-config.xml文件中可以通过resource属性通过类路径来获取properties文件

- 使用示例

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!--引入外部属性资源文件-->
    <properties resource="jdbc.properties"/>

    <environments default="dev">
        <environment id="dev">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!--${key}使用-->
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
~~~



## environments

多环境数据源，在mybatis-config.xml文件中

1. 对于environments
  1. mybatis的环境可以配置多个数据源
  2. 在该元素上存在default属性：表示默认使用的是哪个环境，default的值是environment的id

2. 对于environment
  1. 属性id：给当前环境一个唯一标识，该标识用在environments的default后面，
  2. 以及设定具体的环境配置（**主要包括：事务管理器的配置 + 数据源的配置**）



### transactionManager

事务机制，mybatis-config.xml文件中的environments-environment-transactionManager元素

1. **jdbc**。mybatis默认采用的事务管理器是：JDBC。JDBC事务默认是不提交的，需要手动提交。
2. **MANAGED**。有用到spring框架时设置为此，表交给框架处理事务，如果没有用到框架设置为此类型，则没有人处理事务，会自动提交

- type属性的值不区分大小写

~~~xml
<transactionManager type="JDBC"/>
<transactionManager type="manage"/>
~~~



### dataSource

该元素具有type属性，有三种值：

1. **UNPOOLED**：采用传统的获取连接的方式，虽然也实现Javax.sql.DataSource接口，但是并没有使用池的思想。

    使用该值后子标签中property可填入：

    1. driver 这是 JDBC 驱动的 Java 类全限定名。
    2. url 这是数据库的 JDBC URL 地址。
    3. username 登录数据库的用户名。
    4. password 登录数据库的密码。
    5. defaultTransactionIsolationLevel 默认的连接事务隔离级别。
    6. efaultNetworkTimeout 等待数据库操作完成的默认网络超时时间（单位：毫秒）

2. **POOLED**：采用传统的javax.sql.DataSource规范中的连接池。是mybatis自带的池化技术

    使用该值后子标签中property可填入：

    1. poolMaximumActiveConnections：最大的活动的连接数量。默认值10
    2. poolMaximumIdleConnections：最大的空闲连接数量。默认值5
    3. poolMaximumCheckoutTime：强行回归池的时间。默认值20秒。
    4. poolTimeToWait：当无法获取到空闲连接时，每隔20秒打印一次日志，避免因代码配置有误，导致傻等。（时长是可以配置的）

3. **JNDI**：采用服务器提供的JNDI技术实现，来获取DataSource对象，不同的服务器所能拿到DataSource是不一样。如果不是web或者maven的war工程，JNDI是不能使用的。

    只存在两个property：

    1. initial_context 这个属性用来在 InitialContext 中寻找上下文。
          1. initialContext.lookup(initial_context)这是个可选属性，
          2. 如果忽略，那么将会直接从 InitialContext 中寻找 data_source 属性。
    2. data_source 这是引用数据源实例位置的上下文路径。提供了 initial_context 配置时会在其返回的上下文中进行查找，没有提供时则直接在 InitialContext 中查找。

## mappers

在mybatis-config.xml文件中的mappers标签

1. 其中有mapper元素，元素内有resource、url、class、package属性。
2. resource属性填写的类路径下的xml文件
3. url填写的是以file:///开始的完全限定资源定位
4. class填写的是从类路径下的全限定接口名。
    1. 接口名与实际上的xml文件名要一致
    2. 接口与xml文件放在同一目录下。
5. mappers标签内还有package标签，该标签name属性填写全限定包名，将包含该包下的所有接口
    1. 同理，接口和xml文件名要一致
    2. 要放在同一目录下

~~~xml
<mappers>
    <mapper resource="CarMapper.xml"/>
    <mapper ulr="file:///"/>
    <mapper class="con.pojo.XxxMapper"/>
    <package name="com.dao"/>
</mappers>
~~~



# 重要对象的生命周期与作用域

作用域和生命周期应该严格规范、考虑

1. Builder类似于工具类，
    1. 目的只是为了创建Factory，且Buidler类不对应着任何一个Factory。Factory只与传入的参数有关
    2. Buidler类应该用完即销毁（即一般用于局部变量创建、个人认为直接将build()方法设置为静态方法也行）
2. Factory类似于应用域对象，在程序运行期间应一直存在
    1. 目的是为了创建Session，
    2. 每种Factory应该只存在一个，不应该创建多个同类型的Factory。最简单的办法就是利用单例模式将其作为应用域对象。还有其他方法，例如仿造tomcat利用容器完成假单例。
3. Session类似于请求域对象（方法作用域）。应该在该线程用完时即销毁
    1. 因为Session是线程不安全的，故该实例不应该被共享，而是方法内独享。
    2. Session不能出现在静态域、也不能出现在类的成员变量中。总之Session只能是独享的数据。
    3. 最简单且规范的使用方法就是在一个Service里创建后，在Service执行完前关闭Seesion。
4. Mapper映射器类也类似于请求域对象。理论上来讲Mapper可以存活于任意阶段。但为了配套Session规范的使用，Mapper的作用域也应该是方法作用域
    1. 因为Session也是在方法内创建，故Mapper也一定会是局部变量。方法执行完毕会被自动销毁。



# 一些技巧细节

## 关于占位符

1. #{}，底层使用PreparedStatement，sql语句先编译，再给?传值

    1. 传入的字符串值都会加上`''`单引号，故传入的大多数是具体的字面量值

2. ${}，底层使用Statement，sql语句先进行拼接，再编译。会有sql注入的风险

    1. 传入的字符串值不会加上单引号，故都用于传入sql中的关键字
    2. 用法：拼接表名、批量删除in (${...})、模糊查询like "%${...}"

## 模糊查询的四种方式

1. `"%${...}%"`
2. **<u>`"%"#{...}"%"`</u>**
3. `concat("%", "${...}", "%")`
4. `concat("%", #{...}, "%")`

​    

## 生成模板

可以利用idea ij进行配置文件的模板生成



## 返回主键值

在做插入操作时，会自动返回插入的主键值

利用标签中的属性：`<insert id="" useGeneratedKeys="true", keyProperty="类成员属性">`

1. useGeneratedKeys，是否返回主键值
2. keyProperty，将主键值返回给哪个属性



# 传入单参数

## 传入简单类型

指的是不是自定义的参数类型，如基本类型：int、double...，包装类：Integer、Long...，还有String、Date（java.uitl和java.sql）

- 实际上，在sql标签上，还有一个属性<parametertype>，用于指定传入的参数类型。参数类型在mybatis中内置了别名。
- 注意Character类型与char类型，

| alias      | type       |
| ---------- | ---------- |
| _byte      | byte       |
| _long      | long       |
| _short     | short      |
| _int       | int        |
| _integer   | int        |
| _double    | double     |
| _float     | float      |
| _boolean   | boolean    |
| string     | String     |
| byte       | Byte       |
| long       | Long       |
| short      | Short      |
| int        | Integer    |
| integer    | Integer    |
| double     | Double     |
| float      | Float      |
| boolean    | Boolean    |
| date       | Date       |
| decimal    | BigDecimal |
| bigdecimal | BigDecimal |
| object     | Object     |
| map        | Map        |
| hashmap    | HashMap    |
| list       | List       |
| arraylist  | ArrayList  |
| collection | Collection |
| iterator   | Iterator   |

- 还有一种用法就是在sql语句中，在使用#{}时，这样使用`#{属性名、javaType=..., jdbcType=...}`

## 传入map参数

1. 在sql标签上填入parameterType（不写也行，会自动识别）
2. 在#{}内填入key值

## 传入pojo实体参数

1. 可以写上parameterType，也可以不写。在mybatis-config.xml中，可以设置别名。
2. 在#{}内填入属性值



# 传入多参数

## 使用

底层自动创建一个Map集合，其中格式为：

1. K：map.put("arg0", "Key");map.put("arg1", "Key");...
2. 或者：map.put("param1", "Value");map.put("param2", "Value");...
3. 后面紧跟随的数字是对应方法中的参数次序。arg以0开始；param以1开始

> 低版本mybatis使用的是#{0}，#{1}...

## 关于@param注解

1. 在定义方法时，在形参前加上@param(value="")
2. 该注解用于显示的表明将来传入的key值是什么，而不是使用mybatis底层的arg0或者param1.
    1. arg不能使用，但还是可以使用param1...	 来视作key
3. 一般来说注解中的值就是方法形参的名字



## @param底层

1. @param会填充到占位符中。那么对于多参数，注解的value是该怎么传递？
2. 首先getMapper()获得到的接口实现类实际上是一个代理类，底层运用了动态代理
3. 然后会将注解传给一个SortedMap<Integer, String>接口的实现类，key为每个注解的次序，Map中的value即为注解值
4. 后续进行检测，若发现有注解出现，则生成一个Map<String, Object>，key为SortedMap中的value，value：SortedMap中的key提取出来然后作为args数组的索引值，args[key]，args即为代理方法invoke的最后一个参数。
5. 最后Map<String, Object>中就保存了注解值，以及对应被注解修饰的实参值。

> 在slq语句中#{}内之所以还可以继续协商param1...，是因为在生成、填充Map<String, Object>操作过程中，会判断是否要添加上key为param1...，value为对应值的操作。
>
> 因此实际上Map<String, Object>中不仅保存注解值与实参值得键值对，还保存有param1..与实参值得键值对



# 查询专题





# 现如今的开发模式

1. 编写mybatis-config.xml文件
2. mvc三层模式
3. dao、mapper层均为接口
4. 编写mapper层对应的Mapper.xml文件，该文件即视为具体的接口实现类
5. 在service层内调用接口方法即可完成业务

# 一些其他框架

## junit

1. 要保证每个有意义的功能方法在进行测试的时候都能通过。

2. 测试的过程中涉及到两个概念：

    1. 期望值
    2. 实际值

    期望值和实际值相同表示测试通过，期望值和实际值不同则单元测试执行时会报错。

3. 在编写时，以方法为单位进行测试

4. 每次测试都需要自己提出一个期望值，然后令业务方法为实际值。

5. 将期望值和实际值传给断言方法，从而完成单元测试

6. 传入实参给业务方法有多少种，那么就应该对应有多少条断言。

## logback

1. maven引入logback相关依赖

    ~~~xml
    <dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-classic</artifactId>
      <version>1.2.11</version>
      <scope>test</scope>
    </dependency>
    ~~~

2. 引入logback相关配置文件（文件名叫做logback.xml或logback-test.xml，放到类路径当中）

    ~~~xml
    <?xml version="1.0" encoding="UTF-8"?>
    
    <configuration debug="false">
        <!-- 控制台输出 -->
        <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
                <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
                <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
            </encoder>
        </appender>
        <!-- 按照每天生成日志文件 -->
        <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
            <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
                <!--日志文件输出的文件名-->
                <FileNamePattern>${LOG_HOME}/TestWeb.log.%d{yyyy-MM-dd}.log</FileNamePattern>
                <!--日志文件保留天数-->
                <MaxHistory>30</MaxHistory>
            </rollingPolicy>
            <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
                <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
                <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
            </encoder>
            <!--日志文件最大的大小-->
            <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
                <MaxFileSize>100MB</MaxFileSize>
            </triggeringPolicy>
        </appender>
    
        <!--mybatis log configure-->
        <logger name="com.apache.ibatis" level="TRACE"/>
        <logger name="java.sql.Connection" level="DEBUG"/>
        <logger name="java.sql.Statement" level="DEBUG"/>
        <logger name="java.sql.PreparedStatement" level="DEBUG"/>
    
        <!-- 日志输出级别,logback日志级别包括五个：TRACE < DEBUG < INFO < WARN < ERROR -->
        <root level="DEBUG">
            <appender-ref ref="STDOUT"/>
            <appender-ref ref="FILE"/>
        </root>
    
    </configuration>
    ~~~

## dom4j与jaxen



## javassist

1. 通过方法的形参，获取目标接口，接口是自己写出来的java源码
2. 创建类池
3. 通过类池制造一个代理类（实现接口的类）、一个接口（形参中的接口）、接口中的方法（实现类中需要实现的接口方法），名称自定义，一般通过反射获取
    1. 其中创建方法需要现创建方法片段，即通过字符串拼接获取方法代码
    2. 然后再将该方法片段添加到方法中
4. 最后需要将接口和方法添加到通过类池创建出来的代理类中
5. 然后将该代理类加载到内存中，完成class文件的生成与加载
6. 接下来就可以通过基本的反射来获取实例了。class.newInstance()

# 关于数据共享还是备份多持有

1. 数据共享节省空间，但有线程问题。
2. 备份人手一份，一般没有线程问题

# 关于是否设置为静态方法

1.  工具性质的方法设置为静态方法
2.  需要上锁的方法不应该设置为静态方法，而是成员方法。
    1. 因为静态方法一般属于工具性质的方法，在有需要的地方都能被调用，且调用的都是同一个方法。
    2. 而这时候如果静态方法内部上锁，那么就会造成多个线程在调用这个方法时进行排队，造成时间浪费。
    3. 因此在方法内部需要上锁，那么就不应该设置为静态方法。而是设置为成员方法
    4. 被调用时，首先需要创建一个实例，再通过实例来调用方法

# 简易mybatis框架

1. 定义一个Resource类，用于获取资源的输入流

    1. 工具类，静态方法

2. 定义一个Builder类，

    1. 方法parse()：解析配置文件。私有方法。
        1. 需要参数得到配置文件
    2. 方法build()：用于获取不同的Factory对象。
        1. 公开方法
        2. 调用parse方法
        3. 需要形参得到配置文件，以及配置中的
    3. 采用生产者（建造者）模式。
    4. 具体的指挥者就是外部的配置文件。一系列的算法都集成在Builder内的build方法内，通过配置文件来达到创建不同工厂类（即产品product）。

3. 定义一个Factory类，

    1. 用于获取不同的sqlSession对象

    2. 工厂类里有属性，对应着配置文件的信息

        1. 事务管理器

            1. 该属性面向接口，有两种实现类，jdbc与managed
            2. 接口中包含基本的方法：开启open、获取get、提交commit、回滚roll、关闭close
            3. 实现类中有数据源信息属性，该信息面向接口DataSource
                1. 根据pool、unpool等来实现DataSource
                2. DataSource实现类包含属性property，如username、pwd等

        2. Map属性：

            1. 用于保证了操作的原子性。不同工厂对象，使得不同会话对象可以获取不同的Map集合。从而

            2. K为该sqlId,即namespace.id

            3. V为另一个类，包装了mapper映射信息，该类中resultType和sql语句作为属性。

                 

4. 定义一个sqlSession类，用于开启不同的会话，会话可以保证事务的一致性

    1. 使用组合，含有Transaction属性，以便获取连接资源的相关方法
    2. 内置提交、回滚、关闭等方法，方法内调用的是Transaction的方法
    3. 需要有解析slq语句的方法



## 自己

1. Resource类，获取类路径下指定资源文件的输入流

2. Builder类，通过核心配置文件来构造一个指定的sql会话工厂

3. Factory类，包含核心配置文件中的数据

    1. List<String>接口，用于存放mappers标签下的所有资源路径
    2. DataSouece接口，用于接收指定数据源
    3. TransactionManager接口，用于接收指定的事务机制
    4. openSession方法，用于获取一个包含具体sql映射语句、数据库操作方法的Session对象

4. Session类，会解析更加具体的sql文件，每个Session类都将备份一份sql文件中的数据

    1. HashMap<String, String>，用于接收对应的sqlId和sql语句

    2. void rollback()方法

    3. void close方法

    4. void commit方法

    5. int insert(String sqlId, Object pojo)方法

    6. int update(String sqlId, Object pojo)

    7. int delete(String sqlId, Object pojo)

    8. Object selectOne(String sqlId, Object pojo)

        ~~~java
        public <T> T selectOne(String statement, Object parameter) {
            // Popular vote was to return null on 0 results and throw exception on too many.
            List<T> list = this.selectList(statement, parameter);
            if (list.size() == 1) {
              return list.get(0);
            } else if (list.size() > 1) {
              throw new TooManyResultsException("Expected one result (or null) to be returned by selectOne(), but found: " + list.size());
            } else {
              return null;
            }
          }
        ~~~

    9. List<Object> selectList(String sqlId, Object pojo)







# Mybatis源码分析



调用流程

1. 客户端调用`SqlSessionFactoryBuilder`的`build()`返回一个`DefaultSqlSessionFactory`实现类
    1. build过程中需要通过`XMLConfigBuilder`类解析得到`Configuration`类，然后才能开始调用`DefaultSqlSessionFactory`的构造器来创建实例
2. 客户端通过`DefaultSqlSessionFactory`实现类开启会话`openSession()`返回一个`DefaultSqlSession`实现类
    1. open过程中需要通过`Configuration`类得到环境、事务管理器以及执行器，然后才能通过`DefaultSqlSession`的构造器创建实例
3. 客户端可以通过`DefaultSqlSession`实现类操作数据库，如update
    1. Session相关的方法实际上是调用Executor执行器中的方法来完成。

## 生产者/建造者类

主要定义了两种方式来创建工厂类

1. 通过字符流
2. 通过字节流

```java
public class SqlSessionFactoryBuilder {
  public SqlSessionFactory build(Reader reader, String environment, Properties properties) {
    try {
      XMLConfigBuilder parser = new XMLConfigBuilder(reader, environment, properties);
      return build(parser.parse());
    } catch (Exception e) {
      throw ExceptionFactory.wrapException("Error building SqlSession.", e);
    } finally {
      ErrorContext.instance().reset();
      try {
       if (reader != null) {
         reader.close();
       }
      } catch (IOException e) {
        // Intentionally ignore. Prefer previous error.
      }
    }
  }

  public SqlSessionFactory build(InputStream inputStream, String environment, Properties properties) {
    try {
      XMLConfigBuilder parser = new XMLConfigBuilder(inputStream, environment, properties);
      return build(parser.parse());
    } catch (Exception e) {
      throw ExceptionFactory.wrapException("Error building SqlSession.", e);
    } finally {
      ErrorContext.instance().reset();
      try {
       if (inputStream != null) {
         inputStream.close();
       }
      } catch (IOException e) {
        // Intentionally ignore. Prefer previous error.
      }
    }
  }

  public SqlSessionFactory build(Configuration config) {
    return new DefaultSqlSessionFactory(config);
  }
}
```

1. 可以看出内部逻辑实际上都一样，只不过是传给`XMLConfigBuilder`xml解析器的参数不同而已。

2. 最终通过解析器解析返回的`Configuration`来完成创建工厂类

    ~~~java
    public SqlSessionFactory build(InputStream inputStream, String environment, Properties properties) {
        XMLConfigBuilder parser = new XMLConfigBuilder(inputStream, environment, properties);
        return build(parser.parse());
    }
    public SqlSessionFactory build(Configuration config) {
        return new DefaultSqlSessionFactory(config);
    }
    ~~~



## xml解析器

`XMLConfigBuilder`类是完成配置文件解析的手段

主要的调用执行顺序是：

~~~java
public class XMLConfigBuilder extends BaseBuilder {
    public XMLConfigBuilder(InputStream inputStream, String environment, Properties props) {
    	// 该this()执行下面的 XMLConfigBuilder(XPathParser parser, String environment, Properties props)构造器
        this(new XPathParser(inputStream, true, props, new XMLMapperEntityResolver()), environment, props);
  	}
    private XMLConfigBuilder(XPathParser parser, String environment, Properties props) {
        // ...实现了相关字段的赋值
    }
    // 初始化完成后进入解析接口
    public Configuration parse() {
        parseConfiguration(parser.evalNode("/configuration"));
        return configuration;// 解析完成后返回保存解析结果的configuration对象
    }
    // 开始进行解析的位置
    private void parseConfiguration(XNode root) {
        // ...
    }
}
~~~

最终通过`parse()`方法返回`Configuration`对象



## 配置文件对象

~~~java
public class Configuration {
    protected Environment environment;// mybatis-config.xml文件中的environment标签数据将保存在这里
    protected ProxyFactory proxyFactory = new JavassistProxyFactory();// javassisit完成动态生成类
    protected final Map<String, MappedStatement> mappedStatements = 
        new StrictMap<MappedStatement>("Mapped Statements collection")
        .conflictMessageProducer(
        (savedValue, targetValue)
        -> ". please check " + savedValue.getResource() + " and " + targetValue.getResource()
    );// 保存xml映射文件中的sql语句
    // 保存返回值类型
    protected final Map<String, ResultMap> resultMaps = new StrictMap<>("Result Maps collection");
    // 保存参数类型
    protected final Map<String, ParameterMap> parameterMaps = new StrictMap<>("Parameter Maps collection");

    // 通过全限定id获取对应的sql映射，返回给DefaultSqlSession
    public MappedStatement getMappedStatement(String id, boolean validateIncompleteStatements) {
        if (validateIncompleteStatements) {
            buildAllStatements();
        }
        return mappedStatements.get(id);// 从Map集合获取Value
    }
    // 根据配置信息创建一个执行器，并返回给DefaultSqlSessionFactory
    public Executor newExecutor(Transaction transaction, ExecutorType executorType) {
        executorType = executorType == null ? defaultExecutorType : executorType;
        Executor executor;
        if (ExecutorType.BATCH == executorType) {
            executor = new BatchExecutor(this, transaction);
        } else if (ExecutorType.REUSE == executorType) {
            executor = new ReuseExecutor(this, transaction);
        } else {
            executor = new SimpleExecutor(this, transaction);
        }
        if (cacheEnabled) {
            executor = new CachingExecutor(executor);
        }
        executor = (Executor) interceptorChain.pluginAll(executor);
        return executor;
    }
}
~~~

~~~~java
public final class Environment {// 保存mybatis-config.xml文件中的environment配置信息
    private final String id;
    private final TransactionFactory transactionFactory;
    private final DataSource dataSource;
}
~~~~

~~~java
public final class MappedStatement {// 保存Mapper.xml文件中每一条crud标签中的信息
    // 内置了一个Builder类，用于解析标签信息
    
}
~~~

> MappedStatement类中有一个id属性。这个id和Map集合`mappedStatements`中Key的id是相同的。它们都表示MappedStatement的唯一标识符



## 两个接口

`SqlSessionFactory`和`SqlSession`

1. 工厂主要声明了开启会话的方法

    ~~~java
    public interface SqlSessionFactory {
      SqlSession openSession();
      SqlSession openSession(boolean autoCommit);
      SqlSession openSession(Connection connection);
      SqlSession openSession(TransactionIsolationLevel level);
      SqlSession openSession(ExecutorType execType);
      SqlSession openSession(ExecutorType execType, boolean autoCommit);
      SqlSession openSession(ExecutorType execType, TransactionIsolationLevel level);
      SqlSession openSession(ExecutorType execType, Connection connection);
      Configuration getConfiguration();
    }
    ~~~

2. 会话主要声明了crud和事务管理的方式以及获取连接资源

    ~~~java
    public interface SqlSession extends Closeable {
        <T> T selectOne(String statement, Object parameter);
        <E> List<E> selectList(String statement, Object parameter, RowBounds rowBounds);
        <K, V> Map<K, V> selectMap(String statement, Object parameter, String mapKey, RowBounds rowBounds);
        void select(String statement, Object parameter, RowBounds rowBounds, ResultHandler handler);
        int insert(String statement, Object parameter);
        int update(String statement, Object parameter);
        int delete(String statement, Object parameter);
        void commit();
        void rollback();
        @Override
        void close();
        Configuration getConfiguration();
        <T> T getMapper(Class<T> type);
        Connection getConnection();
    }
    ~~~

    >Closeable继承了AutoCloseable接口



## 两个默认实现类

1. 工厂默认实现类

~~~java
public class DefaultSqlSessionFactory implements SqlSessionFactory {
    private final Configuration configuration;// 每一个实现类都将拥有配置文件中的数据

    // 实例化一个工厂类
    public DefaultSqlSessionFactory(Configuration configuration) {
        this.configuration = configuration;
    }
    // 无关Connection的openSession方法将调用下面的方法
    private SqlSession openSessionFromDataSource(ExecutorType execType, TransactionIsolationLevel level, boolean autoCommit) {
        Transaction tx = null;
        try {
            final Environment environment = configuration.getEnvironment();
            final TransactionFactory transactionFactory = getTransactionFactoryFromEnvironment(environment);
            tx = transactionFactory.newTransaction(environment.getDataSource(), level, autoCommit);
            final Executor executor = configuration.newExecutor(tx, execType);
            return new DefaultSqlSession(configuration, executor, autoCommit);
        } catch (Exception e) {
            closeTransaction(tx); // may have fetched a connection so lets call close()
            throw ExceptionFactory.wrapException("Error opening session.  Cause: " + e, e);
        } finally {
            ErrorContext.instance().reset();
        }
    }

    // 有关Connection的方法将调用下面的方法
    private SqlSession openSessionFromConnection(ExecutorType execType, Connection connection) {
        try {
            // 以下部分是设置提交事务的方式
            boolean autoCommit;
            try {
                autoCommit = connection.getAutoCommit();
            } catch (SQLException e) {
                autoCommit = true;
            }
            // 得到数据库环境配置
            final Environment environment = configuration.getEnvironment();
            // 获取事务管理工厂
            final TransactionFactory transactionFactory = getTransactionFactoryFromEnvironment(environment);
            // 通过工厂创建事务管理器
            final Transaction tx = transactionFactory.newTransaction(connection);
            // 得到执行器
            final Executor executor = configuration.newExecutor(tx, execType);
            // 创建返回一个默认的会话实现类，其中每一个会话类都持有该工厂的配置数据
            return new DefaultSqlSession(configuration, executor, autoCommit);
        } catch (Exception e) {
            throw ExceptionFactory.wrapException("Error opening session.  Cause: " + e, e);
        } finally {
            ErrorContext.instance().reset();
        }
    }
}
~~~

2. 会话默认实现类

~~~java
public class DefaultSqlSession implements SqlSession {
    private final Configuration configuration;// 数据
    private final Executor executor;// 执行器
    private final boolean autoCommit;// 提交事务方式

    // 创建默认会话实现类
    public DefaultSqlSession(Configuration configuration, Executor executor, boolean autoCommit) {
        this.configuration = configuration;
        this.executor = executor;
        this.dirty = false;
        this.autoCommit = autoCommit;
    }

    // 下面的方法都是重写SqlSession的方法
    // 其中dml操作都将调用update方法
    @Override
    public int update(String statement, Object parameter) {
        try {
            // 获取全限定id（statement）对应的sql映射
            MappedStatement ms = configuration.getMappedStatement(statement);
            // 通过执行器完成更新
            return executor.update(ms, wrapCollection(parameter));
        } catch (Exception e) {
            throw ExceptionFactory.wrapException("Error updating database.  Cause: " + e, e);
        } finally {
            ErrorContext.instance().reset();
        }
    }

    @Override
    public <T> T selectOne(String statement, Object parameter) {
        // Popular vote was to return null on 0 results and throw exception on too many.
        List<T> list = this.selectList(statement, parameter);
        if (list.size() == 1) {
            return list.get(0);
        } else if (list.size() > 1) {
            throw new TooManyResultsException("Expected one result (or null) to be returned by selectOne(), but found: " + list.size());
        } else {
            return null;
        }
    }

    private <E> List<E> selectList(String statement, Object parameter, RowBounds rowBounds, ResultHandler handler) {
        try {
            MappedStatement ms = configuration.getMappedStatement(statement);
            return executor.query(ms, wrapCollection(parameter), rowBounds, handler);
        } catch (Exception e) {
            throw ExceptionFactory.wrapException("Error querying database.  Cause: " + e, e);
        } finally {
            ErrorContext.instance().reset();
        }
    }

    // 用于给占位符"?"传参
    private Object wrapCollection(final Object object) {
        return ParamNameResolver.wrapToMapIfCollection(object, null);
    }


    @Override
    public void commit(boolean force) {
        try {
            executor.commit(isCommitOrRollbackRequired(force));
            dirty = false;
        } catch (Exception e) {
            throw ExceptionFactory.wrapException("Error committing transaction.  Cause: " + e, e);
        } finally {
            ErrorContext.instance().reset();
        }
    }

    @Override
    public void rollback(boolean force) {
        try {
            executor.rollback(isCommitOrRollbackRequired(force));
            dirty = false;
        } catch (Exception e) {
            throw ExceptionFactory.wrapException("Error rolling back transaction.  Cause: " + e, e);
        } finally {
            ErrorContext.instance().reset();
        }
    }

    @Override
    public void close() {
        try {
            executor.close(isCommitOrRollbackRequired(false));
            closeCursors();
            dirty = false;
        } finally {
            ErrorContext.instance().reset();
        }
    }

    private boolean isCommitOrRollbackRequired(boolean force) {
        return (!autoCommit && dirty) || force;
    }
    @Override
    public Connection getConnection() {
        try {
            return executor.getTransaction().getConnection();
        } catch (SQLException e) {
            throw ExceptionFactory.wrapException("Error getting a new connection.  Cause: " + e, e);
        }
    }
}
~~~





## 执行器

顶级接口

~~~java
public interface Executor {

    int update(MappedStatement ms, Object parameter) throws SQLException;

    <E> List<E> query(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, CacheKey cacheKey, BoundSql boundSql) throws SQLException;

    <E> List<E> query(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler) throws SQLException;


    void commit(boolean required) throws SQLException;

    void rollback(boolean required) throws SQLException;

    void close(boolean forceRollback);
    boolean isClosed();
    
    void setExecutorWrapper(Executor executor);
}
~~~

抽象父类

~~~java
public abstract class BaseExecutor implements Executor {
    protected Transaction transaction;
    protected Configuration configuration;
    
    @Override
    public int update(MappedStatement ms, Object parameter) throws SQLException {
        ErrorContext.instance()
            .resource(ms.getResource())
            .activity("executing an update").object(ms.getId());

        if (closed) {
            throw new ExecutorException("Executor was closed.");
        }

        clearLocalCache();
        return doUpdate(ms, parameter);
    }

    @Override
    public <E> List<E> query(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, CacheKey key, BoundSql boundSql) throws SQLException {
        ErrorContext.instance().resource(ms.getResource()).activity("executing a query").object(ms.getId());
        if (closed) {
            throw new ExecutorException("Executor was closed.");
        }
        if (queryStack == 0 && ms.isFlushCacheRequired()) {
            clearLocalCache();
        }
        List<E> list;
        try {
            queryStack++;
            list = resultHandler == null ? (List<E>) localCache.getObject(key) : null;
            if (list != null) {
                handleLocallyCachedOutputParameters(ms, key, parameter, boundSql);
            } else {
                list = queryFromDatabase(ms, parameter, rowBounds, resultHandler, key, boundSql);
            }
        } finally {
            queryStack--;
        }
        if (queryStack == 0) {
            for (DeferredLoad deferredLoad : deferredLoads) {
                deferredLoad.load();
            }
            // issue #601
            deferredLoads.clear();
            if (configuration.getLocalCacheScope() == LocalCacheScope.STATEMENT) {
                // issue #482
                clearLocalCache();
            }
        }
        return list;
    }
    @Override
    public void close(boolean forceRollback) {
        if (transaction != null) {
            transaction.close();
        }

    }

    @Override
    public void commit(boolean required) throws SQLException {
        if (required) {
            transaction.commit();
        }
    }

    @Override
    public void rollback(boolean required) throws SQLException {
        if (required) {
            transaction.rollback();
        }
    }
}
~~~

基础执行器

~~~java
public class SimpleExecutor extends BaseExecutor {
    public int doUpdate(MappedStatement ms, Object parameter) throws SQLException;
    public <E> List<E> doQuery(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) throws SQLException;
     private Statement prepareStatement(StatementHandler handler, Log statementLog) throws SQLException;
        
}
~~~





## sql占位符映射

~~~java
public class ParamNameResolver {
    
    public static Object wrapToMapIfCollection(Object object, String actualParamName) {
        if (object instanceof Collection) {
            ParamMap<Object> map = new ParamMap<>();
            map.put("collection", object);
            if (object instanceof List) {
                map.put("list", object);
            }
            Optional.ofNullable(actualParamName).ifPresent(name -> map.put(name, object));
            return map;
        } else if (object != null && object.getClass().isArray()) {
            ParamMap<Object> map = new ParamMap<>();
            map.put("array", object);
            Optional.ofNullable(actualParamName).ifPresent(name -> map.put(name, object));
            return map;
        }
        return object;
    }
}
~~~



## sql处理器

顶级接口

~~~java
public interface StatementHandler {

  Statement prepare(Connection connection, Integer transactionTimeout)
      throws SQLException;

  void parameterize(Statement statement)
      throws SQLException;

  void batch(Statement statement)
      throws SQLException;

  int update(Statement statement)
      throws SQLException;

  <E> List<E> query(Statement statement, ResultHandler resultHandler)
      throws SQLException;

  <E> Cursor<E> queryCursor(Statement statement)
      throws SQLException;

  BoundSql getBoundSql();

  ParameterHandler getParameterHandler();

}
~~~

抽象父类

~~~java
public abstract class BaseStatementHandler implements StatementHandler {
    
}
~~~

基础处理器

~~~java
public class SimpleStatementHandler extends BaseStatementHandler {
}
~~~





## 会话管理器

1. 管理器实现了工厂和会话接口

~~~java
public class SqlSessionManager implements SqlSessionFactory, SqlSession{}
~~~



## 事务管理器

接口：

```java
public interface Transaction {
  Connection getConnection() throws SQLException;
  void commit() throws SQLException;
  void rollback() throws SQLException;
  void close() throws SQLException;
  Integer getTimeout() throws SQLException;
}
```

> 为什么不继承AutoClose接口

jdbc管理事务

~~~java
public class JdbcTransaction implements Transaction {
  protected Connection connection;
  protected DataSource dataSource;
  protected boolean autoCommit;

  @Override
  public Connection getConnection() throws SQLException {
    if (connection == null) {
      openConnection();
    }
    return connection;
  }

  @Override
  public void commit() throws SQLException {
    if (connection != null && !connection.getAutoCommit()) {
      if (log.isDebugEnabled()) {
        log.debug("Committing JDBC Connection [" + connection + "]");
      }
      connection.commit();
    }
  }

  @Override
  public void rollback() throws SQLException {
    if (connection != null && !connection.getAutoCommit()) {
      if (log.isDebugEnabled()) {
        log.debug("Rolling back JDBC Connection [" + connection + "]");
      }
      connection.rollback();
    }
  }

  @Override
  public void close() throws SQLException {
    if (connection != null) {
      resetAutoCommit();
      if (log.isDebugEnabled()) {
        log.debug("Closing JDBC Connection [" + connection + "]");
      }
      connection.close();
    }
  }

  protected void openConnection() throws SQLException {
    if (log.isDebugEnabled()) {
      log.debug("Opening JDBC Connection");
    }
    connection = dataSource.getConnection();
    if (level != null) {
      connection.setTransactionIsolation(level.getLevel());
    }
    setDesiredAutoCommit(autoCommit);
  }

}
~~~

> 可能开启资源和获取资源分开是为了更好的符合单一职责原则，开启open也主要是被获取get调用。
