## 1、注解

- 用于创建对象的注解：与`<bean>`标签作用相同。

  - `@Compoent`：用于将当前类对象存入Spring容器中。属性 value 用于指定 bean 的 id（取出时的索引），默认为当前类名（首字母小写）。

  - 需要在配置文件中告知Spring在创建容器时要扫描注解的包：

    - 注册 context 名字空间：

      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
              http://www.springframework.org/schema/beans/spring-beans.xsd
              http://www.springframework.org/schema/context
              http://www.springframework.org/schema/context/spring-context.xsd">
      
          <context:annotation-config/>
      
      </beans>
      ```

    - 配置要扫描的包：

      ```xml
      <context:component-scan base-package="所要扫描的包"></context:component-scan>
      ```

  - `@Controller`：一般用于表现层。

  - `@Service`：一般用于业务层。

  - `@Respository`：一般用于持久层。

  - 以上三个注解的作用与属性与`@Component`相同。

- 用于注入数据的注解：与`<constructor-arg>`标签作用相同。

  -  `@Autowired`：自动按照类型注入，只要容器中有唯一的一个 bean 对象和要注入的变量类型匹配，就可以注入成功。
  - 可以放在成员变量或方法上。
  - 在使用注解注入时，set 方法不是必要的。
  - 该注解的实现原理：通过成员变量 / 成员方法的 数据类型/返回值类型，去IOC容器中找数据类型与之相同的 bean 对象进行注入。
  - 如果IOC容器类型中有多个类型匹配，会将变量名称 / 方法名称 作为 id （即键）进行查询，对 id 索引到的对象进行注入。
  - `@Qualifier`：在按照类型注入的基础上再按照名称注入。在给类成员变量注入时不能单独使用，但在给成员方法注入时可以单独使用。

    - 给类成员变量注入时，需要跟`@Autowired`配合。

    - 给类成员方法注入时，可以单独使用。说明注入的是 id 为 ds 的对象。

      ```java
      @Bean(name = "runner")
      public QueryRunner creatQueryRunner(@Qualifier("ds") DataSource dataSource) {
          return new QueryRunner(dataSource);
      }
      ```

  - `@Resource`：直接按照 bean 的 id 注入，可以用 name 属性指定 id 。
  - `@Value`：用于注入基本类型和String类型。value 属性指定数据的值，可以使用Spring的 EL 表达式（`$(表达式)`）。
  - 集合类型只能通过 XML 注入。
  
- 用于改变作用范围的注解：与`scope`属性作用相同。

  - `@Scope`：用于指定 bean 的作用范围取值。
  - value 属性可以指定：singleton 和 prototype 。

- 和生命周期相关的注解：与 init-method 和 destroy-method 作用相同。

  - `@PreDestroy`：用于指定销毁方法。
  - `@PostConstruct`：用于指定初始化方法。

- 示例：

  - 三层架构，使用dbutils查询数据库。

  - 在`AccountDaoImpl`中，创建引用 QueryRunner 和 set 方法。

  - 在`AccountServiceImpl`中，创建引用 AccountDao 和 set 方法。

  - 步骤：

    1. 将`AccountServiceImpl`对象创建到IOC容器中，同时注入`accountDao`成员变量。
    2. 将`AccountDaoImpl`对象创建到IOC容器中，同时注入`QueryRunner`成员变量。
    3. 将`QueryRunner`对象创建到IOC容器中，使用构造方法注入`datasource`d成员变量。
    4. 将`datasource`对象创建到IOC容器中，注入数据库连接配置。

  - 使用 Spring xml注入：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd">
    
        <bean id="accountService" class="cn.iwehdio.service.Impl.AccountServiceImpl">
            <property name="accountDao" ref="accountDao"></property>
        </bean>
    
        <bean id="accountDao" class="cn.iwehdio.dao.Impl.AccountDaoImpl">
            <property name="runner" ref="runner"></property>
        </bean>
    
        <bean id="runner" class="org.apache.commons.dbutils.QueryRunner">
            <constructor-arg name="ds" ref="datasource"></constructor-arg>
        </bean>
    
        <bean id="datasource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
            <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
            <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/spring"></property>
            <property name="user" value="root"></property>
            <property name="password" value="root"></property>
        </bean>
    
    </beans>
    ```

  - Spring帮我们将引用链接到了 IOC 容器中的 bean 对象，降低了使用`new`产生的依赖。
  
  - 使用注解优化：
  
    - 在 `bean.xml`中指定要扫描的包，删除 AccountServiceImpl 和 AccountDaoImpl 的 bean 配置：
  
      ```xml
      <context:component-scan base-package="cn.iwehdio"></context:component-scan>
      ```
  
    - 在 AccountServiceImpl 类上添加注解`@Service("accountService")`，在成员变量 private AccountDao accountDao 上添加注解 `@Autowired`。
  
    - 在 AccountDaoImpl 类上添加注解 `@Repository("accountDao")`，在成员变量 private QueryRunner runner 上添加注解`@Autowired`。



## 2、Spring 新注解

- Spring 新注解：

  - 可以不使用 xml 进行配置。

  - 创建配置类`SpringConfiguratuon`，它的作用是与`bean.xml`相同的。

  - 添加注解`@Configuration`，表示当前类是一个配置类。

  - 添加注解`@ComponentScan`，指定使用注解所要扫描的包。

    - 使用：`@ComponentScan(basePackages={"包名"})`，也可以使用 value 属性。

    - 使用此注解等同于在 xml 中配置了`<context:component-scan>`。

    - 示例：

      ```java
      @Configuration
      @ComponentScan(basePackages = "cn.iwehdio")
      public class SpringConfiguratuon {}
      ```

      

  - 创建一个方法，创建 QueryRunner 对象：

    ```java
    @Bean(name = "runner")
    public QueryRunner creatQueryRunner(DataSource dataSource) {
        return new QueryRunner(dataSource);
    }
    ```

    - 添加注解`@Bean`，用于把当前的返回值作为 bean 对象存入 Spring 的 IOC 中。
    - 使用name 属性指定 bean 的 id，默认值是方法名。
    - 当使用注解配置方法时，如果方法有参数，Spring 会去容器中查找有没有可用的 bean 对象。相当于`Autowired`。

  - 创建一个方法，创建 DataSource 对象：

    ```java
    @Bean(name = "dataSource")
    public DataSource creatDataSource() {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        try {
            dataSource.setDriverClass("com.mysql.jdbc.Driver");
            dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/spring");
            dataSource.setUser("root");
            dataSource.setPassword("root");
        } catch (PropertyVetoException e) {
            e.printStackTrace();
        }
        return dataSource;
    }
    ```
  
- 在使用时，需要更换为注解的方法：
  
    ```java
    ApplicationContext ac = new AnnotationConfigApplicationContext(SpringConfiguratuon.class);
  ```
  
  
  
  - 使用注解配置的对象是默认单例的，可以使用`@Scope`注解在方法上配置。
  
  - 当配置类的字节码作为 `AnnotationConfigApplicationContext()` 方法的参数时，`@Configuration`可以不写。
  
    - 如果没有作为字节码参数读入，而只是所要扫描包下的类，则`@Configuration`必须写。
  
  - 添加`@Import`注解，用于导入其他的配置类（父配置类的子配置类），value 属性是字节码对象。
  
    ```java
    @Import(JdbcConfig.class)
    public class SpringConfiguratuon {}
    ```
    
  - 添加`@PropertySource`注解，用于配置 property 的文件位置。
  
    - value 属性指定文件的名称和路径。
  
    - classpath 关键字表示文件在类路径下。
  
    - 示例：
  
      ```java
      @PropertySource("classpath:JdbcConfig")
      public class SpringConfiguratuon {
          @Value("${jdbc.username}")
          private String username;
      }
      ```
  
- Spring 整合 junit：

  -  Junit 默认情况下，main 方法不会启动Spring 创建 IOC 容器。

  - 导入整合坐标：

    ```xml
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>5.2.6.RELEASE</version>
    </dependency>
    ```

  - 使用 Junit 提供的注解`@Runwith`将原有的main方法替换了。

  - 使用`@ContextConfigration`告知 Spring运行器，IOC容器式怎样创建的。

    - locations 属性：指定 xml 的路径，classpath 关键字表示在类路径下。
    - classes 属性：指定注解类所在的位置。

    ```java
    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration(classes = SpringConfiguratuon.class)
    //或 @ContextConfiguration(locations = "classpath:bean.xml")
    public class test {}
    ```

    



  

