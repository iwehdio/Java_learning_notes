## 1、Spring

- 整合方式：用Spring去整合其他的框架，XML + 注解。

- maven坐标：

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
  
    <groupId>org.example</groupId>
    <artifactId>ssm_zhenghe</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>
  
    <name>ssm_zhenghe Maven Webapp</name>
    <!-- FIXME change it to the project's website -->
    <url>http://www.example.com</url>
  
    <properties>
      <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
      <maven.compiler.source>1.8</maven.compiler.source>
      <maven.compiler.target>1.8</maven.compiler.target>
      <spring.version>5.0.2.RELEASE</spring.version>
      <slf4j.version>1.6.6</slf4j.version>
      <log4j.version>1.2.12</log4j.version>
      <mysql.version>5.1.6</mysql.version>
      <mybatis.version>3.4.5</mybatis.version>
    </properties>
  
    <dependencies>
      <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.11</version>
        <scope>test</scope>
      </dependency>
      <!-- spring -->
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
        <artifactId>spring-web</artifactId>
        <version>${spring.version}</version>
      </dependency>
  
      <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>${spring.version}</version>
      </dependency>
  
      <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>${spring.version}</version>
      </dependency>
  
      <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-tx</artifactId>
        <version>${spring.version}</version>
      </dependency>
  
      <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>${spring.version}</version>
      </dependency>
  
      <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
      </dependency>
  
      <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>${mysql.version}</version>
      </dependency>
  
      <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>2.5</version>
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
      </dependency>
  
      <!-- log start -->
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
      </dependency>
      <!-- log end -->
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
  
    </dependencies>
  
    <build>
      <finalName>ssm_zhenghe</finalName>
      <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
        <plugins>
          <plugin>
            <artifactId>maven-clean-plugin</artifactId>
            <version>3.1.0</version>
          </plugin>
          <!-- see http://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_war_packaging -->
          <plugin>
            <artifactId>maven-resources-plugin</artifactId>
            <version>3.0.2</version>
          </plugin>
          <plugin>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.0</version>
          </plugin>
          <plugin>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>2.22.1</version>
          </plugin>
          <plugin>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.2.2</version>
          </plugin>
          <plugin>
            <artifactId>maven-install-plugin</artifactId>
            <version>2.5.2</version>
          </plugin>
          <plugin>
            <artifactId>maven-deploy-plugin</artifactId>
            <version>2.8.2</version>
          </plugin>
        </plugins>
      </pluginManagement>
    </build>
  </project>
  ```



- 搭建基础环境：

  - domin包及实体类。
  - dao包及接口。
  - service包及接口和实现类。
  - controller包及控制器。

- 搭建Spring框架：

  - 创建applicationContext.xml，并配置约束：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
    	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    	xmlns:context="http://www.springframework.org/schema/context"
    	xmlns:aop="http://www.springframework.org/schema/aop"
    	xmlns:tx="http://www.springframework.org/schema/tx"
    	xsi:schemaLocation="http://www.springframework.org/schema/beans 
    	http://www.springframework.org/schema/beans/spring-beans.xsd
    	http://www.springframework.org/schema/context
    	http://www.springframework.org/schema/context/spring-context.xsd
    	http://www.springframework.org/schema/aop
    	http://www.springframework.org/schema/aop/spring-aop.xsd
    	http://www.springframework.org/schema/tx 
    	http://www.springframework.org/schema/tx/spring-tx.xsd">
    	
    </beans>
    ```

  - 配置扫描注解的包：

    ```xml
    <!-- Spring注解只扫dao和service -->
    <context:component-scan base-package="cn.iwehdio">
        <!-- 那些注解不扫描 -->
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
    ```

  - 在AccountServiceImpl类上加注解`@Service("accountService")`。

  - 测试Spring：

    ```java
    @Test
    public void test1() {
        ApplicationContext ac = new ClassPathXmlApplicationContext("classpath:applicationContext.xml");
        AccountService as = (AccountService) ac.getBean("accountService");
        as.findAll();
    }
    ```



## 2、SpringMVC

- 搭建SpringMVC框架：

  - web.xml下配置前端控制器：

    ```xml
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    
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
    ```

  - 创建springmvc.xml，并配置约束：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:mvc="http://www.springframework.org/schema/mvc"
           xmlns:context="http://www.springframework.org/schema/context"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="
            http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
        
    </beans>
    ```

  - 配置：

    ```xml
    <!-- 开启注解扫描，只扫描controll -->
    <context:component-scan base-package="cn.iwehdio">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
    
    <!-- 配置视图解析器 -->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>
    
    <!-- 过滤静态资源 -->
    <mvc:resources location="/css/" mapping="/css/**" />
    <mvc:resources location="/images/" mapping="/images/**" />
    <mvc:resources location="/js/" mapping="/js/**" />
    
    <!-- 开启SpringMVC注解支持 -->
    <mvc:annotation-driven></mvc:annotation-driven>
    ```

  - 配置控制器：

    ```java
    @Controller
    @RequestMapping("/account")
    public class AccountController {
    
        @RequestMapping("findAll")
        public String findAll(){
            System.out.println("表现层查询所有");
            return "list";
        }
    }
    ```

  - 编写index.jsp和list.jsp。



- Spring整合SpringMVC：

  - 启动Tomcat时，自动加载了springmvc.xml，但还需要加载applicationContext.xml文件。

    - ServletContext生命周期与服务器相同。

    - 有一类监听器，监听ServletContext的创建和销毁。

    - 用这个监听器加载Spring的配置文件applicationContext.xml。

    -  配置监听器，监听器会默认加载WEB-INF目录下的applicationContext.xml文件，因此还需要配置路径到类路径下。（resources路径编译后在类路径下）

      ```xml
      <listener>
          <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
      </listener>
      <context-param>
          <param-name>contextConfigLocation</param-name>
          <param-value>classpath:applicationContext.xml</param-value>
      </context-param>
      ```

  - 在控制器中注入：

    ```java
    @Autowired
    private AccountService accountService;
    ```



## 3、Mybatis

- 搭建Mybatis框架：

  - 创建SqlMapConfig.xml，配置约束：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE configuration
    PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-config.dtd">
    ```

  - 配置：

    ```xml
    <configuration>
        <!-- 配置环境 -->
        <environments default="mysql">
            <environment id="mysql">
                <transactionManager type="JDBC"></transactionManager>
                <dataSource type="POOLED">
                    <property name="driver" value="com.mysql.jdbc.Driver"/>
                    <property name="url" value="jdbc:mysql://localhost:3306/ssm"/>
                    <property name="username" value="root"/>
                    <property name="password" value="root"/>
                </dataSource>
            </environment>
        </environments>
        <!-- 映射配置文件 -->
        <mappers>
            <mapper class="cn.iwehdio.dao.AccountDao"></mapper>
            <!-- 或扫描包下的所有接口 
            <package name="cn.iwehdio.dao"/>-->
        </mappers>
    </configuration>
    ```

  - 在dao接口上用注解配置SQL语句：

    ```java
    @Select("select * from account")
    public List<Account> findAll();
    
    @Select("insert into account (name,money) values (#{name},#{money})")
    public void saveAccount(Account account);
    ```

  - 测试（增删改需要commit）：

    ```java
    public class testMybatis {
        @Test
        public void test() throws IOException {
            //加载配置文件
            InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
            //创建SqlSessionFactory对象
            SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
            //创建SqlSession
            SqlSession sqlSession = factory.openSession();
            //获取代理对象
            AccountDao mapper = sqlSession.getMapper(AccountDao.class);
            //进行查询
            List<Account> all = mapper.findAll();
            for (Account a : all) {
                System.out.println(a.getName());
            }
            //释放资源
            sqlSession.close();
            in.close();
        }
    }
    ```

    

- Spring整合Mybatis：

  - applicationConfigContext.xml下配置Mybatis：

    ```xml
    <!-- 整合Mybatis -->
    <!-- 配置连接池 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/ssm"/>
        <property name="user" value="root"/>
        <property name="password" value="root"/>
    </bean>
    <!-- 配置工厂对象 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <!-- 配置接口所在的包 -->
    <bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="cn.iwehdio.dao"></property>
    </bean>
    ```

  - 可以去掉SqlMapConfig.xml。

  - 在AccountDao接口上加`@Repository`注解。

  - 在AccountServiceImpl中注入：

    ```java
    @Service("accountService")
    public class AccountServiceImpl implements AccountService {
    
        @Autowired
        private AccountDao accountDao;
    
        @Override
        public List<Account> findAll() {
            System.out.println("业务层findall");
            return accountDao.findAll();
        }
    
        @Override
        public void saveAccount(Account account) {
            System.out.println("业务层save");
            accountDao.saveAccount(account);
        }
    }
    ```

  - 控制器查询并跳转：

    ```java
    @RequestMapping("findAll")
    public String findAll(Model model){
        System.out.println("表现层查询所有");
        List<Account> all = accountService.findAll();
        model.addAttribute("list", all);
        return "list";
    }
    ```

  - 前端显示：

    ```jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
    <%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
    <html>
    <head>
        <title>Title</title>
    </head>
    <body>
        <h3>findAll</h3>
        <c:forEach items="${list}" var="account">
            ${account.name}
        </c:forEach>
    </body>
    </html>
    ```



- 整合声明式事务管理：

  - applicationConfigContext.xml下配置事务管理：

    ```xml
    <!-- 配置事务管理器 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <!-- 配置事务通知 -->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="find*" read-only="true"/>
            <tx:method name="*" isolation="DEFAULT"/>
        </tx:attributes>
    </tx:advice>
    <!-- 配置AOP增强 -->
    <aop:config>
        <aop:advisor advice-ref="txAdvice" pointcut="execution(* cn.iwehdio.service.Impl.*ServiceImpl.*(..))"></aop:advisor>
    </aop:config>
    ```

  - 控制器保存账户：

    ```java
    @RequestMapping("save")
    public void save(Account account, HttpServletRequest request, HttpServletResponse response) throws IOException {
        accountService.saveAccount(account);
        response.sendRedirect(request.getContextPath()+"/account/findAll");
        return;
    }
    ```