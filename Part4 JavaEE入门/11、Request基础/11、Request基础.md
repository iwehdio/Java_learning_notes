# 38、Servlet、HTTP、Request

## 1、Servlet

- Servlet 体系结构：
  - `GenericServlet`抽象类：是 Servlet 接口的实现，对除了 service() 方法作为抽象，需要在子类中重写。如果继承该类，只需要实现 service() 方法。
  - `HttpServlet`抽象类：是 GenericServlet 抽象类的子类，是对 http 协议的一种封装。需要复写 doGet() 和 doPost() 方法，分别用于不同的请求方式。
- urlpartten 配置：
  - 一个 Servlet 可以定义多个访问路径，在注解上的配置方式为：`@WebServlet({"/路径1","/路径2"})`。
  - 路径配置规则：
    - 单层路径：`/xxx` 。
    - 多层路径：`/xxx/xxx `。（\*是通配符，/\*表示任意路径）
    - 扩展名匹配：`*.扩展名`。（即任意路径 . 扩展名可访问到）

## 2、HTTP-请求

> Hyper Text Transfer Protocol：超文本传输协议。

- 传输协议：定义了客户端和服务器端通信是，发送数据的格式。

- HTTP 协议：

  - 是基于 TCP / IP 的高级协议。
  - 默认端口号是 80 。
  - 基于请求 / 响应模型，一次请求对应一次响应。
  - 是一种无状态协议，每次请求之间相互独立，不能交互数据。
  - 1.0版本对于每次请求都会建立一次连接，使用完后关闭。1.1版本可以复用连接。

- HTTP 请求消息：

  - 数据格式：

    1. 请求行。

       - `请求方式 请求url 请求协议/版本`，如`GET /demo.html HTTP/1.1`。
       - 请求方式：HTTP 有七种请求方式，常用的的 GET 和 POST。
         - GET ：请求参数在请求行中，在 url 后拼接。请求的 url 长度是有限制的。
         - POST：请求参数在请求体中。请求的 url 长度是没有限制的。

    2. 请求头：客户端浏览器传送给服务器其自身信息，

       - `请求头名称：请求头的值`。
       - User-Agent：所要访问服务器所使用的浏览器的版本信息。
       - Accepte：浏览器可以接收的资源类型。
       - Referer：告诉服务器当前请求从哪里来（从哪个页面的链接跳转到此页面）。

    3. 请求空行。

       - 分隔请求头和请求体，就是一个空行。

    4. 请求体。

       - `参数名=参数值`。
       - 峰值POST请求消息的请求参数。

       - GET方式没有请求头，POST方式才有。

## 3、Request

- request 和 response 的原理：
  - request 和 response 对象是由服务器创建的。
  - request 对象是用来获取请求消息，response 对象是用来设置响应消息。
  - 浏览器请求服务器的流程：
    1. Tomcat 服务器根据请求 url 中的资源路径，创建相应的对象。
    2. Tomcat 服务器创建 request 和 response 对象。
    3. Tomcat 将 request 和 response 对象传递给 service() 方法，并且调用 service() 方法。
    4. 在 service() 方法中，通过 request 对象获取请求凶啊洗漱间，通过 response 对象设置响应消息数据。
    5. 在服务器对浏览器响应之前，从 response 对象中取出响应消息数据。
- request 继承体系结构：
  - ServletRequest：service() 方法中的 request 参数类型，是一个接口。
  - HttpServletRequest：继承自 ServletRequest ，是 HttpServlet 中 service() 方法中的 request 参数类型，是一个接口。
  - RequestFacade：Tomcat 实现的 HttpServletRequest 接口的实现类。
- request 的功能：
  - 获取请求消息数据：
    1. 获取请求行数据。
       - `String getMethod()`：获取请求方式。
       - `String getContextPath()`：获取虚拟目录。
       - `String getServletPath()`：获取 Servlet 路径。
       - `String getRequestURI()`：获取请求 uri ，即虚拟目录+ Servlet 路径。URI 是统一资源标识符。
       - `String getRequestURL()`：获取请求 url ，即完整路径。
       - `String getQueryString()`：获取 get 方式请求参数。
       - `String getProtocol()`：获取协议及版本。
       - `String getRemoteAddr()`：获取客户机 IP 地址。
    2. 获取请求头数据。
       - `String getHeader(String name)`：通过请求头名称获取请求头的值（请求头不区分大小写）。
       - `Enumeration<String> getHeaderNames()`：获取所有请求头名称。
    3. 获取请求体数据。
       - 先获取流对象，再从流对象中获取数据。
       - `BufferedReader getReader()`：获取字符输入流。
       - `ServletInputStream getInputStream()`：获取字节输入流。
  - 其他功能：
    1. 获取请求参数（对get和post的通用方式）。
       - `String getPatameter(String name)`：根据参数名称获取参数值。
       - `String[] getPatameterValues(String name)`：根据参数名称获取参数值的数组。
       - `Enumeration<String> getPatameterNames(String name)`：获取所有请求的参数名称。
       - `Map<String, String[]> getParameterMap()`：获取所有参数的 map 集合。
    2. 请求转发：一种在服务器内部的资源跳转方式。
       - 步骤：
         1. 通过 request 对象获取请求转发器对象：`RequestDispatcher getRequsetDispatcher (Stirng path)`。
         2. 使用请求转发器对象进行转发：`void forward(ServletRequest request, ServletResponse response)`。
       - 特点：
         1. 浏览器地址栏路径不发生变化。
         2. 只能转发到当前服务器内部资源中。
         3. 转发是一次请求。
    3. 共享数据。
       - 域对象：一个有作用范围的对象，可以在范围内共享数据。
       - request 的域：一次请求的范围，一般用于请求转发的多个资源共享数据。
       - `void setAttribute(String name, Object obj)`：存储数据。
       - `Object get Attribute(String name)`：通过键获取值。
       - `void removeAttribute(String name)`：通过键移除键值对。
    4. 获取 ServletContext 。
       - `ServletContext getServletContext()`：获取  ServletContext 。
  - 中文乱码问题：
    - Tomcat8 已经解决了 get 方式乱码，post 方式还会乱码。
    - `request.setCharacterEncoding(字符集)`：设置流的编码。

- BeanUtils 工具类：简化数据封装。

  - 可以封装 JavaBean 即标准的 Java 类，在 apache.commons 包下。

  - 标准的 Java 类：

    - 必须被 public 修饰。
    - 必须提供空参的构造方法。
    - 成员变量必须提供 private 修饰。
    - 提供公共的 setter 和 getter 方法。
    - 可以用于封装数据。

  - 方法：

    - `setProperty(Object obj, String name, String value)`：按键值对将数据封装到对象中。
    - `getProperty(Object obj, String name)`：按键从对象中获取数据。
    - `populate(Object obj, Map map)`：将 Map 中的键值对封装为对象。

  - 使用 populate 方法获取所有的请求参数：

    ```java
    Map<String, String[]> map = req.getParameterMap();
    User loginuser = new User();
    try {
        BeanUtils.populate(loginuser, map);
    } catch (IllegalAccessException e) {
        e.printStackTrace();
    } catch (InvocationTargetException e) {
        e.printStackTrace();
    }
    ```

## 4、案例

- 用户登录：
  1. 编写 html 登录页面。
  2. 使用 Druid 数据库连接技术查询数据库中的 user 表。
  3. 使用 JdbcTemplate 封装 JDBC 。
  4. 登陆成功或失败时跳转并提示。
  
- 项目结构（项目 Request）：

  - src
    - cn.iwehdio
      - user
        - User.java
      - dao
        - UserDao.java
      - utils
        - JDBCUtils.java
      - test0
        - TestSQL.java
      - web.servlet
        - LoginServlet.java
        - failServlet.java
        - SuccServlet.java
  - web
    - WEB-INF
      - lib
      - web.xml
    - login.html

- 步骤：

  1. 创建项目，编写 html 文件，导入配置文件和依赖包。

     ```html
     <!DOCTYPE html>
     <html lang="en">
     <head>
         <meta charset="UTF-8">
         <title>Title</title>
     </head>
     <body>
         <form action="/Request/LoginServlet" method="post">
             用户名:<input type="text" name="username"> <br>
             密码:<input type="password" name="password"><br>
             <input type="submit" value="登录">
         </form>
     </body>
     </html>
     ```

  2. 创建数据库环境。

  3. 创建 User 类封装用户信息，并生成 get、set和toString方法。

     ```java
     public class User {
         private int id;
         private String username;
         private String password;
         public int getId() {
             return id;
         }
         public void setId(int id) {
             this.id = id;
         }
         public String getUsername() {
             return username;
         }
         public void setUsername(String username) {
             this.username = username;
         }
         public String getPassword() {
             return password;
         }
         public void setPassword(String password) {
             this.password = password;
         }
         @Override
         public String toString() {
             return "User{" +
                     "id=" + id +
                     ", username='" + username + '\'' +
                     ", password='" + password + '\'' +
                     '}';
         }
     }
     ```

  4. 编写 JDBC 工具类 JDBCUtils 。

     ```java
     //使用Druid连接池
     public class JDBCUtils {
         private static DataSource ds;
     
         static {
             try {
                 //加载配置文件
                 Properties pro = new Properties();
                 InputStream is = JDBCUtils.class.getClassLoader().getResourceAsStream("druid.properties");
                 pro.load(is);
                 //初始化连接池对象
                 ds = DruidDataSourceFactory.createDataSource(pro);
             } catch (IOException e) {
                 e.printStackTrace();
             } catch (Exception e) {
                 e.printStackTrace();
             }
         }
         //获取连接池对象
         public static DataSource getDataSource(){
             return ds;
         }
         //获取连接Connection对象
         public static Connection getConnection() throws SQLException {
             return ds.getConnection();
         }
     }
     ```

  5. 创建 UserDao 类，提供输入数据与数据库的校验。

     ```java
     public class UserDao {
         //声明JDBCTemplate对象公用
         private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());
         //登陆方法
         public User login(User loginUser){
             try {
                 //sql查询语句
                 String sql = "select * from userlogin where username = ? and password = ?";
                 User user = template.queryForObject(sql,
                         new BeanPropertyRowMapper<User>(User.class),
                         loginUser.getUsername(), loginUser.getPassword());
                 //查询到就返回user对象
                 return user;
             } catch (DataAccessException e) {
                 //查询不到返回null
                 e.printStackTrace();
                 return null;
             }
         }
     }
     ```

  6. 创建 test 测试数据库部分。

     ```java
     public class TestSQL {
         @Test
         public void testLogin(){
             try {
                 User loginuser = new User();
                 loginuser.setUsername("qwe");
                 loginuser.setPassword("1234");
     
                 UserDao dao = new UserDao();
                 User user = dao.login(loginuser);
                 System.out.println(user);
             } catch (Exception e) {
                 e.printStackTrace();
             }
         }
     }
     ```

  7. 创建 LoginServlet ，获取浏览器输入到服务器并执行操作。

     ```java
     //指定资源路径
     @WebServlet("/LoginServlet")
     public class LoginServlet extends HttpServlet {
         @Override
         protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
             //指定编码
             req.setCharacterEncoding("utf-8");
             //获取requset请求参数
             String username = req.getParameter("username");
             String password = req.getParameter("password");
             //封装user对象
             User loginuser = new User();
             loginuser.setUsername(username);
             loginuser.setPassword(password);
             //使用login查询方法
             UserDao dao = new UserDao();
             User user = dao.login(loginuser);
             if(user == null){
                 //登录失败转发到failServlet
                 req.getRequestDispatcher("/failServlet").forward(req, resp);
             } else {
                 //登陆成功存储user对象转发SuccServlet
                 req.setAttribute("user", user);
                 req.getRequestDispatcher("/SuccServlet").forward(req, resp);
             }
         }
         @Override
         protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
             //post方法调用get
             this.doGet(req, resp);
         }
     }
     ```

  8. 编写 failServlet 和 SuccServlet 。

     ```java
     @WebServlet("/SuccServlet")
     public class SuccServlet extends HttpServlet {
         protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
             User user = (User) request.getAttribute("user");
             response.setContentType("text/html; charset=utf-8");
             response.getWriter().write(user.getUsername() + "登陆成功");
         }
         protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
             doGet(request, response);
         }
     }
     
     @WebServlet("/failServlet")
     public class failServlet extends HttpServlet {
         protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
             response.setContentType("text/html; charset=utf-8");
             response.getWriter().write("登陆失败");
         }
         protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
             doGet(request, response);
         }
     }
     ```

     

 

