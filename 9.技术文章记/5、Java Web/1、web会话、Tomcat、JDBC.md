学习自：

- [Cookie与Session](https://zhuanlan.zhihu.com/p/42918845)
- [Tomcat外传](https://zhuanlan.zhihu.com/p/54121733)
- [JDBC（上）](https://zhuanlan.zhihu.com/p/64693463)
- [Java3y Sevlet](https://github.com/ZhongFuCheng3y/3y#dollarservlettomcat)
- [Java3y JDBC](https://github.com/ZhongFuCheng3y/3y#tshirtjdbc)




## 1、Cookie和Session

- 会话机制最主要的目的是**帮助服务器记住客户端状态**（标识用户，跟踪状态）。

  - 目前，客户端与服务器的通讯都是通过HTTP协议。而HTTP是一种无状态协议。
  - 当服务器无法断定客户端时，一次会话就结束了！
  - 服务器什么情况下无法断定一个客户端？无非两种情况：服务器不行了（session失效）或客户端不行了（cookie失效）。
  - 会话的基本原则：双方共存（cookie与session同在）。

- Cookie：

  - Cookie其实是一份小数据，是服务器响应给客户端，并且**存储在客户端**的一份小数据。**下次客户端访问服务器时，会自动带上这个Cookie。**服务器通过Cookie就可以区分客户端。
  - 服务器端发给客户端的Cookie最终以HTTP响应形式发送，也就是Set-Cookie响应头。
  - 浏览器接收到这些响应头后，会把它们作为Cookie文件存在客户端。当我们第二次请求**同一个**服务器（也可以具体设置Cookie的作用域），浏览器在发送HTTP请求时，会带上这些Cookie。

  ![image-20210120195514144](http://iwehdio.gitee.io/img/my-img/21-01/image-20210120195514144.png)

- 其实Cookie可以分为两种：

  - 会话Cookie (Session Cookie)：会话Cookie被保存在浏览器的内存中，当浏览器关闭时，内存被释放，内存中的Cookie自然也就烟消云散。
  - 持久性Cookie (Persistent Cookie)：向客户端响应持久性Cookie，要设置Cookie的持久化时间！
  - 不设置MaxAge，默认响应会话Cookie(MaxAge<0)，存在浏览器内存。Cookie随浏览器关闭而消失
  - 设置MaxAge>0，响应持久性Cookie，会存在电脑硬盘的特定文件夹下（浏览器自定义的）
  - 设置特定Cookie的MaxAge=0，则会删除已经存在客户端的此Cookie

- Session：

  - Session是服务端的东西。其本质上类似于一个大Map，里面的内容，以键值对的形式存储。
  - 不再把数据作为Cookie放在请求头/响应头里传来传去了，而是只给客户端传一个JSESSIONID（其实也是一个Cookie）。
  - 真正的数据存在服务器端的Session中，Cookie中只是存了Session的id，即JSESSIONID。下次访问该网站时，把JSESSIONID带上，即可在服务器端找到对应的Session，也相当于“带去”了用户信息。
  - Session有个默认最大不活动时间：30分钟。
  - 只要你在服务器端创建了Session，即使不写addCookie("JSESSIONID", id)，JSESSIONID仍会被作为Cookie返回。
  - 钝化：如果一个Session长时间无人访问，为了减少内存占用，会被钝化（序列化）到磁盘上。当该Session再次被访问时，才会被活化（反序列化）。
  
- Cookie细节：

  - Cookie不可跨域名性。
    - 浏览器判断⼀个⽹站是否能操作另⼀个⽹站的Cookie的依据是域名。所以⼀般来说，当我访问baidu的时候，浏览器只会把baidu颁发的Cookie带过去，⽽不会带上google的Cookie。
    - 但是也可以通过修改作用的域名实现跨域名。
  - Cookie保存中⽂。需要进行URLEncode和Decode。
  - Cookie的有效期是通过setMaxAge()来设置的。
    - 如果MaxAge为正数，浏览器会把Cookie写到硬盘中，只要还在MaxAge秒之前，登陆⽹站时该
      Cookie就有效【不论关闭了浏览器还是电脑】
    - 如果MaxAge为负数，Cookie是临时性的，仅在本浏览器内有效，关闭浏览器Cookie就失效了，
      Cookie不会写到硬盘中。Cookie默认值就是-1。
    - 如果MaxAge为0，则表示删除该Cookie。Cookie机制没有提供删除Cookie对应的⽅法，把
      MaxAge设置为0等同于删除Cookie。
  - Cookie也没有提供修改Cookie的⽅法。
    - Cookie的名称相同，通过response添加到浏览器中，会覆盖原来的Cookie。
    - 注意：删除，修改Cookie时，新建的Cookie除了value、maxAge之外的所有属性都要与原Cookie相同。否则浏览器将视为不同的Cookie，不予覆盖，导致删除修改失败

- 浏览器禁⽤了Cookie，Session还能⽤吗？
  - Session好像不能⽤了。但是Java Web提供了解决⽅法：URL地址重写
  - HttpServletResponse类提供了两个URL地址重写的⽅法：
    - encodeURL(String url)
    - encodeRedirectURL(String url)
  - 这两个⽅法会⾃动判断该浏览器是否⽀持Cookie，如果⽀持Cookie，重写后的URL地址就不会带有jsessionid了。
  - URL地址重写的原理：将Session的id信息重写到URL地址中。服务器解析重写后URL，获取Session的
    id。

- Session和Cookie的区别：
  - 从存储内容上⽐较
    - Cookie只能存储字符串，如果要存储⾮ASCII字符串还要对其编码。
    - Session可以存储任何类型的数据，可以把Session看成是⼀个容器
  - 从隐私安全上⽐较
    - Cookie存储在浏览器中，对客户端是可⻅的。信息容易泄露出去。如果使⽤Cookie，最好将
      Cookie加密
    - Session存储在服务器上，对客户端是透明的。不存在敏感信息泄露问题。
  - 从有效期上⽐较
    - Cookie保存在硬盘中，只需要设置maxAge属性为⽐较⼤的正整数，即使关闭浏览器，
      Cookie还是存在的
    - Session的保存在服务器中，设置maxInactiveInterval属性值来确定Session的有效期。并且Session依赖于名为JSESSIONID的Cookie，该Cookie默认的maxAge属性为-1。如果关闭了浏览器，该Session虽然没有从服务器中消亡，但也就失效了。
  - 从对服务器的负担⽐较
    - Session是保存在服务器的，每个⽤户都会产⽣⼀个Session，如果是并发访问的⽤户⾮常
      多，是不能使⽤Session的，Session会消耗⼤量的内存。
    - Cookie是保存在客户端的。不占⽤服务器的资源。⼤型⽹站，⼀般都
      是使⽤Cookie来进⾏会话跟踪。
  - 从浏览器的⽀持上⽐较
    - 如果浏览器禁⽤了Cookie，那么Cookie是⽆⽤的了！
    - 如果浏览器禁⽤了Cookie，Session可以通过URL地址重写来进⾏会话跟踪。
  - 从跨域名上⽐较
    - Cookie可以设置domain属性来实现跨域名
    - Session只在当前的域名内有效，不可跨域名



## 2、Tomcat

- 服务器最本质的作用有两个：

  - 将资源对外暴露
  - 配合各种传输协议进行响应输出

- 什么是Tomcat？

  - Tomcat简单的说就是⼀个运⾏JAVA的⽹络服务器，底层是Socket的⼀个程序，它也是JSP和Serlvet的⼀
    个容器。

- 为什么需要Tomcat？

  - Tomcat就是提供能够让别⼈访问⾃⼰写的⻚⾯的⼀个程序。

- Tomcat目录结构：

  ![image-20210120201221113](http://iwehdio.gitee.io/img/my-img/21-01/image-20210120201221113.png)

- Tomcat架构：

  ![image-20210120201311516](http://iwehdio.gitee.io/img/my-img/21-01/image-20210120201311516.png)

  - 应该结合配置文件conf/server.xml看，其中与server中的结构对应。

  ![image-20210120201421679](http://iwehdio.gitee.io/img/my-img/21-01/image-20210120201421679.png)

  - Server.xml文件中的配置结构和Tomcat的架构是一一对应的。根目录是`<Server>`，代表服务器，`<Server>`下面有且仅有1个`<Service>`，代表服务。
  - Service>下有两个`<Connector>`，代表连接（需要的话可以再加）。
  - 其实这个Connector就是服务器的端口。可以看到Tomcat默认配置了两个端口，一个是HTTP/1.1协议的，一个是AJP/1.3协议。
  - 我们在浏览器输入"http://localhost:8080/demo/index.html"时，浏览器是以**HTTP协议**发送的，当这个请求到了服务器后，会被识别为HTTP类型，于是服务器就找来专门处理HTTP的Connector，它的默认端口正是上面Server.xml配置的8080。
  - 与Connector**平级**的还有个`<Engine>`（Tomcat引擎）。Connector的作用说穿了就是监听端口，不处理实际业务，它只负责把请求带到Engine那，然后Engine会处理。
  - `<Engine>`下面有个Host，代表主机。主机中就部署了Web项目。

- web站点目录：

  - 在webapps中建⽴了web1⽬录，下⾯放置我们的html⽂件，jsp⽂件，图⽚等等，则web1就被当做
    web应⽤管理起来（tomcat6.0以后的版本才⽀持）。

  ![image-20210129152828602](http://iwehdio.gitee.io/img/my-img/21-01/image-20210129152828602.png)

  - web.xml中可以用`<welcome-file>`指定首页。

- 配置虚拟目录：

  - 为什么需要配置虚拟⽬录？

    - 如果把所有web站点的⽬录都放在webapps下，可能导致磁盘空间不够⽤，也不利于对web站点
      ⽬录的管理【如果存在⾮常多的web站点⽬录】
    - 把web站点的⽬录分散到其他磁盘管理就需要配置虚拟⽬录【默认情况下，只有webapps下的⽬
      录才能被Tomcat⾃动管理成⼀个web站点】
    - 把web应⽤所在⽬录交给web服务器管理，这个过程称之为虚拟⽬录的映射

  - 配置方法1：

    - 在server.xml中的`<Host>` 节点下添加如下代码。path表示的是访问时输⼊的web项⽬名，docBase表示的是站点⽬录的绝对路径。

    ```xml
    <Context path="/web1" docBase="D:\web1"/>
    ```

  - 配置方法2：

    - 进⼊到conf\Catalina\localhost⽂件下，创建⼀个xml⽂件，该⽂件的名字就是站点的名字。xml⽂件的代码如下，docBase是你web站点的绝对路径。

    ```xml
    <Context
        docBase="D:\web1"
        reloadable="true">
    </Context>
    ```

- 虚拟主机：

  - 什么是虚拟主机？

    - 多个不同域名的⽹站共存于⼀个Tomcat中

  - 为什么要用到虚拟主机？

    - 开发了4个⽹站，有4个域名。如果我不配置虚拟主机，⼀个Tomcat服务器运⾏⼀个⽹站，我就需要4台电脑才能把4个⽹站运⾏起来。

  - 配置虚拟主机：

    - 在tomcat的server.xml⽂件中添加主机名

    ```xml
    <Host name="zhongfucheng" appBase="D:\web1">
    	<Context path="/web1" docBase="D:\web1"/>
    </Host>
    ```

- 浏览器访问WEB资源的流程图：

  ![image-20210129153553721](http://iwehdio.gitee.io/img/my-img/21-01/image-20210129153553721.png)

## 3、JDBC

- 什么是JDBC？

  - JDBC全称为：Java Data Base Connectivity,它是可以执⾏SQL语句的Java API。
  - JDBC就是一组API（包括少量类），为访问不同数据库提供了统一的途径，为开发者屏蔽了一些细节问题。

- 使用JDBC有三个大步骤：

  - 连接数据库
  - 执行SQL语句
  - 获得结果

- 获取数据库连接：

  ```java
  public class DriverDemo {
  	@Test
  	public void testDriver() throws SQLException {
  		//1. 创建一个 Driver 实现类的对象
  		Driver driver = new com.mysql.jdbc.Driver();
  
  		//2. 准备连接数据库的基本信息: url, user, password
  		String url = "jdbc:mysql://192.168.136.128:3306/test";
  		Properties info = new Properties();
  		info.put("user", "root");
  		info.put("password", "root");
  
  		//3. 调用 Driver 接口的　connect(url, info) 获取数据库连接
  		Connection connection = driver.connect(url, info);
  		System.out.println(connection);
  	}
  }
  ```

- MySQL的Driver实现类：

  - 只有一个静态代码块和无参构造。

  ![image-20210120212312981](http://iwehdio.gitee.io/img/my-img/21-01/image-20210120212312981.png)

  - MySQL的Driver类继承了NonRegisteringDriver，NonRegisteringDriver实现了Driver接口的全部方法。

  - connect()的核心代码就一句：总之，就是根据给定的url和用户名密码，返回一个与数据库关联的Connection。

    ```java
    Connection newConn = com.mysql.jdbc.ConnectionImpl.getInstance(host(props), port(props), props, database(props), url);
    ```

  - 由于com.mysql.jdbc.Driver继承了NonRegisteringDriver，所以它也可以调用connect方法（继承父类方法）。

  - 空参构造的作用，仅仅是为了能够new com.mysql.jdbc.Driver()。但重点不在于new本身，而是new这个Driver的时候，JVM会去加载这个Driver实现类。此时会自动执行静态代码块中的代码，就是把这个Driver注册到DriverManager。

    ```text
    java.sql.DriverManager.registerDriver(new Driver());
    ```

- 使用Driver获取连接的整体逻辑是：

  - new com.mysql.jdbc.Driver()
  - 类被加载进内存
  - 静态代码块执行：java.sql.DriverManager.registerDriver(newDriver());
  - com.mysql.jdbc.driver实例被注册进DriverManager
  - 调用com.mysql.jdbc.driver实例的connect()方法获得Connection

- 获取Connection通常有两种办法：

  - 根据具体的Driver实现类直接获取
  - 通过驱动管理器获取
  - 通常后者更常用。而所谓的驱动管理器，就是DriverManager。通过DriverManager.getConnection()即可获取。

- DriverManager如何获得Connection。

  ```java
  @Test
  public void testDriverManager() throws Exception{
      //1. 驱动的全类名
      String driverClass = "com.mysql.jdbc.Driver";
      //2. 准备连接数据库的基本信息: url, user, password
      String url = "jdbc:mysql://192.168.136.128:3306/test";
      String user = "root";
      String password = "root";
  
      //2. 加载数据库驱动程序(，JVM主动加载类到内存，对应的 Driver 实现类中有注册驱动的静态代码块)
      Class.forName(driverClass);
  
      //3. 通过 DriverManager 的 getConnection() 方法获取数据库连接
      Connection connection =
          DriverManager.getConnection(url, user, password);
      System.out.println(connection);
  
  }
  ```

- driver.connect()和DriverManager.getConnection()都可以获得连接，它们之间有什么联系吗？

  - DriverManager内部维护一个容器，该容器存储所有已注册的Driver
  - DriverManager是java.sql包下的一个类，内部维护着一个CopyOnWriteArrayList用于存放已经注册的驱动实例。之所以能getConnection()，是因为底层会循环遍历所有驱动，找到当前注册的驱动后调用driver.connect()获得Connection。
  - 静态代码块注册驱动到DriverManager对于Driver方式获取Connection是多余的，但是对于DriverManager获取Connection是必须的。
  - 设计DriverManager的初衷就是为了支持注册多个（多种）驱动，通过DriverManager可以管理多个驱动程序。所以它叫“驱动管理器”(DriverManager)。
  
- Connection对象：

  - 客户端与数据库所有的交互都是通过Connection来完成的。

  ```java
  //创建向数据库发送sql的statement对象。
  createStatement()
  //创建向数据库发送预编译sql的PrepareSatement对象。
  prepareStatement(sql)
  //创建执⾏存储过程的callableStatement对象
  prepareCall(sql)
  //设置事务⾃动提交
  setAutoCommit(boolean autoCommit)
  //提交事务
  commit()
  //回滚事务
  rollback()
  ```

- Statement对象：

  - 用于向数据库发送Sql语句，对数据库的增删改查都可以通过此对象发送sql语句完成。

  ```java
  //查询
  executeQuery(String sql)
  //增删改
  executeUpdate(String sql)
  //任意sql语句都可以，但是⽬标不明确，很少⽤
  execute(String sql)
  //把多条的sql语句放进同⼀个批处理中
  addBatch(String sql)
  //向数据库发送⼀批sql语句执⾏
  executeBatch()
  ```

- ResultSet对象：

  - 代表Sql语句的执⾏结果，当Statement对象执⾏executeQuery()时，会返回⼀个ResultSet对象。
  - ResultSet对象维护了⼀个数据⾏的游标【简单理解成指针】，调⽤ResultSet.next()⽅法，可以让游标指向具体的数据⾏，进⾏获取该⾏的数据。

  ```java
  //获取任意类型的数据
  getObject(String columnName)
  //获取指定类型的数据【各种类型，查看API】
  getString(String columnName)
  //对结果集进⾏滚动查看的⽅法
  next()
  Previous()
  absolute(int row)
  beforeFirst()
  afterLast()
  ```

- PreparedStatement对象：

  - 继承Statement对象，它⽐Statement对象更强⼤，使⽤起来更简单
  - Statement对象编译SQL语句时，如果SQL语句有变量，就需要使⽤分隔符来隔开，如果变量⾮常
    多，就会使SQL变得⾮常复杂。PreparedStatement可以使⽤占位符，简化sql的编写
  - Statement会频繁编译SQL。PreparedStatement可对SQL进⾏预编译，提⾼效率，预编译的SQL存储在PreparedStatement对象中
  - PreparedStatement防⽌SQL注⼊。【Statement通过分隔符'++',编写永等式，可以不需要密码就进⼊数据库】

- 批处理：

  - 当需要向数据库发送⼀批SQL语句执⾏时，应避免向数据库⼀条条发送执⾏，采⽤批处理以提升执⾏效率
  - 批处理有两种⽅式：
    1. Statement
    2. PreparedStatement
  - 通过addBatch()方法添加到批处理中，
  - 通过executeBath()⽅法批量处理执⾏SQL语句，返回⼀个int[]数组，该数组代表各句SQL的返回值

- MySQL存储⼤⽂本是⽤Test，Test⼜分为4类：

  - TINYTEXT
  - TEXT
  - MEDIUMTEXT
  - LONGTEXT

- ⽤JDBC操作MySQL数据库去操作大文本数据：

  - 插入：`preparedStatement.setCharacterStream(int parameterIndex,Reader reader,long length)`：第⼆个参数接收的是⼀个流对象，因为⼤⽂本不应该⽤String来接收，String太⼤会导致内存溢出。第三个参数接收的是⽂件的⼤⼩。
  - 查询：`resultSet.getCharacterStream(String filename)`：返回一个该文件路径的Reader对象。

