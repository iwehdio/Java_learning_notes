## 1、环境搭建

- 创建maven父工程 ssm_ex。
  - 创建maven子工程ssm_dao。
  - 创建maven子工程ssm_service。
  - 创建maven子工程ssm_web。
  - 创建maven子工程ssm_domain。
  - 创建maven子工程ssm_utils。



- maven父工程依赖：

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
  
      <groupId>org.example</groupId>
      <artifactId>ssm_ex</artifactId>
      <packaging>pom</packaging>
      <version>1.0-SNAPSHOT</version>
      <modules>
          <module>ssm_dao</module>
          <module>ssm_service</module>
          <module>ssm_domain</module>
          <module>ssm_utils</module>
          <module>ssm_web</module>
      </modules>
  
      <properties>
          <spring.version>5.0.2.RELEASE</spring.version>
          <slf4j.version>1.6.6</slf4j.version>
          <log4j.version>1.2.12</log4j.version>
          <oracle.version>11.2.0.1.0</oracle.version>
          <mybatis.version>3.4.5</mybatis.version>
          <spring.security.version>5.0.1.RELEASE</spring.security.version>
      </properties>
  
      <dependencies>        <!-- spring -->
          <dependency>
              <groupId>org.aspectj</groupId>
              <artifactId>aspectjweaver</artifactId>
              <version>1.6.8</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-aop</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-context</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-context-support</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-web</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-orm</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-beans</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-core</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-test</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-webmvc</artifactId>
              <version>${spring.version}</version>
  
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-tx</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>junit</groupId>
              <artifactId>junit</artifactId>
              <version>4.12</version>
              <scope>test</scope>
          </dependency>
  
          <dependency>
              <groupId>javax.servlet</groupId>
              <artifactId>javax.servlet-api</artifactId>
              <version>3.1.0</version>
              <scope>provided</scope>
          </dependency>
          <dependency>
              <groupId>javax.servlet.jsp</groupId>
              <artifactId>jsp-api</artifactId>
              <version>2.0</version>
              <scope>provided</scope>
          </dependency>
          <dependency>
              <groupId>jstl</groupId>
              <artifactId>jstl</artifactId>
              <version>1.2</version>
          </dependency>        <!-- log start -->
          <dependency>
              <groupId>log4j</groupId>
              <artifactId>log4j</artifactId>
              <version>${log4j.version}</version>
          </dependency>
          <dependency>
              <groupId>org.slf4j</groupId>
              <artifactId>slf4j-api</artifactId>
              <version>${slf4j.version}</version>
          </dependency>
          <dependency>
              <groupId>org.slf4j</groupId>
              <artifactId>slf4j-log4j12</artifactId>
              <version>${slf4j.version}</version>
          </dependency>        <!-- log end -->
  
          <dependency>
              <groupId>org.mybatis</groupId>
              <artifactId>mybatis</artifactId>
              <version>${mybatis.version}</version>
          </dependency>
          <dependency>
              <groupId>org.mybatis</groupId>
              <artifactId>mybatis-spring</artifactId>
              <version>1.3.0</version>
          </dependency>
          <dependency>
              <groupId>c3p0</groupId>
              <artifactId>c3p0</artifactId>
              <version>0.9.1.2</version>
              <type>jar</type>
              <scope>compile</scope>
          </dependency>
          <dependency>
              <groupId>com.github.pagehelper</groupId>
              <artifactId>pagehelper</artifactId>
              <version>5.1.2</version>
          </dependency>
          <dependency>
              <groupId>org.springframework.security</groupId>
              <artifactId>spring-security-web</artifactId>
              <version>${spring.security.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework.security</groupId>
              <artifactId>spring-security-config</artifactId>
              <version>${spring.security.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework.security</groupId>
              <artifactId>spring-security-core</artifactId>
              <version>${spring.security.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework.security</groupId>
              <artifactId>spring-security-taglibs</artifactId>
              <version>${spring.security.version}</version>
          </dependency>
  
          <dependency>
              <groupId>com.oracle</groupId>
              <artifactId>ojdbc14</artifactId>
              <version>${oracle.version}</version>
          </dependency>
  
          <dependency>
              <groupId>javax.annotation</groupId>
              <artifactId>jsr250-api</artifactId>
              <version>1.0</version>
          </dependency>
      </dependencies>
      <build>
          <pluginManagement>
              <plugins>
                  <plugin>
                      <groupId>org.apache.maven.plugins</groupId>
                      <artifactId>maven-compiler-plugin</artifactId>
                      <version>3.2</version>
                      <configuration>
                          <source>1.11</source>
                          <target>1.11</target>
                          <encoding>UTF-8</encoding>
                          <showWarnings>true</showWarnings>
                      </configuration>
                  </plugin>
              </plugins>
          </pluginManagement>
      </build>
  </project>
  ```

- 配置文件，都在ssm_web下：

  - applicationContext.xml：

    ```xml
    <!-- 注解扫描 -->
    <context:component-scan base-package="cn.iwehdio.dao"></context:component-scan>
    <context:component-scan base-package="cn.iwehdio.service"></context:component-scan>
    
    <!-- 获取数据库配置文件 -->
    <context:property-placeholder location="classpath:db.properties"></context:property-placeholder>
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"></property>
        <property name="jdbcUrl" value="${jdbc.url}"></property>
        <property name="user" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </bean>
    <!-- sqlSessionFactory -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <!-- 扫描Dao接口 -->
    <bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="cn.iwehdio.dao"></property>
    </bean>
    <!-- 事务管理器 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
    ```

  - springmvc.xml:

    ```xml
    <!-- 扫描controller的注解，别的不扫描 -->
    <context:component-scan base-package="cn.iwehdio.controller">
    </context:component-scan>
    
    <!-- 配置视图解析器 -->
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!-- JSP文件所在的目录 -->
        <property name="prefix" value="/pages/" />
        <!-- 文件的后缀名 -->
        <property name="suffix" value=".jsp" />
    </bean>
    
    <!-- 设置静态资源不过滤 -->
    <mvc:resources location="/css/" mapping="/css/**" />
    <mvc:resources location="/img/" mapping="/img/**" />
    <mvc:resources location="/js/" mapping="/js/**" />
    <mvc:resources location="/plugins/" mapping="/plugins/**" />
    
    <!-- 开启对SpringMVC注解的支持 -->
    <mvc:annotation-driven />
    
    <!--
            支持AOP的注解支持，AOP底层使用代理技术
            JDK动态代理，要求必须有接口
            cglib代理，生成子类对象，proxy-target-class="true" 默认使用cglib的方式
        -->
    <aop:aspectj-autoproxy proxy-target-class="true"/>
    ```

  - web.xml:

    ```xml
    <!-- 配置加载类路径的配置文件 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath*:applicationContext.xml</param-value>
    </context-param>
    
    <!-- 配置监听器 -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <!-- 配置监听器，监听request域对象的创建和销毁的 -->
    <listener>
        <listener-class>org.springframework.web.context.request.RequestContextListener</listener-class>
    </listener>
    
    <!-- 前端控制器（加载classpath:springmvc.xml 服务器启动创建servlet） -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- 配置初始化参数，创建完DispatcherServlet对象，加载springmvc.xml配置文件 -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        <!-- 服务器启动的时候，让DispatcherServlet对象创建 -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>*.do</url-pattern>
    </servlet-mapping>
    
    <!-- 解决中文乱码过滤器 -->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    
    <welcome-file-list>
        <welcome-file>index.html</welcome-file>
        <welcome-file>index.htm</welcome-file>
        <welcome-file>index.jsp</welcome-file>
        <welcome-file>default.html</welcome-file>
        <welcome-file>default.htm</welcome-file>
        <welcome-file>default.jsp</welcome-file>
    </welcome-file-list>
    ```

  - dp.properties:

    ```properties
    jdbc.driver=com.mysql.jdbc.Driver
    jdbc.url=jdbc:mysql://localhost:3306/ssm
    jdbc.username=root
    jdbc.password=root
    ```

    

## 2、产品的查询

- domain下创建实体类：

  ```java
  public class product {
      private String id;  //主键
      private String productNum;  //唯一编号
      private String productName; //商品名称
      private String cityName;    //出发城市
      private Date departureTime; //出发时间
      private String departureTimeStr;
      private double productPrice;    //产品价格
      private String productDesc;     //产品描述
      private Integer productStatus;  //状态，0关闭，1开启
      private String productStatusStr;
  	// getter & setter
  }
  ```

- dao下编写持久层接口：

  ```java
  public interface ProductDao {
      @Select("select * from product")
      List<Product> findAll() throws Exception;
  }
  ```

- service下编写业务层接口和实现类：

  ```java
  public interface ProductService {
      List<Product> findAll() throws Exception;
  }
  
  public class ProductServiceImpl implements ProductService {
      @Autowired
      private ProductDao productDao;
      @Override
      public List<Product> findAll() throws Exception {
          return productDao.findAll();
      }
  }
  ```

- web下编写控制器：

  ```java
  @Controller
  @RequestMapping("/product")
  public class ProductController {
      @Autowired
      private ProductService productService;
      @RequestMapping("/findAll.do")
      public ModelAndView findAll() throws Exception {
          ModelAndView modelAndView = new ModelAndView();
          List<Product> productList = productService.findAll();
          modelAndView.addObject("productList", productList);
          modelAndView.setViewName("product-list");
          return modelAndView;
      }
  }
  ```

- 编写jsp页面，跳转到控制器。以及控制器所要跳转到的页面

- 编写日期与字符串相互转换的工具类：

  ```java
  public class DateUtils {
  
      public static String Date2String(Date date, String pat) {
          SimpleDateFormat simpleDateFormat = new SimpleDateFormat(pat);
          String format = simpleDateFormat.format(date);
          return format;
      }
  
      public static Date String2Date(String string, String pat) throws ParseException {
          SimpleDateFormat simpleDateFormat = new SimpleDateFormat(pat);
          Date parse = simpleDateFormat.parse(string);
          return parse;
      }
  }
  ```

- 在Product实体类中，对字符串显示属性进行设置。

- 编写main.jsp，图片无法显示，将图片加入到tomcat部署中的external source。



## 3、添加商品操作

- 在product-list.jsp中，点击新建，跳转到product-add.jsp。

- 在product-add.jsp中点击添加，跳转到相应的控制器，用save方法完成添加操作。调用service和dao。注意事务。

- 添加后回显到findAll的页面。

- 控制器：

  ```java
  @RequestMapping("/save.do")
  public String save(Product product) {
      productService.save(product);
      return "redirect:findAll.do";
  }
  ```

- SQL语句：

  ```java
  @Insert("insert into product (productNum,productName,cityName,departureTime,productPrice,productDesc,productStatus)" +
              "values(#{productNum},#{productName},#{cityName},#{departureTime},#{productPrice},#{productDesc},#{productStatus})")
      void save(Product product);
  ```

- 在业务层类上加`@Transactional`事务管理。

- 页面提交的是字符串，而product类中的类型是Date，需要进行类型转换（直接在实体类上加注解）：

  ```java
  @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm")
  private Date departureTime; //出发时间
  ```



## 4、订单操作

- 表之间的关系：

  - 订单表与产品表用一个外键联系。
  - 订单表与会员表用一个外键联系。
  - 订单表与旅客表用一张中间表联系。

- 查询所有订单：

  - 左侧菜单栏中的订单管理为入口，请求服务器。
  - 控制器查询所有订单，调用service和dao完成操作。
  - 需要查询订单关联的产品信息。

- 分别创建订单、会员、旅客的实体类：

  ```java
  public class Orders {
      private String id;
      private String orderNum;
      private Date orderTime;
      private String orderTimeStr;
      private int orderStatus;
      private String orderStatusStr;
      private int peopleCount;
      private Product product;
      private List<Traveller> travellers;
      private Member member;
      private Integer payType;
      private String payTypeStr;
      private String orderDesc;
  }
  
  public class Member {
      private String id;
      private String name;
      private String nickname;
      private String phoneNum;
      private String email;
  }
  
  public class Traveller {
      private String id;
      private String name;
      private String sex;
      private String phoneNum;
      private Integer credentialsType;
      private String credentialsTypeStr;
      private Integer travellerType;
      private String travellerTypeStr;
  }
  ```

- 创建Service和Dao，为Service添加`@Service`和`@Transactional`注解。为Dao添加`@Repository`注解。

- 在ProductDao中提供按照id查询一个的方法：

  ```java
  @Select("select * from product where id = #{id}")
  Product findById(String id);
  ```

- 在OrdersDao中，进行关联查询：

  ```java
  @Select("select * from orders")
  @Results({
      @Result(id = true, property = "id", column = "id"),
      @Result(property = "orderNum", column = "orderNum"),
      @Result(property = "orderTime", column = "orderTime"),
      @Result(property = "orderStatus", column = "orderStatus"),
      @Result(property = "peopleCount", column = "peopleCount"),
      @Result(property = "payType", column = "payType"),
      @Result(property = "orderDesc", column = "orderDesc"),
      @Result(property = "product", column = "productId", javaType = Product.class, one = @One(select = "cn.iwehdio.dao.ProductDao.findById")),
  })
  List<Orders> findAll();
  ```

- 创建OrdersController控制器：

  ```java
  @Controller
  @RequestMapping("/orders")
  public class OrdersController {
      @Autowired
      private OrdersService ordersService;
      @RequestMapping("/findAll.do")
      public ModelAndView findAll() throws Exception {
          List<Orders> orders = ordersService.findAll();
          ModelAndView modelAndView = new ModelAndView();
          modelAndView.addObject("ordersList", orders);
          modelAndView.setViewName("orders-list");
          return modelAndView;
      }
  }
  ```

  

- 分页查询：

  - PageHelper是一个用于分页的Mybatis插件。

  - 添加依赖：

    ```xml
    <dependency>
        <groupId>com.github.pagehelper</groupId>
        <artifactId>pagehelper</artifactId>
        <version>5.1.2</version>
    </dependency>
    ```

- 使用步骤：

  1. 导入依赖。

  2. 如果使用Spring整合了Mybatis，则：

     ```xml
     <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
         <property name="dataSource" ref="dataSource" />
         <!-- 传入PageHelper的插件 -->
         <property name="plugins">
             <array>
                 <!-- 传入插件的对象 -->
                 <bean class="com.github.pagehelper.PageInterceptor">
                     <property name="properties">
                         <props>
                             <prop key="helperDialect">mysql</prop>
                             <prop key="reasonable">true</prop>
                         </props>
                     </property>
                 </bean>
             </array>
         </property>
     </bean>
     ```

  3. helperDialect：分页插件会自动检测当前的数据库连接，自动选择合适的分页方式。

  4. reasonable：分页合理化参数，默认为false。为true时，pageNum<=0会查询第一页，>pages会查询最后一页。

  5. 静态方法调用：在正式调用dao进行查询前一句执行：

     ```java
     PageHelper.startPage(PageNum,pageSize);
     ```

     即页码和每页显示数。

  6. 构造方法调用：传入pageInfo的构造，会返回一个含有分页数据和查询到的数据的PageBean。控制器：

     ```java
     @Controller
     @RequestMapping("/orders")
     public class OrdersController {
         @Autowired
         private OrdersService ordersService;
         @RequestMapping("/findAll.do")
         public ModelAndView findAll(@RequestParam(name = "page",required = true, defaultValue = "1")int page,
                                     @RequestParam(name = "size", required = true, defaultValue = "4") int size ) throws Exception {
             List<Orders> orders = ordersService.findAll(page, size);
             PageInfo pageInfo = new PageInfo(orders);
             ModelAndView modelAndView = new ModelAndView();
             modelAndView.addObject("pageInfo", pageInfo);
             modelAndView.setViewName("orders-page-list");
             return modelAndView;
         }
     }
     ```

  7. Service：

     ```java
     @Override
     public List<Orders> findAll(int page, int size) throws Exception {
         PageHelper.startPage(page, size);
         return ordersDao.findAll();
     }
     ```

- JSP页面分页显示：

  ```jsp
  <li><a href="${pageContext.request.contextPath}/orders/findAll.do?page=1&size=${pageInfo.pageSize}" aria-label="Previous">首页</a></li>
  <li><a href="${pageContext.request.contextPath}/orders/findAll.do?page=${pageInfo.pageNum-1}&size=${pageInfo.pageSize}">上一页</a></li>
  <c:forEach begin="1" end="${pageInfo.pages}" var="pageNum">
      <li><a href="${pageContext.request.contextPath}/orders/findAll.do?page=${pageNum}&size=${pageInfo.pageSize}">${pageNum}</a></li>
  </c:forEach>
  <li><a href="${pageContext.request.contextPath}/orders/findAll.do?page=${pageInfo.pageNum+1}&size=${pageInfo.pageSize}">下一页</a></li>
  <li><a href="${pageContext.request.contextPath}/orders/findAll.do?page=${pageInfo.pages}&size=${pageInfo.pageSize}" aria-label="Next">尾页</a></li>
  ```

  

- 订单详情：

  - 点击详情时，查看当前订单的具体信息（携带订单的id）。
  - 包括订单信息、产品信息、会员信息和旅客信息。
  - Dao层返回的是一个orders对象。控制器响应后跳转到show页面。

- OrdersDao下的多表查询：

  ```java
  @Select("select * from orders where id = #{ordersId}")
  @Results({
      @Result(id = true, property = "id", column = "id"),
      @Result(property = "orderNum", column = "orderNum"),
      @Result(property = "orderTime", column = "orderTime"),
      @Result(property = "orderStatus", column = "orderStatus"),
      @Result(property = "peopleCount", column = "peopleCount"),
      @Result(property = "payType", column = "payType"),
      @Result(property = "orderDesc", column = "orderDesc"),
      @Result(property = "product", column = "productId", javaType = Product.class, one = @One(select = "cn.iwehdio.dao.ProductDao.findById")),
      @Result(property = "member", column = "memberId", javaType = Member.class, one = @One(select = "cn.iwehdio.dao.MemberDao.findById")),
      @Result(property = "travellers", column = "id", javaType = java.util.List.class, many = @Many(select = "cn.iwehdio.dao.TravellerDao.findByOrdersId"))
  })
  Orders findById(String ordersid) throws Exception;
  ```

- 同时需要先添加MemberDao和TravellerDao的单表查询：

  ```java
  @Repository
  public interface MemberDao {
      @Select("select * from member where id = #{id}")
      Member findById(String id) throws Exception;
  }
  
  @Repository
  public interface TravellerDao {
      @Select("select * from traveller where id in " +
              "(select travellerId from order_traveller where orderId=#{ordersId})")
      List<Traveller> findByOrdersId(String ordersId) throws Exception;
  }
  ```

- 控制器：

  ```java
  @RequestMapping("/findById.do")
  public ModelAndView findById(@RequestParam(name = "id",required = true) String ordersid) throws Exception {
      Orders orders = ordersService.findById(ordersid);
      ModelAndView modelAndView = new ModelAndView();
      modelAndView.addObject("orders", orders);
      modelAndView.setViewName("orders-show");
      return modelAndView;
  }
  ```

  

## 5、权限控制

- 权限控制：

  - 涉及到用户表、角色表和资源权限表。
  - 用户表和角色表之间是多对多的关系，需要中间表关联。
  - 权限表和角色表之间也是多对多的关系，需要中间表关联。

- Spring Security安全服务认证：

  - 认证：角色的登录。

  - 授权：在执行操作之前，已经拥有权限。

  - 导入依赖：

    ```xml
    <dependency>
      <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-web</artifactId>
        <version>${spring.security.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-config</artifactId>
        <version>${spring.security.version}</version>
    </dependency>
    ```

  - 在web.xml中指定配置文件、创建Filter：

    ```xml
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring-security.xml</param-value>
      </context-param>
      <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
      </listener>
      <filter>
        <filter-name>springSecurityFilterChain</filter-name>
        <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
      </filter>
      <filter-mapping>
        <filter-name>springSecurityFilterChain</filter-name>
        <url-pattern>/*</url-pattern>
      </filter-mapping>
    ```

  - Spring-Security.xml配置文件：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:security="http://www.springframework.org/schema/security"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/security
    http://www.springframework.org/schema/security/spring-security.xsd">
        <security:http auto-config="true" use-expressions="false">
            <!-- intercept-url定义一个过滤规则 pattern表示对哪些url进行权限控制，ccess属性表示在请求对应的URL时需要什么权限 -->
            <security:intercept-url pattern="/**" access="ROLE_USER" />
            <!-- 定义两个用户 -->
        </security:http>
        <security:authentication-manager>
            <security:authentication-provider>
                <security:user-service>
                    <security:user name="user" password="{noop}user"
                                   authorities="ROLE_USER" />
                    <security:user name="admin" password="{noop}admin"
                                   authorities="ROLE_ADMIN" />
                </security:user-service>
            </security:authentication-provider>
        </security:authentication-manager>
    </beans>
    ```

  - 使用自己的登录页面：

    ```xml
    <!-- 配置不过滤的资源（静态资源及登录相关） -->
    <security:http security="none" pattern="/login.html" />
    <security:http security="none" pattern="/failer.html" />
    
    <security:form-login login-page="/login.html"
                         login-processing-url="/login" username-parameter="username"
                         password-parameter="password" authentication-failure-url="/failer.html"
                         default-target-url="/success.html" 
                         />
    <!-- 登出， invalidate-session 是否删除session logout-url：登出处理链接 logout-success-url：登出成功页面 
       注：登出操作 只需要链接到 logout即可登出当前用户 -->
    <security:logout invalidate-session="true" logout-url="/logout"
                     logout-success-url="/login.jsp" />
    <!-- 关闭CSRF,默认是开启的 -->
    <security:csrf disabled="true" />
    ```

  - 使用数据库进行认证：使用UserDetails接口和UserDetailsService接口。

- 登录流程分析：

  - 在登录页面，提交用户名密码后跳转到Spring Security控制。
  - 业务层，userService实现UserDetailsService接口，则Spring Security可以调用这个Service。
  -  

- Spring Security 配置：

  - web.xml中配置Filter和读入配置文件。

    ```xml
    <!-- 配置加载类路径的配置文件 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath*:applicationContext.xml,classpath*:spring-security.xml</param-value>
    </context-param>
    
    <filter>
        <filter-name>springSecurityFilterChain</filter-name>
        <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>springSecurityFilterChain</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    ```

    

  - 配置文件：

    ```xml
    <!-- 配置不拦截的资源 -->
    <security:http pattern="/login.jsp" security="none"/>
    <security:http pattern="/failer.jsp" security="none"/>
    <security:http pattern="/css/**" security="none"/>
    <security:http pattern="/img/**" security="none"/>
    <security:http pattern="/plugins/**" security="none"/>
    
    <security:http auto-config="true" use-expressions="false">
        <!-- 配置具体的拦截的规则 pattern="请求路径的规则" access="访问系统的人，必须有ROLE_USER的角色" -->
        <security:intercept-url pattern="/**" access="ROLE_USER,ROLE_ADMIN"/>
    
        <!-- 定义跳转的具体的页面 -->
        <security:form-login
                             login-page="/login.jsp"
                             login-processing-url="/login.do"
                             default-target-url="/index.jsp"
                             authentication-failure-url="/failer.jsp"
                             authentication-success-forward-url="/pages/main.jsp"
                             />
    
        <!-- 关闭跨域请求 -->
        <security:csrf disabled="true"/>
    
        <!-- 退出 -->
        <security:logout invalidate-session="true" logout-url="/logout.do" logout-success-url="/login.jsp" />
    
    </security:http>
    
    <!-- 切换成数据库中的用户名和密码 -->
    <security:authentication-manager>
        <security:authentication-provider user-service-ref="userService">
        </security:authentication-provider>
    </security:authentication-manager>
    ```
    
  - 创建相关实体类：
  
    ```java
    public class UserInfo {
        private String id;
      private String username;
        private String email;
      private String password;
        private String phoneNum;
        private int status;
        private String statusStr;
        private List<Role> roles;
    }
    
    public class Role {
        private String id;
        private String roleName;
        private String roleDesc;
        private List<Permission> permissions;
        private List<UserInfo> users;
    }
    
    public class Permission {
        private String id;
        private String permissionName;
        private String url;
        private List<Role> roles;
    }
    ```
  
  - 编写指定的UserService接口和实现类、Dao：
  
    ```java
    public interface UserService extends UserDetailsService {
    }
  
    @Service("userService")
    @Transactional
    public class UserServiceImpl implements UserService {
        @Autowired
        private UserDao userDao;
        @Override
        public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
          UserInfo userInfo = null;
            try {
                userInfo = userDao.findByUsername(username);
            } catch (Exception e) {
                e.printStackTrace();
            }
          System.out.println("userInfo" + userInfo);
            User user = new User(userInfo.getUsername(), "{noop}"+userInfo.getPassword(), userInfo.getStatus()==0?false:true,
                    true,true,true, getAuthority(userInfo.getRoles()));
            return user;
        }
    
        private List<SimpleGrantedAuthority> getAuthority(List<Role> roles) {
            List<SimpleGrantedAuthority> list = new ArrayList<>();
            for(Role role : roles) {
                list.add(new SimpleGrantedAuthority(role.getRoleName()));
            }
            return list;
        }
    }
    
    public interface UserDao {
        @Select("select * from user where username=#{username}")
        UserInfo findByUsername(String username) throws Exception;
    }
    ```
    
  
- 用户注销：

  - 在Spring-Security.xml中添加：

    ```xml
    <security:logout invalidate-session="true" logout-url="/logout.do" logout-success-url="/login.jsp" />
    ```

  - 访问logout.do时会自动注销，跳转到login.jsp，并且杀死session。

## 6、用户操作

- 用户查询：

  - 点击用户管理，对/user/findAll.do请求userController查询所有用户。
  - 调用Service和Dao进行查询。返回userInfo实体类的列表。

- 用户添加：

  - 点击左上角的新建，点击后跳转到录入用户信息的表单。

  - 密码在存储的数据库中，密码应该是加密的。拼接密码时去掉`{noop}`。

  - 在Spring-security.xml中配置加密类：`BCryptPasswordEncoder`。

    ```xml
    <!-- 配置加密类 -->
    <bean id="passwordEncoder" class="org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder"/>
    ```

  - 在Service中进行加密，`password=BCryptPasswordEncoder.encode(passwo)`。

  - 在前述操作后，如果去掉`{noop}`，还需要在登陆前解密。在Spring-security.xml中配置登录的密码是加密的：

    ```xml
    <security:authentication-manager>
        <security:authentication-provider user-service-ref="userService">
            <!--配置加密的方式-->
            <security:password-encoder ref="passwordEncoder"/>
        </security:authentication-provider>
    </security:authentication-manager>
    ```

- 用户详情：

  - 在user-list点击详情后，给userController请求findById.do。并携带当前用户的id。

  - Service和Dao完成查询用户相关信息的操作。需要查询包含用户的角色信息以及角色拥有的权限信息。

  - 返回userInfo信息，跳转到user-show页面。

  - Dao的注解：

    - UserDao：

      ```java
      @Select("select * from user where id=#{id}")
      @Results({@Result(id = true, property = "id", column = "id"),
                @Result(property = "username", column = "username"),
                @Result(property = "email", column = "email"),
                @Result(property = "password", column = "password"),
                @Result(property = "phoneNum", column = "phoneNum"),
                @Result(property = "status", column = "status"),
                @Result(property = "roles", column = "id", javaType = java.util.List.class, many = @Many(select = "cn.iwehdio.dao.RoleDao.findRoleByUserId"))
               })
      UserInfo findById(String id);
      ```

    - RoleDao：

      ```java
      @Select("select * from role where id in" +
              "(select roleId from users_role where userId=#{userId})")
      @Results({
          @Result(id = true, property = "id", column = "id"),
          @Result(property = "roleName", column = "roleName"),
          @Result(property = "roleDesc", column = "roleDesc"),
          @Result(property = "permissions", column = "id", javaType = java.util.List.class, many = @Many(select = "cn.iwehdio.dao.PermissionDao.findByRoleId"))
      })
      List<Role> findRoleByUserId(String userId) throws Exception;
      ```

    - PermissionDao：

      ```java
      @Select("select * from permission where id in " +
              "(select permissionId from role_permission where roleId = #{rid})")
      List<Permission> findByRoleId(String rid) throws Exception;
      ```




## 7、角色操作

- 查询所有角色：
  - 左侧菜单中点击角色管理，向控制器发送请求/role/findAll.do。
  - 调用Service和Dao完成查询。返回的是角色信息的列表。
  - 跳转到role-list.jsp进行显示。
- 角色添加：
  - role-list.jsp上点击新建，跳转到新建角色页面role-add.jsp。
  - 录入role相关的信息，封装到表单中。
  - 表单提交到/role/save.do。
  - 调用Service和Dao完成添加操作。重定向到角色显示页面。



## 8、资源权限操作

- 查询所有资源权限：
  - 左侧菜单中点击资源权限，向控制器发送请求/permission/findAll.do。
  - 调用Service和Dao完成查询。返回的是资源权限信息的列表。
  - 编写前端页面。
- 资源权限的添加：
  - 类似角色的添加操作。



## 9、关联

- 用户关联角色：

  - user-list下点击添加角色按钮，请求控制器/user/findUserByIdAndAllRole

  - 根据id查询用户和用户可以添加的角色。

  - 调用Service和Dao，返回用户信息和可以添加的角色。

  - 跳转到user-role-add，显示用户信息和用户可以添加的角色信息。

  - 请求控制器/user/addRoleToUser，给用户添加角色（在数据库中插入信息）。

  - 在UserDao中，由于有多个参数，需要使用`@Param`进行绑定：

    ```java
    @Insert("insert into users_role(userId, roleId) values(#{userId},#{roleId})")
    void addRolesToUser(@Param("userId") String userId, @Param("roleId") String roleId);
    ```

- 角色关联权限：

  - 与用户管理角色的流程类似。



## 10、权限控制

- 方法级权限控制：

  - `JSR-250`注解：

    - 需要在spring-security.xml中开启：

      ```xml
      <security:global-method-security jsr250-annotations="enabled"></security:global-method-security>
      ```

    - `@RolesAllowed({"需要的权限名称"})`：在控制器指定的方法上使用，指定权限的字符串。

    - 必须在pom.xml 中导入依赖。

      ```xml
      <dependency>
          <groupId>javax.annotation</groupId>
          <artifactId>jsr250-api</artifactId>
          <version>1.0</version>
      </dependency>
      ```

    - 在web.xml中配置403（权限不足）的页面：

      ```xml
      <error-page>
          <error-code>403</error-code>
          <location>/403.jsp</location>
      </error-page>
      ```

  - `@Secured`注解：

    - 需要在spring-security.xml中开启：

      ```xml
      <security:global-method-security secured-annotations="enabled"></security:global-method-security>
      ```

    - `@Secured({"ROLE_需要的权限名称"})`：在控制器指定的方法上使用，指定权限的字符串。前缀不能省略。（JSR-250可以省略前缀）

  - 基于支持表达式的注解：

    - `@PreAuthorize`：在方法调用之前，基于SPEL表达式的计算结果来限制对方法的访问。

    - 需要在spring-security.xml中开启：

      ```xml
      <security:global-method-security pre-post-annotations="enabled"></security:global-method-security>
      ```

    - `@PreAuthorize("hasRole('ROLE_需要的权限名称')")`：功能与之前注解相同。

    - `@PreAuthorize("authentication.principal.username=='用户名'")`：只有某个用户才能访问该方法。

- 页面端权限控制：

  - pom.xml中导入依赖：

    ```xml
    <dependency>
      <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-taglibs</artifactId>
        <version>${spring.security.version}</version>
    </dependency>
    ```

  - 在jsp页面中导入标签：

    ```jsp
    <%@taglib uri="http://www.springframework.org/security/tags" prefix="security"%>
    ```

  - `authentication`标签：获取当前正在操作的用户信息。

    - 显示当前用户信息：

      ```jsp
      <security:authentication property="principle.username"></security:authentication>
      ```

  - `authorize`标签：控制页面上某些标签是否可以显示。

    - 控制页面显示：

      ```jsp
      <security:authorize access="hasRole('需要的权限名称')">控制显示的内容
      </security:authorize>
      ```

    - 需要开启SPEL表达式。



## 11、AOP日志

- 记录下在系统中的任何操作。

- 创建日志信息对应的实体类。

- 创建切面，获取执行时长、IP地址、url、操作者、执行的类和方法。
  - 获取执行时长：前置通知获取开始时间。后置通知获取结束时间。
  - IP地址：通过request对象，需要先在web.xml中配置一个监听器RequestContextListener。然后自动注入request，使用.getRemoteAddr()获取IP。
  - 获取url：通过反射获取注解的value。
  - 获取操作者：使用SecurityContext对象或request的session获取。
  - 获取执行的类与方法：getclass()和getMethod()方法。
  
  ```java
  @Component
  @Aspect
  public class LogAop {
  
      @Autowired
      private HttpServletRequest request;
  
      @Autowired
      private ISysLogService sysLogService;
  
      private Date visitTime; //开始时间
      private Class clazz; //访问的类
      private Method method;//访问的方法
  
      //前置通知  主要是获取开始时间，执行的类是哪一个，执行的是哪一个方法
      @Before("execution(* cn.iwehdio.control.*.*(..))")
      public void doBefore(JoinPoint jp) throws NoSuchMethodException {
          visitTime = new Date();//当前时间就是开始访问的时间
          clazz = jp.getTarget().getClass(); //具体要访问的类
          String methodName = jp.getSignature().getName(); //获取访问的方法的名称
          Object[] args = jp.getArgs();//获取访问的方法的参数
  
          //获取具体执行的方法的Method对象
          if (args == null || args.length == 0) {
              method = clazz.getMethod(methodName); //只能获取无参数的方法
          } else {
              Class[] classArgs = new Class[args.length];
              for (int i = 0; i < args.length; i++) {
                  classArgs[i] = args[i].getClass();
              }
              clazz.getMethod(methodName, classArgs);
          }
      }
  
      //后置通知
      @After("execution(* cn.iwehdio.control.*.*(..))")
      public void doAfter(JoinPoint jp) throws Exception {
          long time = new Date().getTime() - visitTime.getTime(); //获取访问的时长
  
          String url = "";
          //获取url
          if (clazz != null && method != null && clazz != LogAop.class) {
              //1.获取类上的@RequestMapping("/orders")
              RequestMapping classAnnotation = (RequestMapping) clazz.getAnnotation(RequestMapping.class);
              if (classAnnotation != null) {
                  String[] classValue = classAnnotation.value();
                  //2.获取方法上的@RequestMapping(xxx)
                  RequestMapping methodAnnotation = method.getAnnotation(RequestMapping.class);
                  if (methodAnnotation != null) {
                      String[] methodValue = methodAnnotation.value();
                      url = classValue[0] + methodValue[0];
  
                      //获取访问的ip
                      String ip = request.getRemoteAddr();
  
                      //获取当前操作的用户
                      SecurityContext context = SecurityContextHolder.getContext();//从上下文中获了当前登录的用户
                      User user = (User) context.getAuthentication().getPrincipal();
                      String username = user.getUsername();
  
                      //将日志相关信息封装到SysLog对象
                      SysLog sysLog = new SysLog();
                      sysLog.setExecutionTime(time); //执行时长
                      sysLog.setIp(ip);
                      sysLog.setMethod("[类名] " + clazz.getName() + "[方法名] " + method.getName());
                      sysLog.setUrl(url);
                      sysLog.setUsername(username);
                      sysLog.setVisitTime(visitTime);
  
                      //调用Service完成操作
                      sysLogService.save(sysLog);
                  }
              }
          }
  
      }
  }
  ```
  
  



