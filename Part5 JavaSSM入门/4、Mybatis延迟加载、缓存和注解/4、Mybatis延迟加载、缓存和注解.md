## 1、延迟加载

- 在查询用户时，用户下的账户信息应该是什么时候使用什么时候查询（用户的账户比较多时）。

- 在查询账户时，账户的所属用户信息，应该随着账户查询一起查询（账户的用户只有一个）。

- 延迟加载就是，在真正使用数据时才发起查询，不用的时候不加载，是按需加载。

- 立即加载就是，不管是否使用，一调用方法马上发起查询。

- 一对多和多对多一般是延迟加载。

- 多对一和一对一一般是立即加载。

- 在全局配置文件中开启延迟加载的全局开关：

  ```xml
  <settings>
      <setting name="lazyLoadingEnabled" value="true"/>
      <setting name="aggressiveLazyLoading" value="true"/>
  </settings>
  ```

  

- 一对一延迟加载的配置：

  - 在`AccountDao.xml`中：

    ```xml
    <resultMap id="accountUserMap" type="cn.iwehdio.Domin.Account">
        <id property="id" column="id"></id>
        <result property="uid" column="uid"></result>
        <result property="money" column="money"></result>
    
        <association property="user" column="uid" javaType="cn.iwehdio.Domin.User" select="cn.iwehdio.Dao.UserDao.findById">
    
        </association>
    </resultMap>
    <!-- 配置查询所有，id是UserDao中的方法名称, 其中包含的是sql语句 -->
    <select id="findAll" resultMap="accountUserMap">
        select * from account;
    </select>
    ```

  - `<association>`标签配置延迟加载，其中的`select`属性指向了所要进行延迟加载进行的查询，值为 Dao全类名 + 方法名。`column`指向了查询的输入参数。

- 一对多延迟加载的配置：

  - 在`UserDao.xml`中：

    ```xml
    <resultMap id="userAccountMap" type="cn.iwehdio.Domin.User">
        <id property="id" column="id"></id>
        <id property="username" column="username"></id>
        <id property="address" column="address"></id>
        <id property="sex" column="sex"></id>
        <id property="birthday" column="birthday"></id>
        <collection property="accounts" ofType="cn.iwehdio.Domin.Account" select="cn.iwehdio.Dao.AccountDao" column="id"></collection>
    </resultMap>
    
    
    <!-- 配置查询所有，id是UserDao中的方法名称, 其中包含的是sql语句 -->
    <select id="findAll" resultMap="userMap">
        select * from user;
    </select>
    ```

    - 使用 `<collection>`标签中的`select`属性指向所要延迟加载的方法，`column`属性为参数输入。

  - 在`AccountDao.xml`中：

    ```xml
    <select id="findAccountByUid" resultType="cn.iwehdio.Domin.Account">
        select * from account where uid = #{uid};
    </select>
    ```

## 2、缓存

- 缓存是存在内存中的临时数据，适用于经常查询且不经常改变的，数据的正确与否对最终结果影响不大时。经常改变的数据不适合用缓存。

- Mybatis中的一级缓存：

  - 指的是Mybatis中`SqlSession`对象的缓存。
  - 当执行查询后，结果会被同时存入SqlSession提供的区域，该区域的结构是一个 Map。
  - 再次查询同样的数据时，Mybatis会先去SqlSession中查询。
  - 当SqlSession对象消失时，Mybatis的一级缓存也就消失了。
  - 比如连续进行两次同样的查询时，第一次从数据库中查询，第二次从缓存中查询。但是如果这之间使用了不同的SqlSession对象则不行。
  - 也可以使用SqlSession的`clearCache()`方法清除缓存。

- Mybatis中触发清空一级缓存的情况：

  - 一级缓存是SqlSession范围的缓存，当调用SqlSession的修改、添加、删除、commit()和close()等方法时，就会触发一级缓存的清空。

- Mybatis的二级缓存：

  - 指的是Mybatis中`SqlSessionFactory`的缓存。

  - 同一个SqlSessionFactory创建的SqlSession共享二级缓存。

  - 二级缓存的使用：

    1. 让Mybatis支持二级缓存，在主配置文件中配置。

       ```xml
       <settings>
           <setting name="cacheEnabled" value="true"/>
       </settings>
       ```

    2. 在当前的映射文件支持二级缓存。添加`<cache/>`标签。

    3. 让当前操作支持二级缓存，在select标签中配置`<userCache>`为true。

  - 二级缓存中存放的是数据而不是对象，两次从缓存中获取的不是同一个对象。

## 3、注解开发

- 注解配置的是映射配置文件，主配置文件不能省略。

- Mybatis中针对CRUD有四个注解：

  - `@Select(sql语句)`。
  - `@Insert(sql语句)。`
  - `@Update(sql语句)。`
  - `@Delete(sql语句)。`

- 如果已经使用注解开发，resources下 与Dao接口的对应路径中不能存在 xml 配置文件，否则会报错。

- 注解创建实体类属性与数据库列的对应关系：

  - 如果属性名与列名不同，可以使用`@Results`注解。

  - `@Results`注解使用方法：

    ```java
    @Results(id="userMap',value={
        @Result(id=true,column="id",property="id"),
        @Result(column="name",property="uname"),
    })
    ```

    - `@Results`下的id属性可以用来索引这个映射关系。
    - `@Result`下的id属性指定该属性是否是主键，column属性指定数据库列名，property属性指定实体类属性名。
    - 可以使用`@ResultMap("userMap")`注解复用这个映射。

- 一对一/多对一的注解配置：

  - 使用`@Result`下的`@One`注解。

  - `@One`注解使用方法：

    ```java
    @Results(id="userMap',value={
        @Result(id=true,column="id",property="id"),
        @Result(id=true,column="uid",property="uid"),
        @Result(property="user",column="uid",one=@One(select="cn.iwehdio.dao.UserDao.findById",fetchType=FetchType.EAGER))
    })
    ```

    - 一对一/多对一查询时，所关联的查询使用`@Result`，property属性指定封装的返回值，column属性指定查询的输入参数。
    - `@One`注解表示是一对一/多对一查询，select属性是所要进行查询依据的全类名+方法名，fetchType属性为LAZY表示延迟加载，为EAGER表示同步加载。
    - 一般一对一/多对一查询用同步加载，一对多/多对多用延迟加载。

- 一对多/多对多的注解配置：

  - 使用`@Result`下的`@Many`注解。
  - `@Many`注解使用方法：与`@one`类似。fetchType属性设置为LAZY。

- 注解开发使用二级缓存：

  - 在主配置文件中开启全局设置开启二级缓存。
  - 在Dao接口上添加注解`@CacheNameSpace(blocking=true)`。