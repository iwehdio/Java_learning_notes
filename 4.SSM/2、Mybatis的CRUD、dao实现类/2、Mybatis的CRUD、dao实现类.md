## 1、Mybatis的CRUD

- 保存操作：

  1. 在`UserDao`接口下添加方法`void saveUser(User user)`。

  2. 在`UserDao.xml`下配置映射关系。并且获取保存后的id值。

     ```xml
     <!-- 保存用户 -->
     <insert id="saveUser" parameterType="cn.iwehdio.Domin.User">
         <!-- 获取插入后的数据id -->
         <selectKey keyProperty="id" keyColumn="id" resultType="Integer" order="AFTER">
             select last_insert_id();
         </selectKey>
         insert into user(username, address, sex, birthday)values(#{username}, #{address},#{sex},#{birthday});
     </insert>
     ```

  3. 为 Test 测试方法编写准备的结尾程序。

     `init()`方法，在测试方法运行前运行：

     ```java
     private InputStream in;
     private SqlSession sqlSession;
     private UserDao userDao;
     
     @Before
     public void init() throws IOException {
         //1.读取配置文件
         in = Resources.getResourceAsStream("SqlMapConfig.xml");
         //2.创建SqlSessionFactory工厂
         SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
         SqlSessionFactory factory = builder.build(in);
         //3.使用工厂生产SqlSession对象
         sqlSession = factory.openSession();
         //4.使用SqlSession创建Dao接口的代理对象
         userDao = sqlSession.getMapper(UserDao.class);
     }
     ```

     `destroy()`方法，在测试方法运行后运行：

     ```java
     @After
     public void destroy() throws IOException {
         //提交事务
         sqlSession.commit();
         //6.释放资源
         sqlSession.close();
         in.close();
     }
     ```

  4. 执行测试方法，完成保存。

     ```java
     @Test
     public void testsave() {
         User user = new User();
         user.setUsername("Bob");
         user.setAddress("JD");
         user.setSex("F");
         user.setBirthday(new Date());
         System.out.println(user);
         //5、执行保存方法
         userDao.saveUser(user);
         System.out.println(user);
     }
     ```

- 更新操作：

  1. 在`UserDao`接口下添加方法`void updateUser(User user)`。

  2. 在`UserDao.xml`下配置映射关系。

     ```xml
     <!-- 更新用户 -->
     <update id="updateUser" parameterType="cn.iwehdio.Domin.User">
         update user set username=#{username},address=#{address},sex=#{sex},birthday=#{birthday} where id=#{id};
     </update>
     ```

  3. 执行测试方法，完成更新。

     ```java
     @Test
     public void testupdate() {
         User user = new User();
         user.setId(49);
         user.setUsername("Danial");
         user.setAddress("JDE");
         user.setSex("M");
         user.setBirthday(new Date());
         //5、执行更新方法
         userDao.updateUser(user);
     }
     ```

- 删除操作：

  1. 在`UserDao`接口下添加方法`void deleteUser(Integer id)`。

    2. 在`UserDao.xml`下配置映射关系。

       ```xml
       <!-- 删除用户 -->
       <delete id="deleteUser" parameterType="Integer">
           delete from user where id=#{id};
       </delete>
       ```
       
  3. 执行测试方法，完成删除。

     ```java
     @Test
     public void testdelete() {
         //5、执行删除方法
         userDao.deleteUser(48);
     }
     ```

- 查询一个操作：

  1. 在`UserDao`接口下添加方法`User findById(Integer id)`。

    2. 在`UserDao.xml`下配置映射关系。

       ```xml
       <!-- id查询一个用户 -->
       <select id="findById" parameterType="Integer" resultType="cn.iwehdio.Domin.User">
           select * from user where id=#{id};
       </select>
       ```

  3. 执行测试方法，完成查询。

     ```java
     @Test
     public void testfindById() {
         //5、执行查询一个方法
         User user = userDao.findById(49);
         System.out.println(user);
     }
     ```

- 模糊查询操作：

  1. 在`UserDao`接口下添加方法`List<User> findByName(String username)`。

    2. 在`UserDao.xml`下配置映射关系。

       ```xml
       <!-- 名称模糊查询 -->
       <select id="findByName" parameterType="String" resultType="cn.iwehdio.Domin.User">
           select * from user where username like #{username};
       </select>
       ```

  3. 执行测试方法，完成查询。需要提供`%`作为占位符。

     ```java
     @Test
     public void testfindByName() {
         //5、执行模糊查询方法
         List<User> list = userDao.findByName("%王%");
         for(User user : list) {
             System.out.println(user);
         }
     }
     ```

- 聚合函数查询操作：

  1. 在`UserDao`接口下添加方法`int findTotal()`。

    2. 在`UserDao.xml`下配置映射关系。

       ```xml
       <!-- 聚合函数查询 -->
       <select id="findTotal" resultType="Integer">
           select count(id) from user;
       </select>
       ```

  3. 执行测试方法，完成查询。

     ```java
     @Test
     public void testfindTotal() {
         //5、执行聚合函数查询方法
         int count = userDao.findTotal();
         System.out.println(count);
     }
     ```



## 2、Mybatis中的参数

- `parameterType`参数：

  - 指传入的参数类型。

  - 可以是简单类型，也可以是 pojo 对象（实体类）或 OGNL 表达式。

    - OGNL表达式是对象图导航语言。
    - 通过对象中的取值方法来获取数据，写法上把 get 省略的。
    - 如将`user.getUsername()`变为`user.username`。

  - 如果查询条件是综合条件，比如将 User 对象放入另一个 QueryVo 对象中，使用 QueryVo 对象进行查询。

    - `UserDao`中添加`List<User> findUserByVo(QueryVo vo)`方法，且有：

      ```java
      public class QueryVo {
          private User user;
          public User getUser() {
              return user;
          }
          public void setUser(User user) {
              this.user = user;
          }
      }
      ```

    - 在`UserDao.xml`下配置映射关系。因为传入的类型是`QueryVo`，而查询条件是`User`下的`username`属性，所以不能直接写`username`而要使用OGNL表达式。

      ```xml
      <!-- 综合条件查询 -->
      <select id="findByVo" parameterType="cn.iwehdio.Domin.QueryVo" resultType="cn.iwehdio.Domin.User">
          select * from user where username like #{user.username};
      </select>
      ```

    - 执行测试方法，完成查询。

      ```java
      @Test
      public void testfindByVo() {
          QueryVo vo = new QueryVo();
          User user = new User();
          user.setUsername("%王%");
          vo.setUser(user);
          //5、执行综合查询方法
          List<User> list = userDao.findByVo(vo);
          for(User u : list) {
              System.out.println(u);
          }
      }
      ```

- `resultType`参数：

  - 指返回的参数类型。

  - 如果实体类中的属性与数据库中的列名不同，语句中的占位符`#{}`中的属性名也要进行调整。但是如果要封装为实体类返回，由于名称不同无法封装。

  - 解决办法一：在SQL语句中给列起别名，别名与实体类的属性名一致。

  - 解决办法二：配置查询结果的列名和实体类属性名的对应关系。

    ```xml
    <resultMap id="userMap" type="cn.iwehdio.Domin.User">
        <!-- 主键字段的对应 -->
        <id property="userId" column="id"></id>
        <!-- 非主键字段的对应 -->
        <result property="userName" column="username"></result>
    </resultMap>
    ```

    并且在所要进行查询时指定`resultMap`属性。

    ```xml
    <!-- 配置查询所有 -->
    <select id="findAll" resultMap="userMap">
        select * from user;
    </select>
    ```

## 3、Dao实现类的使用

- `UserDao`的实现类`UserDaoImpl`实现`findAll()`方法：

  ```java
  public class UserDaoImpl implements UserDao {
  
      private SqlSessionFactory factory;
  
      public UserDaoImpl(SqlSessionFactory factory) {
          this.factory = factory;
      }
  
      @Override
      public List<User> findAll() {
          SqlSession sqlSession = factory.openSession();
          List<User> users = sqlSession.selectList("cn.iwehdio.Dao.UserDao.findAll");
          sqlSession.close();
          return users;
      }
  }
  ```

- 测试类：

  ```java
  public class MybatisTest {
  
      private InputStream in;
      private UserDao userDao;
  
      @Before
      public void init() throws IOException {
          //1.读取配置文件
          in = Resources.getResourceAsStream("SqlMapConfig.xml");
          //2.创建SqlSessionFactory工厂
          SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
          SqlSessionFactory factory = builder.build(in);
          //3.使用工厂生产SqlSession对象
          userDao = new UserDaoImpl(factory);
      }
  
      @After
      public void destroy() throws IOException {
          in.close();
      }
  
      @Test
      public void testfindall() {
          //5.使用代理对象执行方法
          List<User> users = userDao.findAll();
          for(User user : users) {
              System.out.println(user);
          }
      }
  ```

- `PreparedStatement`对象的执行方法：

  - `execute()`：可以执行CRUD，返回值为一个布尔类型，为true表示有结果集。
  - `executeUpdate()`：只能执行CUD，不能进行查询，返回值为影响数据库记录的行数。
  - `executeQuery()`：只能进程查询，不能进行增删改，执行结果为封装的结果集`ResultSet`对象。

- `<properties>`标签：

  - 在主配置文件中,定义在`<configuration>`标签下，作为全局参数。
  - 在下方使用`#{键的名称}`调用全局参数。
  - 也可以用`rescource`属性指定外部配置文件，按照类路径的写法来写，必须存在类路径下。.
  - 也可以用`url`属性指定路径，使用`file:///+文件路径`。

- `<typeAliases>`标签：

  - 在主配置文件中,定义在`<configuration>`标签下，可以配置 domain 中类的别名。

  - 注册别名后，别名不区分大小写。

  - 使用：

    ```xml
    <typeAlias type="全类名" alias="别名"></typeAlias>
    ```

- `<package>`标签：

  - 在主配置文件中,定义在`<configuration>`标签下，指定配置别名的包，该包下的所有实体类都会注册别名，并且类名就是别名。

  - 使用：

    ```xml
    <package name="包名"></package>
    ```

  - 也可以定义在`<mappers>`标签下，指定dao接口所在的包，当指定后就不需要写`<mapper>`标签了。