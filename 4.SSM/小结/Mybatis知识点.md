## Mybatis

### 1、基础

- 入门案例使用流程。
- 配置文件配置。
- 注解配置。
- 入门案例的执行原理。
- 自定义类实现Mybatis。



### 2、CRUD

- 提取Mybatis相关资源的初始化和销毁方法。
- 插入`<insert>`、更新`<update>`、删除`<delete>`、查询`<select>`、查询主键`<selectKey>`。
- 方法名`id`、输入参数类型`parameterType`、返回参数类型`resultType`/`resultMap`。
- `<resultMap>`：`id`唯一标识、`type`返回类型、`<id>`主键、`<result>`非主键、`property`对象中的属性、`column`数据库中的字段。
- 使用Dao实现类。
- 主配置文件：全局参数`<properties>`、类别名`<typeAliases>`、包别名`<package>`。



### 3、多表

- Mybatis连接池。
- Mybatis中的事务。
- 动态SQL：
  - `<if>`条件，按`test`中结果判断。
  - `<where>`可包裹`<if>`，自动拼接SQL中的where。
- 一对一：
  - 数据库中：一对一外键连接。
  - 实体类中：一个类聚合到另一个中。
  - 聚合另一个对象在`<resultMap>`中的配置：
    - 单个对象聚合`<association>`。
    - 属性名`property`。
    - 外键列`column`。
    - 对象类型`javaType`。
    - 其中又包含了`<id>`和`<result>`。
- 一对多：
  - 数据库中：一对多外键连接。
  - 实体类中：“多”的类聚合到“一”的类中，并以集合的形式组织。
  - 聚合另一个对象的集合在`<resultMap>`中的配置：
    - 集合对象聚合`<collection>`。
    - 集合属性名`property`。
    - 外键列`column`，可省略因为必是“一”的一方的主键。
    - 集合中元素类型`ofType`。
    - 其中又包含了`<id>`和`<result>`。
- 多对多：
  - 数据库中：中间表外键连接。
  - 实体类中：两个类都聚合了一个对方的集合。
  - `<resultMap>`中的配置类似一对多。



### 4、延迟加载、缓存、注解

- “x对多”一般延迟加载，“x对一”一般立即加载。
- 延迟加载配置：
  - 全局开关。
  - `<association>`/`<collection>`中使用`column`输入和`select`延迟查询方法。
- 一级缓存：原理、触发清除。
- 二级缓存：配置。
- 注解开发：
  - CRUD：`@Select(sql)`、`@Insert(sql)`、`@Update(sql)`、`@Delete(sql)`。
  - 结果集：`@Results()`，其中`@Result`。
  - “x对一”：`@One`。
  - “x对多”：`@Many`。