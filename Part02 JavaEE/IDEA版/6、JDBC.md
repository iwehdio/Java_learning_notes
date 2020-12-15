# 29、JDBC

## 1、JDBC

> Java DataBase Connectivity：Java 语言操作数据库。

- JDBC：定义了操作所有关系型数据库的规则（接口）。

- 数据库驱动：JDBC的实现类由各个不同的数据库厂商实现，成为驱动 jar 包的形式。

- 步骤：

  1. 导入驱动 jar 包。
     - 复制 mysql-connector-java-5.1.37-bin.jar 到项目 libs 目录下。
     - 右键 > Add As Library。
  2. 注册驱动。
  3. 获取数据库连接对象（Connection）。
  4. 定义 sql 。
  5. 获取执行 sql 语句的对象（Statement）。
  6. 执行 sql ，接收返回的结果。
  7. 处理结果。
  8. 释放资源。

- 实现：

  ```java
  public static void main(String[] args) throws ClassNotFoundException, SQLException {
      //1、导入驱动
      //2、注册驱动
      Class.forName("com.mysql.jdbc.Driver");
      //3、获取数据库连接对象
      Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/db1", "root", "root");
      //4、定义sql语句
      String sql = "update users set uname = 500 where uid = 1";
      //5、获取执行sql的对象Statement
      Statement stmt = conn.createStatement();
      //6、执行sql
      int count = stmt.executeUpdate(sql);
      //7、处理结果
      System.out.println(count);
      //8、释放资源
      stmt.close();
      conn.close();
  }
  ```

- 考虑异常处理的实现：

  ```java
  public static void main(String[] args) {
      Statement stmt = null;		//在try的作用域外声明，这样才能在catch中调用
      Connection conn = null;
      try {
          Class.forName("com.mysql.jdbc.Driver");
          String sql = "insert into users values(null,'user2')";
          conn = DriverManager.getConnection("jdbc:mysql:///db1","root","root");
          stmt = conn.createStatement();
          int count = stmt.executeUpdate(sql);
          System.out.println(count);		//返回值判断是否修改成功
          if(count > 0){
              System.out.println("成功");
          } else {
              System.out.println("失败");
          }
      } catch (SQLException e) {		//处理异常
          e.printStackTrace();
      } catch (ClassNotFoundException e) {
          e.printStackTrace();
      } finally {
          if(stmt != null){		//判断是否为null，为null时无法调用
              try {
                  stmt.close();
              } catch (SQLException e) {
                  e.printStackTrace();
              }
          }
          if(conn != null){
              try {
                  stmt.close();
              } catch (SQLException e) {
                  e.printStackTrace();
              }
          }
      }
  
  }
  ```

- 解释各个对象：

  - `DiverManager`：驱动管理对象。
    1. `static void registerDriver(Driver driver)`：注册驱动。
       - 写代码时使用：`Class.forName("com.mysql.jdbc.Driver")`。实际上是在这个类下的静态代码块注册了驱动。
       - mysql.jar 包在 5.1 版本后，可以自动注册。
    2. `static Connection getConnection(String url, String user, String password)`：获取数据库连接。
       - url 指定连接的路径，语法为`jdbc:mysql://ip地址(域名):端口号/数据库名`。
       - ip地址默认本机，端口号默认3306。但是需要用 / 代替。
    
  - `Connection`：数据库连接对象。
    1. 获取执行 sql 的对象。
       - `Statement creatStatement()`。
       - `PreparedStatement preparedStatement(String sql)`。
    2. 管理事务。
       - `void setAutoCommit(boolean autoCommit)`：为 false 时为开启事务。
       - `void commit()`：提交事务。
       - `void rollback()`：回滚事务。
    
  - `Statement`：执行 sql 的对象。用于执行 sql 语句。
    
    - `boolean execute(String sql)`：执行任意 sql 语句。
    - `int excuteUpdate(String sql)`：执行 DML（增删改表中数据）、DDL（操作库和表） 语句。执行 DML时的返回值是 sql 语句所影响的行数。可以用来判断语句是否执行成功。执行DDL时无返回值。
    - `ResultSet excuteQuery(String sql)`：执行 DQL（查询）语句，返回结果集对象。
    
  - `ResultSet`：结果集对象。封装查询的结果。
  
    - 游标：指向表的中的一行。默认指向标题行即第 0 行。
  
    - `boolean next()`：游标向下移动一行，返回当前行是否是最后一行末尾（是否有数据）。
  
    - `Xxx getXxx(参数)`：获取数据。Xxx代表数据类型，即所要获取的列的数据类型。参数可以为接收 int 类型（列的编号，从1开始）或 String 类型（列的名称）。
  
    - 遍历结果集：
  
      1. 游标向下移动一行；
      2. 判断是否有数据；
      3. 获取数据。
  
      ```java
      ResultSet rs = stmt.excuteQuery(sql);
      while(rs.next()){
          int id = rs.getInt(1);
          String name = rs.getString("name");
          double balance = rs.getDouble(3);
          System.out.println(id + name + balance);
      }
      ```
  
  - `PreparedStatement`：执行 sql 的对象。
  
    - SQL 注入问题：
  
      - 在拼接 sql 时，有一些 sql 的特殊关键字参与字符串的拼接，会造成安全性问题。
      -  例如：`select * from user where username = abc and password = 'a' or 'a' = 'a'`，由于后半句 or 以后的部分恒成立，则返回值一定为 true 。
  
    - Statement 的子接口，表示预编译 sql 语句，参数使用问号（？）作为占位符。
  
    - 使用步骤：
  
      1. 使用问号占位符定义预编译的 sql 语句。
      2. 使用 `Connection.prepareStatement(String sql)`方法传入预编译 sql 语句，获取 PrepareStatement对象。
      3. 使用 `SetXxx(?的编号位置, ?的赋值内容)`给问号赋值。问号的编号从1开始，Xxx表示赋值的数据类型。
      4. 执行 sql，不需要再次传入 sql 语句。
  
      ```java
      Connection conn = JDBCUtils.getConnection();
      String sql = "select * from userlogin where username = ? and password = ?";
      PreparedStatement  pstmt = conn.prepareStatement(sql);
      pstmt.setString(1, username);
      pstmt.setString(2, password);
      ResultSet rs = pstmt.executeQuery();
      ```

## 2、JDBC工具类

> 抽取重复代码，简化书写。

- 功能分析：

  1. 注册驱动也需要抽取。
  2. 抽取一个方法获取连接对象。
     - 不想传参，又要保证方法的通用性，使用 .properties 配置文件。
     - 配置文件的读取只需要一次（在类加载时），使用静态代码块实现。
     - 使用 Properties 集合类加载配置文件。
  3. 抽取一个方法用于释放资源。
     - 需要直接被类名调用，需要是静态方法。

- 实现：

  ```java
  public class JDBCUtils{
  	//静态代码块只能使用静态变量
      private static String url;
      private static String user;
      private static String password;
      private static String driver;
  	//只需在类加载时运行一次
      static {
          try {
         		//使用Properties配置文件
              Properties pro = new Properties();
              //使用 ClassLoader 找到某个类所属的字节码.class文件（JDBCUtils.class）所属的 src 目录下的，某个文件（jdbc.properties）的绝对路径
              ClassLoader classLoader = JDBCUtils.class.getClassLoader();
              URL res = classLoader.getResource("jdbc.properties");
              String path = res.getPath();
              pro.load(new FileReader(path));
              
              url = pro.getProperty("url");
              user = pro.getProperty("user");
              password = pro.getProperty("password");
              driver = pro.getProperty("driver");
              Class.forName(driver);
          } catch (ClassNotFoundException e) {
              e.printStackTrace();
          } catch (FileNotFoundException e) {
              e.printStackTrace();
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  	//连接资源
      public static Connection getConnection() throws SQLException {
          return DriverManager.getConnection(url,user,password);
      }
  
      //释放资源的方法重载，适用于修改和查询操作中，需不需要ResultSet对象的区别
      public static void close(Statement stmt, Connection conn){
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

## 3、JDBC控制事务

- `void setAutoCommit(boolean autoCommit)`：为 false 时为开启事务。
- `void commit()`：提交事务。
- `void rollback()`：回滚事务。
- 事务在创建 Connection 对象后，执行具体的操作之前开启。
- 事务在所有具体的操作完成，try 的最后提交。
- 事务的回滚在 catch 中，抓取的异常应为最顶层的 Exception，回滚前判断 conn 是否为空。

```java
Connection conn = null;
try{
    conn = JDBCUtils.getConnection();
    conn.setAutoCommit(false);		//开启事务
    /*
    	具体操作
    */
    conn.commit();				//提交事务
} catch(Exception e){
    if(conn != null){
        conn.rollback();		//回滚事务
    } catch(SQLException e1){
        e1.printStackTrace();
    }
    e.printStackTrace();
} finally{
    //释放资源
}
```



## 4、实例

- 查询练习：定义一个方法，查询 emp 表中的数据将其封装为对象，然后装载集合并返回。

  1. 定义 Emp 类。
  2. 定义方法 `public List<Emp> findAll(){}`。
  3. 实现方法 `select * from emp`。

  - 实现：

    ```java
    public class Emp {		//Emp类
        private int id;
        private String ename;
        private int job_id;
        private int mgr;
        private Date joindate;
        private double bouns;
        private double salary;
        private int dept_id;
        /*
        	重写的 get 、set 和 toString 方法
        */
    }
    //主方法
    public static void main(String[] args) {
        List<Emp> list = new Demo2().findAll();
        System.out.println(list);
    }
    //查询方法
    public List<Emp> findAll(){
    	//变量抽取
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;
        List<Emp> list = null;
    
        try {
            Class.forName("com.mysql.jdbc.Driver");
            conn = DriverManager.getConnection("JDBC:mysql:///db1","root","root");
            String sql = "select * from emp";
            stmt = conn.createStatement();
            rs = stmt.executeQuery(sql);
    
            Emp emp = null;		//防止反复申请空间
            list = new ArrayList<Emp>();
            while(rs.next()){
                int id = rs.getInt("id");
                String ename = rs.getString("ename");
                int job_id = rs.getInt("job_id");
                int mgr = rs.getInt("mgr");
                Date joindate = rs.getDate("joindate");
                double salary = rs.getDouble("salary");
                double bonus = rs.getDouble("bonus");
                int dept_id = rs. getInt("dept_id");
    
                emp = new Emp();
                emp.setId(id);
                emp.setEname(ename);
                emp.setJob_id(job_id);
                emp.setMgr(mgr);
                emp.setJoindate(joindate);
                emp.setSalary(salary);
                emp.setBouns(bonus);
                emp.setDept_id(dept_id);
    
                list.add(emp);
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
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
        return list;
    }
    ```

- 通过键盘录入用户名和密码，通过数据库判断用户是否登录成功。

  ```java
  public static void main(String[] args) {
      Scanner sc = new Scanner(System.in);
      System.out.println("用户名");
      String username = sc.nextLine();
      System.out.println("密码");
      String password = sc.nextLine();
      boolean flag = new Demo4().login(username,password);
      System.out.println(flag);
  }
  public boolean login(String username, String password) {
      if (username == null || password == null) {
          return false;
      }
      Connection conn = null;
      PreparedStatement pstmt = null;
      ResultSet rs = null;
      try {
          conn = JDBCUtils.getConnection();
          String sql = "select * from userlogin where username = ? and password = ?";
          pstmt = conn.prepareStatement(sql);
          pstmt.setString(1, username);
          pstmt.setString(2, password);
          rs = pstmt.executeQuery();
          return rs.next();
      } catch (SQLException e) {
          e.printStackTrace();
      } finally {
          JDBCUtils.close(pstmt, conn, rs);
      }
      return false;
  }
  ```

  

