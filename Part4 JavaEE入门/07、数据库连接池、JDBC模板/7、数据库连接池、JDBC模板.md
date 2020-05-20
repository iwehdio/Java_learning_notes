# 30、数据库连接池、JDBC模板

## 1、数据库连接池

> 是一个容器（集合），存放数据库连接。

- 当系统初始化好后，容器会申请一些连接对象，当用户访问数据库时，从容器中获取连接对象，访问完后将对象归还给容器。

- 优点：节约资源、用户访问高效。

- 实现：

  - 标准接口：`DataSource`。
    - 获取连接的方法：`getConnection()`。
    - 归还连接的方法：如果连接对象 Connect 是从连接池中获取的，那么调用 `Connection.close()` 方法只会归还连接到容器中，不会关闭连接。

- C3P0 数据库连接池：

  - 步骤：

    1. 导入 c3p0 jar 包及其依赖以及数据库驱动，到 libs 目录下。
    2. 定义配置文件。名称必须为 c3p0.properties 或 c3p0-config.xml ；路径直接放在 src 目录下。
    3. 创建数据库连接池对象 `ComboPooledDataSource(配置名称)`。如果不传入配置名称，则选择默认配置，对应于配置文件中的 default 。传入配置名称，对应于配置文件中的 name 属性。
    4. 获取连接 `getConnection()`。

  - 配置文件中包括：

    1. 连接参数：数据库驱动、url 、用户名和密码。
    2. 连接池参数：初始化申请的连接数量、最大连接数量（同时访问的连接数量）和超时时间等。
    3. 不同的配置名称，默认 default ，或者指定 name 属性。

  - 实现：

    ```java
    DataSource ds = new ComboPooledDataSource();
    Connection conn = ds.getConnection();
    ```

- Druid 数据库连接池：

  - 步骤：

    1. 导入 jar 包。
    2. 定义配置文件。可以为任意目录下的任意名称的 .properties 文件。需要手动加载。
    3. 加载配置文件，创建 Properties 对象，加载指定类的 src 目录下的指定文件名称。
    4. 获取数据库连接池对象：通过工厂类`DruidDataSourceFactory`下的方法`createDataSource(Properties对象)`方法获取。
    5. 获取连接 `getConnection()`。

  - 配置文件中的内容与 c3p0 类似。

  - 定义工具类：

    1. 定义类。
    2. 提供静态代码块加载配置文件，初始化连接池对象。
    3. 提供方法：
       - 获取连接方法：通过数据库连接池获取连接。
       - 归还连接。
       - 获取连接池。

    - 实现：

      ```java
      public class PoolUtils {
          private static DataSource ds;
      	//连接数据库
          static {
              try {
                  Properties pro = new Properties();
                  pro.load(PoolUtils.class.getClassLoader().getResourceAsStream("druid.properties"));
                  ds = DruidDataSourceFactory.createDataSource(pro);
              } catch (IOException e) {
                  e.printStackTrace();
              } catch (Exception e) {
                  e.printStackTrace();
              }
          }
      	//获取连接
          public static Connection getConnection() throws SQLException {
              return ds.getConnection();
          }
      	//获取连接池
          public static DataSource getDataSource(){
              return ds;
          }
      	//归还连接
          public static void close(Statement stmt, Connection conn){
              close(stmt, conn, null);
          }
          public static void close(Statement stmt, Connection conn, ResultSet rs){
              if(rs != null){
                  try {
                      rs.close();
                  } catch (SQLException e) {
                      e.printStackTrace();
                  }
              }
              if(stmt != null){
                  try {
                      stmt.close();
                  } catch (SQLException e) {
                      e.printStackTrace();
                  }
              }
              if(conn != null){
                  try {
                      conn.close();
                  } catch (SQLException e) {
                      e.printStackTrace();
                  }
              }
          }
      }
      ```

  - 使用工具类：

    ```java
    Connection conn = null;
    PreparedStatement pstmt = null;
    try {
        conn = PoolUtils.getConnection();
        String sql = "insert into userlogin value(? , ?, ?)";
        pstmt = conn.prepareStatement(sql);
        pstmt.setInt(1,6);
        pstmt.setString(2,"user");
        pstmt.setString(3,"pass");
        pstmt.executeUpdate();
    } catch (SQLException e) {
        e.printStackTrace();
    } finally {
        PoolUtils.close(pstmt, conn);
    }
    ```

## 2、JDBC Template

- Spring JDBC：Spring框架提供的JDBC的简单封装（JDBC Template）。

- 步骤：

  1. 导入 jar 包。

  2. 创建 JDBC Template 对象。依赖于数据源 DataSource 。

     `JdbcTemplate template = new JdbcTemplate(ds)`。

  3. 调用 JdbcTemplate 的方法完成 sql 操作。

     - `update()`：执行DML语句，增删改。
     - `queryForMap()`：查询结果，将结果集封装为 map 集合。将列名作为 key，值作为 value，查询的结果集长度只能为1。
     - `queryForList()`：查询结果，将结果集封装为 List 集合。将每一条结果封装为 map 集合，再将 map 集合装载到 list 集合。
     - 以上方法括号中传入的参数，第一个参数为 sql 语句，之后的参数依次为预编译 sql 语句中问号的值。
     - `query()`：查询结果，将结果集封装为 JavaBean 对象。
       - 第一个参数为 sql 语句，第二个为 JavaBean 对象的封装实现类 RowMapper，可以使用匿名类自定义，一般使用 `BeanPropertyPowMapper`实现类自动封装。
       - 对于指定类型的封装，格式为 `new BeanPropertyRowMapper<类型>(类型.class)`。
       - 之后的参数依次为预编译 sql 语句中问号的值。
     - `queryForObject()`：查询结果，将结果集封装为对象。
       - 一般用于聚合函数的查询。
       - 第一个参数为 sql 语句，第二个为 返回类型.class。

  4. 不需要手动归还连接，Template 类中已封装。

- Junit :单元测试，可以让方法独立执行。

  需要先将 junit 加入工作空间。

  ```java
  @Test
  public void test1(){
      //所要测试的内容
  }
  ```

  可以通过看控制台中，执行单元测试的方法的颜色判断方法是否出错。

- 实现：

  ```java
  public class JDBCTemplate {
  	//获取JdbcTemplate对象
      private JdbcTemplate template = new JdbcTemplate(PoolUtils.getDataSource());
  	//.update()方法
      @Test
      public void test1(){
          String sql = "update emp set salary = 100 where id = 1001";
          template.update(sql);
      }
      //.update()方法，预编译语句
      @Test
      public void test2(){
          String sql = "insert into emp(id,ename) value(?,?)";
          template.update(sql, 1015,"AA");
      }
      //.queryForMap()方法，预编译语句
      @Test
      public void test3(){
          String sql = "select * from emp where id = ?";
          Map<String, Object> map = template.queryForMap(sql, 1001);
          System.out.println(map);
      }
      //.queryForList()方法
      @Test
      public void test4(){
          String sql = "select * from emp";
          List<Map<String, Object>> list = template.queryForList(sql);
          for(Map<String, Object> ObjMap : list){
              System.out.println(ObjMap);
          }
      }
      //.query()方法，预编译语句
      @Test
      public void Test5(){
          String sql = "select * from emp where id between ? and ?";
          List<Emp> list = template.query(sql, new BeanPropertyRowMapper<Emp>(Emp.class), 1001,1010);
          for(Emp emp : list){
              System.out.println(emp);
          }
      }
      //.queryForObject()方法
      @Test
      public void Test6(){
          String sql = "select count(id) from emp";
          Long total = template.queryForObject(sql, Long.class);
          System.out.println(total);
      }
  }
  ```

  

