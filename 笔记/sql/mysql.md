# 关于mysql

## 连接mysql服务

`mysql -h 主机IP -P 端口 -u 用户名 -p密码`

可以不写`-h 主机IP`，默认是本机主机。(127.0.0.1/localhost)

可以不写`-P 端口`，默认是my.ini文件中的端口

`-p密码`，不输入密码，直接回车，则在下一行要求输入密码。（-p与密码直接没空格）





## mysql的工作机制

DBMS的核心是mysqld.exe，该程序监听的窗口就是3306.

客户端通过这个端口连接上mysql。

每当客户端要对mysql进行操作时，都是通过这个端口进行连接，并传输数据的。

DBMS下可以管理很多的数据库（db1，db2...）。数据库的位置存放在my.ini文件中指定的data目录下

每个数据库下可以管理很多的表（表的本质是一个文件）。



## mysql用户管理

~~~mysql
-- 创建用户
create user "用户名"@ "登录IP" identified by "密码";

-- 删除用户
drop user "用户名" @ "IP";

-- 修改密码
set password = password("密码");
set password for "用户名" @ "IP" = password("密码");# 修改他人密码，需要权限
ALTER USER 'root'@'localhost' IDENTIFIED WITH 
mysql_native_password BY '密码';

-- 授权
grant 权限 on 库.表/视图... to "用户名"@ "IP" [identified by "密码"];# 中括号的内容：若用户存在，则修改密码，否则创建用户
grant 权限 on *.* to "用户名"@ "IP"; # 为所有库、所有对象授权
grant 权限 on 库.* to "用户名"@ "IP";# 为指定库的所有对象授权

-- 收回权限
revoke 权限 on 库.... from "用户名"@ "IP";
~~~

![](https://img-blog.csdnimg.cn/4285b2bad384496a94b6c4eb544747e6.png)



## 关于模糊IP

~~~mysql
-- 说明 用户管理的细节
-- 在创建用户的时候，如果不指定Host, 则为% , %表示表示所有IP都有连接权限 
-- create user  xxx;
CREATE USER jack
SELECT `host`, `user` FROM mysql.user

-- 你也可以这样指定 
-- create user  'xxx'@'192.168.1.%'  表示 xxx用户在 192.168.1.*的ip可以登录mysql
CREATE USER 'smith'@'192.168.1.%'

-- 在删除用户的时候，如果 host 不是 %, 需要明确指定  '用户'@'host值'
DROP USER jack -- 默认就是 DROP USER 'jack'@'%'
DROP USER 'smith'@'192.168.1.%'
~~~

## 忘记密码

[csdn](https://blog.csdn.net/m0_56767169/article/details/126385776?ops_request_misc=&request_id=&biz_id=102&utm_term=mysql8.0.32%E5%AF%86%E7%A0%81%E5%BF%98%E8%AE%B0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-126385776.142^v73^insert_down2,201^v4^add_ask,239^v1^insert_chatgpt&spm=1018.2226.3001.4187)

## 字符集

CHARACTER SET（缩写CHARSET）用于指定数据库采用的字符集，默认是utf8

utf8mb4


## 校对规则

COLLATE用于指定数据库字符集的校对规则

utf8mb4-unicode-ci

utf8_bin在校对时区分大小写

utf8_general_ci在校对时不区分大小写。这是默认的校对规则

表的字符集和校对规则的默认值都以数据库为准





# 列类型

![](https://img-blog.csdnimg.cn/34d126a381d74f1c8e250705521ba272.png)

## 数值

BIT类型数据，存放的是二进制数据。单查询时仍可以按十进制查询。当保存的值只是0和1时，可以用BIT(1)来保存。

在指定无符号时，UNSIGNED写在类型INT后面。

DECIMAL中：

1. M指定总位数，D指定小数典后的 位数
2. M是精度，最大65；D是标度，最大30
3. M默认10，D默认0

## 文本

CHAR。最多存放255个**<u>字符</u>**。

VARCAHR。最多存放65532字节。被减去的3个字节用于存放该数据的大小。可存放的字符数量与编码相关。如utf-8下最多存放21844个汉字。

但是在(size)，这个size所填的都是字符数量，即char的size最多255，varchar的size在utf-8下是21844个汉字。

关于定长与变长：

对于varchar来说，实际占用的空间大小 = 实际字符数 * 编码规定的字节大小 + 1-3个字节（根据字符串长度不同而占用不同空间来存放长度值）。size是指定最大的字符数量，而不指定占用空间。

对于char来说，实际占用的空间大小 = size * 编码规定的字节大小。size确定，则空间大小也确定。

varchar多用于留言、文章。char多用于md5密码（加密后是32位）、手机号（常见11位）、邮编（6位）等。

char的查询速度大于varchar。

text与varchar差不多。

## BLOB

用于存储二进制数据。**特别说明：序列化对象就可以用这个数据类型接收。**

## 时间

TIMESTAMP 时间戳，想要自动更新时间，那么需要在后面加上`NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP`。不指定该类型的列名时，则自动在当前表更改后 更新时间



## 常用类型

int、double、decimal、char、varchar、text、date、dateTime、timeStamp



# 关于备份与恢复

~~~
在dos下，执行备份/导出功能
mysqldump -u user_name -p密码 -B db_name01 db_name02 ... > 文件位置#此条语句用于备份多个数据库
mysqldump -u user_name -p密码 db_name table_name01 table_name02 ... > 文件位置 #此条语句用于备份数据库中的多张表

在mysql下，执行恢复/导入功能
source 文件位置

文件位置，如：F:\mysql\mysql1.sql
~~~



# 关于库的语法

~~~mysql
#创建数据库
CREATE DATABASE [IF NOT EXISTS] 数据库名#中括号内表示，如果不存在则创建，存在不报错。当不写这段语句时，即默认情况下，如果不存在则创建，存在则报错。
CREATE DATABASE 数据库名 CHARACTER SET 字符集 COLLATE 校对规则
#`db_name`

#查看数据库
SHOW DATABASES
#查看指定数据库的定义信息
SHOW CREATE DATABASE db_name

#删除数据库
DROP DATABASE [IF EXISTS] db_name

~~~

在创建时，可以利用反引号来避免数据库名是关键字的情况.

# 关于表的语法

~~~mysql
#创建表
CREATE TABLE tableName (
	field1 datatype,#field列名（字段名），datatype列类型（字段类型）
	field2 datatype,
	field3 datatype
) character set 字符集 collate 校对规则 engine 存储引擎

#添加操作时，后加下面关键字
Not NULL default value0#指定该列不为空，且为value初始值
AFTER fieldName#在什么列 后面

#可以指定某字段为某集合中中元素，利用Enum()可以不同写数据类型
如field00 Enum(1， 2， 3),

#查看表结构，即查看表的所有列 
desc tableName
#修改表名
Rename table oldTableName to newTableName
#删除表
drop table tableName
#修改表字符集
alter table tableName character set 字符集


#添加列
ALTER TABLE tableName ADD field1 datatype not null default value0 after fieldName


#修改列
ALTER TABLE tableName MODIFY field1 datatype#field1存在，且此语句不改变列名。故用于修改数据类型
alter table tableName change oldFieldName newFieldName datatype#改变列名。故用于修改列名

#删除列
ALTER TABLE tableName DROP field1

dual 是亚元表，也是系统表。可以作为测试表。
~~~



# 关于记录的操作

## 添加

~~~mysql
INSERT INTO tablename[(field_name01, field_name02, ...)] VALUES(value01, value02, ...), (value01, value02, ...)#中括号不指定列名，则默认全部列都需要赋值。可以同时添加多条记录
~~~

### 注意事项

1. 输入的值要符合数据类型。但是一个字符串，如果可以隐式的转换为int，那么也可以输入该字符串。如'30'是可以传给int类型的列。
2. 输入的值要符合数据范围。
3. 输入的值与列名要一一对应
4. 字符和日期要用引号包含起来
5. 列可以插入null，前提是可以为null。利用`not null `来设置不可为空
6. 可以不指定列名，在传值时按照默认顺序进行赋值
7. 再添加记录时，若对某列不赋值，则该列使用默认值（null或指定的默认值`not null default value0`）。但如果没有指定默认值且不能为null，则报错。



## 关于where子句经常使用的运算符

![](https://img-blog.csdnimg.cn/fd63f12f49e64e9a92a514241746a17e.png)

where内不能使用聚合函数

## 修改

~~~mysql
update tableName set fieldName01=value01, fieldName02=value02, ... [where fieldName=value0]#不加where则默认对表中指定列的所有记录进行修改
~~~

一般情况下要带where关键字



## 删除

~~~mysql
delete from tableName [where fieldName=value0]#不加where默认删除所有记录
~~~





# 函数

## 统计函数

count(*|expression)查询记录数量，expression为null的记录不会统计

sum(expreesion)查询数值总和

avg(expression)查询数值平均数

max(expression)/min(expression)返回最大/最小值



## 字符串函数

![](https://img-blog.csdnimg.cn/cfba38dd05414f469975e7b52ee75eec.png)



~~~mysql
# 拼接多个字段成为一列输出
select concat(field01, "" , 2, field02, ...) from tableName
~~~



## 数学函数

![](https://img-blog.csdnimg.cn/4db74297597641398973cfef2ea76d7c.png)

同一种子，随机数不变。即每次查询返回的都是同一串数字。



## 时间函数

![](https://img-blog.csdnimg.cn/2db92edf954546db89a48837e7997165.png)



DATE开头的函数，里面传入的时间可以是datetime、date、timestamp；d_type可以是year、 month、day、hour、minute、second



### 开发用法

~~~mysql
-- 筛选时间范围

#过滤得到，时间在time01时间内的记录
where DATE_ADD(datetime, interval time01 MINUTE) >= NOW();
#以下表达也是对的
where DATE_SUB(now(), interval time01 MINUTE) <= datetime;
~~~



~~~mysql
#利用int保存时间，要用日期的情况下再from_unixtime()来转为日期

unix_timestamp()# 返回1970-1-1 00:00;00到现在的秒数

from_unixtime(second, "%Y-%m-%d %H:%i:%s")# 将一个unix_timestamp的秒数转为指定格式的日期
#h是12小时制，H是24小时制
~~~



## 加密和系统函数

~~~mysql
select user() from dual;# 查询当前数据库的用户以及IP

select DATABASE(); #查询当前数据库的名称

select MD5(string); # 为字符串算出MD5 32个字符的字符串，常用于加密

# 创建密码列： `password` CHAR(32) not null default "";
# 添加记录： MD5(value);
# 查询记录： where password = MD5(value);

select Password(String);#mysql数据库的用户密码就是这个函数来加密的
~~~



## 流程控制函数

~~~mysql
# 演示流程控制语句
select IF(expression, value01, value02);# expression 为空则会返回value01，否则返回value02
select IFNULL(value01, value02);# value01为空，返回value02，否则返回value01；
select (CASE 
 			when expr1 then expr2
			when expr3 then expr4
			else expr5 
 			end);
#判断null时，用 is null来判断，而不是用=号


-- 1. 查询emp 表, 如果 comm 是null , 则显示0.0
--    判断是否为null 要使用 is null, 判断不为空 使用 is not
SELECT ename, IF(comm IS NULL , 0.0, comm)
	FROM emp;
SELECT ename, IFNULL(comm, 0.0)
	FROM emp;
~~~









# 复制操作

~~~mysql
-- 复制表结构
create table tableName like table1;

-- 将记录从一张表复制到新表
insert into tableName (field01, ...) select (field00, ...) from tableName00;

-- 记录自我复制
insert into tableName select * from tableName;
~~~

# 去重操作

~~~mysql
-- 1. 复制表结构
-- 2. 复制去重后的表记录
-- 3. 删除原先表
-- 4. 改名新表为原先表
~~~







# 查询

## 单表

###  基本查询

~~~mysql
# * 号代表查询所有列；from指定查询的表；distinct代表是否去除重复记录，只有所有字段都相同才会去重
select [distinct] *|(field01, field02, ...) from tableName
~~~

~~~mysql
#查询过程中对列进行运算
select (field01 + field02 - ...) from tableName # 即可以在括号内输入一个表达式

#查询结果的 列名 替换为 别名
select field01 as "newName01", field02 as "newName02" from tableName
~~~

### 过滤查询

~~~mysql
-- 查询姓名为赵云的学生成绩
SELECT * FROM student 
	WHERE `name` = '赵云'
	
-- 查询英语成绩大于90分的同学
SELECT * FROM student 
	WHERE english > 90
	
-- 查询总分大于200分的所有同学
SELECT * FROM student 
	WHERE (chinese + english + math) > 200
	
-- 查询math大于60 并且(and) id大于4的学生成绩
SELECT * FROM student
	WHERE math >60 AND id > 4
	
-- 查询英语成绩大于语文成绩的同学
SELECT * FROM student
	WHERE english > chinese
	
-- 查询总分大于200分 并且 数学成绩小于语文成绩,的姓赵的学生.
-- 赵% 表示 名字以韩开头的就可以
SELECT * FROM student
	WHERE (chinese + english + math) > 200 AND 
		math < chinese AND `name` LIKE '赵%'
		
-- 查询英语分数在 80－90之间的同学。
SELECT * FROM student
	WHERE english >= 80 AND english <= 90;
SELECT * FROM student
	WHERE english BETWEEN 80 AND 90; -- between .. and .. 是 闭区间
	
-- 查询数学分数为89,90,91的同学。
SELECT * FROM student 
	WHERE math = 89 OR math = 90 OR math = 91;
SELECT * FROM student 
	WHERE math IN (89, 90, 91);
	
-- 查询所有姓李的学生成绩。
SELECT * FROM student 
	WHERE `name` LIKE '李%'
	
~~~





### 模糊查询

~~~mysql
# %号表示任意多个字符（0-INF）; _号表示一个字符

# 查询姓张的所有记录
select `name` from tableName where `name` like "张%"

#查询第3个字符为A的记录
select `name` from tableName where `name` like "__A%"
~~~



### 分组查询

~~~mysql
select max(field) as newname from tableName group by fieldName having expression；

# select 一般都接一个不作处理的字段，该字段就是 group by 后面所接的字段。
# select 一般对字段都会做一些处理，比如运算/调用函数
# 一般查询多个字段，且调用了统计/数学函数时，都需要分组


# 不利用group by 关键字也可以实现分组 ，建议使用group by 达到见名知意
~~~



将查询到的数据进行分组（group by），对每组数据进行过滤（having ）.

having不可以用where代替，WHERE 是对表中的原始数据进行过滤。

### 排序查询

**默认升序**

~~~mysql
-- 在指定表中 先以field01为准进行升/降序，（此时已根据第一次排序分成了若干组）再以field02为准在各组内进行排序
select * from tableName order by field01 asc|desc, field02 asc|desc;
~~~



### 分页查询

~~~mysql
# 从第 start + 1 行开始，取rows行记录。
select * from tableName order by field01 limit start, rows;

# 分页公式： limit (每页记录数/行数 * (第i页 - 1)), 每页记录数/行数
# 注意，limit 后不可接表达式
~~~

## 多表查询

### 笛卡尔集

默认情况下，多表查询是将表的所有数据进行组合。

设表1有10条记录，表2有5条记录，那么返回的结果是10 * 5 = 50 条记录。

且表中的列数是表1列数 + 表2列数，即并不会因为有重名的列而被过滤掉。

故返回的集合成为笛卡尔集。

为了避免笛卡尔集的出现，在多表查询时，至少得有（表数-  1）种过滤条件来进行查询。



### 自连接

在同一张表上可以在逻辑上再细化进行查询，于是可以使用别名的方式来自连接 同一张表。

~~~mysql
select tab1.field1 from t1 as tab1, t1 as tab2 where tab1.field2 = tab2.field2;
~~~

可以再对字段名使用别名，利于区分。

### 外连接

用于多表查询

`from t1 left/right jion t2 on 过滤条件`

左外连接，会显示左侧表的所有信息，即使两表无关联。

右外连接，显示右侧表所有信息，即使两表无关联。

左右外连接本质是一样的。

### 子查询

也称嵌套查询。

1. 单行子查询，只返回一条记录
2. 多行子查询，返回多条记录。常搭配关键字`in`

~~~mysql
-- 子查询的演示
-- 请思考：如何显示与SMITH同一部门的所有员工?
/*
	1. 先查询到 SMITH的部门号得到
	2. 把上面的select 语句当做一个子查询来使用
*/
SELECT deptno 
	FROM emp 
	WHERE ename = 'SMITH';

SELECT * 
	FROM emp
	WHERE deptno = (
		SELECT deptno 
		FROM emp 
		WHERE ename = 'SMITH'
	);

-- 查询和部门10的工作相同的雇员的
-- 名字、岗位、工资、部门号, 但是不含10号部门自己的雇员.
/*
	1. 查询到10号部门有哪些工作
	2. 把上面查询的结果当做子查询使用
*/
select distinct job 
	from emp 
	where deptno = 10;
	
select ename, job, sal, deptno
	from emp
	where job in (
		SELECT DISTINCT job 
		FROM emp 
		WHERE deptno = 10
	) and deptno != 10;

~~~

#### 子查询常用作临时表

主要作用就是分解复杂查询。

~~~mysql
-- 查询ecshop中各个类别中，价格最高的商品

-- 查询 商品表
-- 先得到 各个类别中，价格最高的商品 max + group by cat_id, 当做临时表
-- 把子查询当做一张临时表可以解决很多很多复杂的查询

select cat_id , max(shop_price) 
	from ecs_goods
	group by cat_id
	
-- 这个最后答案	
select goods_id, ecs_goods.cat_id, goods_name, shop_price 
	from (
		SELECT cat_id , MAX(shop_price) as max_price
		FROM ecs_goods
		GROUP BY cat_id
	) temp , ecs_goods
	where  temp.cat_id = ecs_goods.cat_id 
	and temp.max_price = ecs_goods.shop_price 

~~~



#### 子查询的运用时机

1. 子查询的结果是多列时，
    1. 要么在过滤时令条件一一对应这些列，
    2. 要么当作一张临时表用于多表查询
2. 结果是单列是，一般用于过滤条件。



### 合并查询

~~~~mysql
-- union all，合并两表记录，不会去重
-- union， 会去重

-- union all 就是将两个查询结果合并，不会去重
SELECT ename,sal,job FROM emp WHERE sal>2500
UNION ALL
SELECT ename,sal,job FROM emp WHERE job='MANAGER'

-- union  就是将两个查询结果合并，会去重
SELECT ename,sal,job FROM emp WHERE sal>2500
UNION 
SELECT ename,sal,job FROM emp WHERE job='MANAGER'
~~~~



## 查询的要点

拆分查询需求，写出表达式、子查询临时表等待。最后组合起来。



方法步骤： 先写简单的语句，再逐步添加过滤条件。

过滤条件：找出表之间的联系，即找出列的关联关系。

## 查询执行顺序

### 语法顺序

~~~mysql
SELECT ..., ..., ...(存在聚合函数)
FROM ... (LEFT / RIGHT OUTER)JOIN ... ON 多表查询的连接条件
(LEFT / RIGHT OUTER)JOIN ... ON 多表查询的连接条件
WHERE 不包含聚合函数的过滤条件
GROUP BY ..., ...
HAVING 包含聚合函数的过滤条件
ORDER BY ..., ...(ASC / DESC)
LIMIT ..., ...
~~~

### 执行顺序

`FROM --> ON --> (LEFT / RIGHT OUTER)JOIN --> WHERE --> GROUP BY --> HAVING --> SELECT --> DISTINCT --> ORDER BY --> LIMIT`





# 约束

## 主键

1. 主键非null，且不能重复
2. 每张表最多有一个主键
3. 可以存在复合主键（视作一个主键）
4. 主键声明时，可以在指定字段后面声明；也可以在创建表的最后声明。复合主键只能用第二种方法声明。
5. 每个表中往往都会定义一个主键（primary key）

## 非空

1. 字段后加 not null

## 唯一

1. 字段后加 unique，则记录不可重复
2. 表中可以存在多个唯一字段
3. 记录可以存放null，且记录中可以出现多条null。（视null为非值，故可以填多条null记录）
4. unique not null 效果类似与primary key



## 外键

用法：

~~~mysql
create table t1 (
	-- ...
    -- ...
    foreign key (field01) references mainTableName(field00)
)
~~~

1. 所关联的主表的列是primary key 或者unique
2. 从表添加的记录一定是在 所关联的字段 范围内的。（主表不存在该字段，则从表会添加失败）
3. 主表删除字段记录时，若从表有记录与此关联，则删除失败。（只有当 **从表**关于主表该记录 **的所有记录** 被删除后，主表的记录才能被删除）
4. 外键约束的字段可以为null
5. 当引擎为innodb时，才能使用外键
6. 一张表可存在多个外键

## check

5.7仅语法校验，实际不生效。





# 自增长

1. 语法： `auto_increment`
2. 每次增加1。默认从1开始，可以指定开始值`auto_increment = value`
3. 一般用于整型（int，double也可以但基本不用）
4. 使用自增长后，添加记录时，若指定值，则按指定为准。但下次执行自增长时，从该记录的指定值开始+1增长
5. 一般来说，使用自增长就不应再传入自行指定记录的值。





# 索引

## 使用

~~~mysql
-- 创建索引
-- 1. 使用alter
alter table t1 add primary key(field1);-- 创建主键索引
alter table t1 add unique [key/index] index_name(field1);-- 创建唯一索引
alter table t1 add index index_name(field1);-- 创建普通索引

-- 2. 使用create
create table unique index on t1 (field1);-- 创建唯一索引
create table index on t1 (field1);-- 创建普通索引
~~~

在创建表时，若字段被主键和唯一约束，那么就已经是索引了。主键索引名就是`PRIMARY`，唯一索引名是字段名。

~~~mysql
-- 删除索引
-- 1. 使用alter
alter table t1 drop primary key;-- 删除主键索引
alter table t1 drop index index_name;-- 删除唯一索引和普通索引

-- 2. 使用drop
drop table index index_name on t1; -- 删除唯一索引和普通索引
~~~

~~~mysql
-- 查询索引
show index from t1;
show keys from t1;
~~~

## 使用时机

1. 频繁查询某字段时
    1. 但是，如果唯一性太差，即使频繁查询，也不应该使用索引。如性别的查询
2. 更新频繁的字符，不应该使用索引
3. 无法过滤的字符不应该使用索引，即无法出现在where字句中的字段。





# 事务

事务用于保证数据的一致性，用于完成一组dml操作。



开启事务：`start transaction`（这是8以前），8以后采用`begin`。另类方式：`setautocommit = off`

设置保存点： `savepoint save_name`

回滚：`rollback to save_name` 回滚到指定保存点和 `rollback`回滚到事务开始时。

提交事务： `commit`

## 开启事务注意事项

1. 只能回滚，回滚后，该保存点以后的操作（事务）均被删除。
2. 不开启事务，是默认自动提交的
3. 事务机制在Innodb引擎下才能使用

## 开启事务的时机

1. 在每一个service方法调用时开启事务。一般一个业务方法对应一个事务


# 隔离

## 事务特性ACID

1. 原子性：事务应该是一个不可分割的单位。
2. 一致性：事务应该使数据库从一个一致性的状态转变为另一个一致性的状态，这与原子性是相关的。
3. 隔离性：各个事物之间应该互不影响。即在事务提交前，是不影响其他事务的操作的。
4. 持久性：事务一旦提交，那么数据就被持久化，不可更改.

只有一个事务时，只要保证原子性就可以达到一致性。

并发事务时，要保证原子性和隔离性，才可以达到一致性。

## 并发事务的问题

1. 脏读（dirty read）： 事务1读取到了事务2**尚未提交**的**修改、删除和添加操作**
2. 不可重复读（nonrepeatable read）：事务1在进行多次查询时，能够读取到事务2进行的**修改和删除操作**，因此每次查询返回不同的结果集合。
3. 幻读（phantom read）： 事务1在进行多次查询时，能够读取到事务2进行的**插入（添加）操作**，因此每次查询返回不同的结果集合。



## 隔离级别

未提交读（Read Uncommitted）：事务可以读取未提交的数据，也称作脏读（Dirty Read）。一般很少使用。

提交读（Read Committed）：是大多数 DBMS （如：Oracle, SQLServer）默认事务隔离。执行两次同一的查询却有不同的结果，也叫不可重复读。

可重复读（Repeable Read）：是 MySQL 默认事务隔离级别。能确保同一事务多次读取同一数据的结果是一致的。可以解决脏读的问题，但理论上无法解决幻读（Phantom Read）的问题。

可串行化（Serializable）：是最高的隔离级别。强制事务串行执行，会在读取的每一行数据上加锁，这样虽然能避免幻读的问题，但也可能导致大量的超时和锁争用的问题。很少会应用到这种级别，只有在非常需要确保数据的一致性且可以接受没有并发的应用场景下才会考虑。



|solation Level	|Dirty Read	|Non Repeatable Read| Phantom Read | 加锁读 |
|--|--|--|--|--|
|read Uncommitted|	Yes|	Yes|	Yes| 不加锁 |
|read Committed	|-|Yes	|Yes |不加锁 |
|repeatable Read	|-|-	|Yes|不加锁|
|serializable	|-	|-|	-  |	加锁 |



不可重复读（nonrepeatable read）的意思是：不能够重复查询该事务下的数据。读取到的数据会受到（其他客户端的）其他事务的影响。

可重复读（reoeatable read）的意思是：能够重复查询该事务下的数据，读取到的数据与其他事务无关。



## 隔离的相关语法

~~~mysql
-- 1. 查看隔离级别
select @@tx_isolation;# 查看当前会话（连接）的隔离级别
select @@global.tx_isolation;#查看全局的隔离级别

-- 2. 设置隔离级别 
set session transaction isolation level XXXX;#设置当前会话
set global transaction isolation level XXXX;#设置全局
~~~

说明：MySQL的session和global一般使用在终端，用来对配置进行暂时设置，当**数据库服务重启就会失效**。

1. session：当前会话，也就是当前连接立即生效。
2. global：全局，不包含当前连接，之后新获取的连接都会生效。

也可以修改配置文件。





# 存储引擎

## InnoDB

Supports transactions, row-level locking, and foreign keys。

1. 支持事务、外键
2. 支持行锁
3. 批量插入慢
4. 支持行锁，间接影响速度
5. 存在大量索引
6. 空间有64TB限制
7. 占用空间大

## MyISAM

1. 无内存限制
2. 批量插入快
3. 空间占用小
4. 支持表锁



## Memory

Hash based, stored in memory, useful for temporary tables。

1. 管理内存数据。
2. 有内存限制。
3. 批量插入快。
4. 支持表锁。
5. 默认使用哈希索引
6. 关闭mysql服务后，数据消失，但表结构依然存在



## 引擎选择

1. 不需要事务操作、只做基本的CRUD操作，那么选择MyISAM
2. 需要事务操作，选择InnoDB
3. 记录用户数据，且数据变动频繁，且该数据实时性强、无需保留数据时，选择Memory



## 相关语法

~~~mysql
-- 查询所有引擎
show Engines;

-- 修改表的引擎
alter table tableName engine engineName;

-- 创建表时，也可指定引擎
create table tableName () engine engineName;
~~~

# 视图

## 认识

1. 视图是基表的一个映射。相当于保存了一个指针，因此不管在哪里进行操作，都会互相影响。
2. 视图是虚拟的的表。
3. 视图文件就只是.frm，用于保存视图结构
4. 视图可以根据多个基表创建

## 语法

~~~mysql
-- 创建
create view viewName as (select ...);

-- 修改(alter 仅此一句)
alter view viewName as (select ...);

-- 显示结构
show create view viewName

-- 删除
drop view view_name;

~~~

## 视图好处

1. 安全，只提供必要的数据给外界
2. 性能，数据库的多表，可以通过视图结合在一起，避免了使用复杂的外连接（join on），且提高了效率。
3. 灵活，
    1. 将多张表组合在一起，视作新表。
    2. 旧表不适用，但依旧可以利用时，可以通过视图映射得到需要的结构、数据，进而再通过小改动而实现升级数据表。

# 统一的一些语法使用

~~~mysql
-- 显示某单位下的所有结构信息
show databases;# 显示所有数据库
show tables [from 数据库];# 默认显示当前数据库所有的表
show index/keys [from 数据库.]表;# 默认显示当前数据库，指定表的索引

-- 显示定义信息
show create database 数据库;#显示数据库
show create table [数据库.]表;#显示表，默认当前数据库。不可用from
show create view [数据库.]视图;#显示视图，默认当前数据库。不可用from

-- 显示表/视图结构信息
desc name; 

-- 删除数据库、表、视图
drop database/table/view name;#可以用数据库.name的方式，但不可以使用from

-- 创建结构
create database 数据库;
create table 表 ();
create view 视图 as (select ...);


-- 更改操作（涉及表、视图、索引）
alter table/view ...;
~~~



# 表关系

在数据库中，表之间可以有不同类型的关系，包括一对一、一对多和多对多。

- 一对一（1:1）关系：两个表中的每条记录都只与另一个表中的一条记录相关联。
- 一对多（1:N）关系：一个表中的每条记录都可以与另一个表中的多条记录相关联。
- 多对多（N:M）关系：两个表中的每条记录都可以与另一个表中的多条记录相关联。
