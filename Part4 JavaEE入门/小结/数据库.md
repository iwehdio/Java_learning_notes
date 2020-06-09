## 1、MySQL

- DDL：
  - `create database 数据库名`：创建数据库。
  - `drop database 数据库名`：删除数据库。
  - `create table 表名(列名1 数据类型1,列名n 数据类型n)`：创建表。
  - `alter table 表名 add 列名 数据类型`：添加一列。
  - `alter table 表名 drop 列名`：删除一列。
  - `drop table 表名`：删除表。
- DML：
  - `insert into 表名(列名1, 列名2) values(值1, 值2)`：向列1、列2中分别添加数据。
  - `delete from 表名 where 删除条件`：对于某个表，删除所有满足删除条件的数据（行）
  - `update 表名 set 列名1=值1,列名2=值2 where 修改条件`：对于某个表，修改所有满足修改条件的数据，按列进行赋值。
- DQL：
  - 查询：
    - `select * from 表名`：查询表中的所有值。
    - `select 字段列表 from 表名`：字段列表是指逗号分隔的列名。
    - `select distinct 字段列表 from 表名`  ：distinct 字段去除结果集中的重复值。
  - 条件查询：
    - `select * from 表名 where 条件列表`：查询所有符合条件的值。
  - 模糊查询：
    - `select * from 表名 where 条件列表 like 查询字段`：查询符合某个条件的值中，符合模糊查询字段的值。
    - _ ：下划线表示单个任意字符；% ：百分号表示多个任意字符。
  - 排序查询：
    - `select * from 表名 order by 排序字段1 排序方式1,排序字段2 排序方式2`：排序语法。排序字段就是列名，默认升序。ASC——升序；DESC——降序。
  - 聚合函数：
    - `select 聚合函数(列名) from 表名`：聚合函数按列计算。
  - 分组查询：
    - `select 分组字段,聚合函数(列名) from 表名 group by 分组字段`：分组字段是按某个列的值进行分组，然后求分组后某个列的聚合函数。
    - `select 分组字段,聚合函数(列名) from 表名 where 条件字段 group by 分组字段`：在分组之前限定，不满足条件则不参与分组。where 后的条件字段不能跟聚合函数。
    - `select 分组字段,聚合函数(列名) from 表名 group by 分组字段 having 条件字段`：在分组之后限定，不满足的结果不会被显示出来。having 后的条件字段可以跟聚合函数。
  - 分页查询：
    - `select * from 表名 limit 开始的索引,每页查询的条数`：显示查询到的从开始索引开始的，与查询条数相等的结果。
- 约束：
  - 主键约束：`primary key`。
  - 非空约束：`not null`。
  - 唯一约束：`unique`。
  - 自动增长：`auto_increment`。
  - 外键约束：`constraint 外键名称 foreign key (外键列名称) references 主表名称(主表列名称)`。
- 多表查询：
  - 内连接（查询的是交集）：
    - `select 列名列表 from 表1,表2 where 条件字段`：隐式。
    - `select 列名列表 from 表名1 inner join 表2 on 条件字段`：显式。
  - 外连接（查询的是一方的所有与另一方的交集）：
    - `select 列名列表 from 表1 left outer join 表2 on 条件字段`：左外连接查询的是左表（表1）的所有数据以及其（与表2）交集部分。
    - `select 列名列表 from 表1 right outer join 表2 on 条件字段`：右外连接查询的是右表（表2）的所有数据以及其（与表1）交集部分。
  - 子查询。



## 2、JDBC

- 注册驱动。

  ```java
  Class.forName("com.mysql.jdbc.Driver");
  ```

- 获取数据库连接对象。

  ```java
  Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/database", "用户名", "密码");
  ```

- `Connection`：数据库连接对象。

  ```java
  //获取执行 sql 的对象
  Statement creatStatement();
  PreparedStatement preparedStatement(String sql);
  //开启、提交、回滚事务
  void setAutoCommit(boolean autoCommit);//为false时开启
  void commit();
  void rollback()
  ```

- `Statement`：执行 sql 的对象。

- `PreparedStatement`：执行预编译 sql 的对象。

  ```java
  boolean execute(String sql); //执行任意 sql 语句
  int excuteUpdate(String sql);//执行增删改语句
  ResultSet excuteQuery(String sql);//执行查询语句
  void SetXxx(?的编号位置, ?的赋值内容);//赋值
  ```

- `ResultSet`：结果集对象。

  ```java
  boolean next();//下一个
  Xxx getXxx(参数);//传入列的编号或名称
  ```

- 事务：

  - `void setAutoCommit(boolean autoCommit)`：为 false 时为开启事务。
  - `void commit()`：提交事务。
  - `void rollback()`：回滚事务。

- 数据库连接池标准接口：`DataSource`。

  - 获取连接的方法：`getConnection()`。
  - 归还连接的方法：``Connection.close()` 。

- 获取Druid数据库连接池对象：

  - 通过工厂类`DruidDataSourceFactory`下的方法`createDataSource(Properties对象)`方法。

- Spring JDBC下的JdbcTempla：

  - 创建 JDBC Template 对象。依赖于数据源 DataSource 。

    `JdbcTemplate template = new JdbcTemplate(ds)`。

  - `update()`：执行DML语句，增删改。

  - `queryForMap()`：查询结果，将结果集封装为 map 集合。将列名作为 key，值作为 value，查询的结果集长度只能为1。

  - `queryForList()`：查询结果，将结果集封装为 List 集合。将每一条结果封装为 map 集合，再将 map 集合装载到 list 集合。

  - 以上方法括号中传入的参数，第一个参数为 sql 语句，之后的参数依次为预编译 sql 语句中问号的值。

  - `query()`：查询结果，将结果集封装为 JavaBean 对象。

    - 第一个参数为 sql 语句，第二个为 JavaBean 对象的封装实现类 RowMapper，可以使用匿名类自定义，一般使用 `BeanPropertyPowMapper`实现类自动封装。
    - 对于指定类型的封装，格式为 `new BeanPropertyRowMapper<类型>(类型.class)`。
    - 之后的参数依次为预编译 sql 语句中问号的值。

  - `queryForObject()`：查询结果，将结果集封装为对象返回。

    - 一般用于聚合函数的查询。
    - 第一个参数为 sql 语句，第二个为 返回类型.class。

