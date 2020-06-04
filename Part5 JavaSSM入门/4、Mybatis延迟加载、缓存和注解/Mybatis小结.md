## 1、配置文件

- 主配置文件约束：

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE configuration  
    PUBLIC "-//mybatis.org//DTD Config 3.0//EN"  
    "http://mybatis.org/dtd/mybatis-3-config.dtd">
  ```

- 主配置文件环境：

  ```xml
  <!-- Mybatis主配置文件 -->
  <configuration>
      <!-- 配置mysql环境 -->
      <environments default="mysql">
          <environment id="mysql">
              <!-- 配置事务类型 -->
              <transactionManager type="JDBC"></transactionManager>
              <!-- 配置数据源（连接池） -->
              <dataSource type="POOLED">
                  <!-- 配置连接数据库的四个基本信息 -->
                  <property name="driver" value="com.mysql.jdbc.Driver"/>
                  <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
                  <property name="username" value="root"/>
                  <property name="password" value="root"/>
              </dataSource>
          </environment>
      </environments>
  
      <!-- 指定映射配置文件的位置，映射配置文件指的是每个Dao独立的配置文件 -->
      <mappers>
          <mapper resource="" />
      </mappers>
  </configuration>
  ```

- 映射配置文件约束：

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE mapper  
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"  
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd"
  ```

- 映射配置文件根标签：

  ```xml
  <mapper namespace=""></mapper>
  ```

  

## 2、主配置文件下的标签

- `<mapper>`标签：

  - `resource`属性：xml配置的接口。
  - `class`属性：注解配置的接口。

- `<properties>`标签：

  -  通过指定外部属性文件指定信息。
    -  通过`${}`获取配置文件的信息。

- `<typeAliases>`标签：

  - 配置 domain 中类的别名。
  - `type`属性：所要配置别名的全类名。
  - `alias`属性：所要配置的别名。

- `<package>`标签：

  - `name`属性：所指向的包名。

  - 定义在`<configuration>`标签下：
    - 指定配置别名的包，该包下的所有实体类都会注册别名，并且类名就是别名。
    - 制定后在映射配置文件中，全类名都可以直接写别名。
  - 定义在`<mappers>`标签下：
    - 指定dao接口所在的包，当指定后就不需要写`<mapper>`标签了。

- 延迟加载的全局开关：

  ```xml
  <settings>
      <setting name="lazyLoadingEnabled" value="true"/>
      <setting name="aggressiveLazyLoading" value="true"/>
  </settings>
  ```

- 二级缓存的全局开关：

  ```xml
  <settings>
      <setting name="cacheEnabled" value="true"/>
  </settings>
  ```

  

## 3、映射配置文件下的标签

- `<mapper>`标签：

  - `namespace`属性：指定了要实现的是哪一个Dao接口。

- `<select>`标签：
  - 在`<mapper>`标签下，表示查询语句。
  - `id`属性：所要实现的接口中的方法名。
  - `resultType`：结果所要封装为的全类名。
  - `parameterType`：输入的参数的全类名。
  
- `<insert>`标签：

  - 在`<mapper>`标签下，表示插入语句。

  - 获取插入后的数据id：

    ```xml
    <selectKey keyProperty="id" keyColumn="id" resultType="Integer" order="AFTER">
        select last_insert_id();
    </selectKey>
    ```

- `<update>`标签：

  - 在`<mapper>`标签下，表示更新语句。

- `<delete>`标签：

  - 在`<mapper>`标签下，表示删除语句。

- `<resultMap>`标签：

  - 配置查询结果的列名和实体类属性名的对应关系。

  - `id`属性：进行sql操作的标签下可用`resultMap`属性指向该id，表示使用该映射关系。

  - `<id>`标签：表示主键。`property`属性表示封装为的实体类中的属性名，`column`属性表示数据库中的列名。

  - `<result>`标签：表示非主键。

  - `<association>`标签表示了实体类之间的关系。

    - 其中包含了封装类的`<id>`标签和`<result>`标签。一般用于一对一/多对一。

    - `property`属性同上。
    - `column`属性：表示输入的查询参数（数据库中的列）。如果没有使用`#{}`也可以不写。使用延迟加载必须写。
    - `javaType`属性：表示了所要封装成的实体类结果。
    - `select`属性：指向了所要进行延迟加载进行的查询，值为 Dao全类名 + 方法名。此时标签内不再写`property`属性和`column`属性。

  - `<collection>`属性：

    - 其中包含了封装类的`<id>`标签和`<result>`标签。一般用于一对多/多对多。
    - `property`属性同上。
    - `column`属性：表示输入的查询参数（数据库中的列）。如果没有使用`#{}`也可以不写。使用延迟加载必须写。
    - `ofType`属性：所要封装为集合的泛型。
    - `select`属性：指向了所要进行延迟加载进行的查询，值为 Dao全类名 + 方法名。此时标签内不再写`property`属性和`column`属性。

- `<if>`标签：

  - 在SQL语句中，实现动态sql。
  - `test`属性：表示判断的条件，为true时拼接`<if>`中的语句。

- `<where>`标签：

  - 其中可以包含`<if>`标签，不需要在sql语句中写`where 1=1`了。



