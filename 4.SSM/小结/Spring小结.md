## 1、IOC

### （1）XML配置

- IOC 的 bean.xml 约束：

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd">
  </beans>
  ```

- 获取核心容器：

  ```java
  ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
  ```

- `<bean>`标签：

  - 表示把对象交给Spring管理，存入IOC容器。
  - 使用默认构造方法创建：
    - id 属性：键，调用时引用该id。
    - class 属性：所要创建的类的全类名。
  - 使用工厂方法创建（使用某个类中的方法创建）：
    - factory-bean 属性：工厂类全类名。
    - factory-method 属性：工厂中创建对象的方法。
  - 使用静态工厂中的静态方法创建：
    - class 属性：工厂类全类名。
    - factory-method 属性：静态方法。
  - bean 的作用范围：
    - 默认情况下是单例的。也就是多次创建的是同一个对象。
    -  scope 属性：用于指定作用范围：
      - singleton：单例、默认值。
      - prototype：多例。
    - init-method和destroy-method 属性用于指定初始化前和销毁后的方法。

- 依赖注入：

  - 使用构造函数注入：`<bean>` 标签中的 `<constructor-arg>`标签。
    - value 属性：用于提供基本类型和String类型的数据。
    - ref 属性：用于引入其他的 bean 类型数据，即在 Spring 的 IOC 核心容器中出现过的 bean 对象。
  - set 方法注入：`<bean>` 标签中的 `<property>`标签。
    - name 属性：注入时调入的 set 方法名称（属性名）。
    - value 属性：提供基本类型和String的数据。
    - ref 属性：提供 bean 类型数据。

### （2）注解配置

- 启用注解并配置注解扫描的包：

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans 
      xmlns:context="http://www.springframework.org/schema/context"
      xsi:schemaLocation="http://www.springframework.org/schema/context
          http://www.springframework.org/schema/context/spring-context.xsd">
  
      <context:annotation-config/>
      <context:component-scan base-package="所要扫描的包"></context:component-scan>
      <context:annotation-config/>
  
  </beans>
  ```

- maven 导入注解支持：

  ```xml
  <groupId>org.springframework</groupId>
  <artifactId>spring-context</artifactId>
  ```

- `@Component`注解：

  - 用于将当前类对象存入Spring容器中。
  - value 属性：用于指定 bean 的 id（取出时的索引），默认为当前类名（首字母小写）。
  - 相同的功能：
    - `@Controller`：一般用于表现层。
    - `@Service`：一般用于业务层。
    - `@Respository`：一般用于持久层。

- `@Autowired`注解：

  - 自动按照类型注入，只要容器中有唯一的一个 bean 对象和要注入的变量类型匹配，就可以注入成功。

- `@Qualifier`注解：

  - value 属性：在按照类型注入的基础上再按照名称注入。在给类成员变量注入时不能单独使用，但在给成员方法注入时可以单独使用。

- `@Resource`注解：

  - name 属性：直接按照 bean 的 id 注入，指定 id 。

- `@Value`注解：

  - value 属性：指定数据的值，用于注入基本类型和String类型。可以使用Spring的 EL 表达式（`$(表达式)`）。

- `@Scope`注解：

  - value 属性：用于指定 bean 的作用范围取值。

- 和生命周期相关的注解：

  - `@PreDestroy`：用于指定销毁方法。
  - `@PostConstruct`：用于指定初始化方法。

### （3）新注解

- 创建配置类`SpringConfiguration`，它的作用是与`bean.xml`相同的。

- `@Configuration`注解：

  - 表示当前类是一个配置类。

  - 获取核心容器：

    ```java
    ApplicationContext ac = new AnnotationConfigApplicationContext(SpringConfiguratuon.class);
    ```

- `@ComponentScan`注解：

  - 指定使用注解所要扫描的包。
  - value 属性/basePackages 属性：=`{"包名"}`。

- `@Bean`注解：

  - 用于把当前的返回值作为 bean 对象存入 Spring 的 IOC 中。
  - name 属性：指定 bean 的 id，默认值是方法名。

- `@Import`注解：

  - 用于导入其他的配置类（父配置类的子配置类）。
  - value 属性：子配置类的字节码对象。

- `@PropertySource`注解：

  - 用于配置 property 的文件位置。

  - value 属性：指定文件的名称和路径，classpath 关键字表示文件在类路径下。



## 2、AOP

### （1）XML配置

- AOP 的 bean.xml 约束：

  ```xml
  <beans xmlns:aop="http://www.springframework.org/schema/aop"
         xsi:schemaLocation="http://www.springframework.org/schema/aop
          http://www.springframework.org/schema/aop/spring-aop.xsd">
  </beans>
  ```

- maven 导入切入点表达式解析器：

  ```xml
  <groupId>org.aspectj</groupId>
  <artifactId>aspectjweaver</artifactId>
  ```

- `<aop:config>`标签：

  - 表明开始AOP的配置，AOP的配置都在该标签下。

- `<aop:aspect>`标签：

  - 表明配置切面，在`<aop:config>`标签内，通知配置都在此标签内。

  - id 属性：给切面提供一个唯一索引。
  - ref 属性：指定通知类bean的id。

- `<aop:before>`标签：

  - 表示前置通知，在`<aop:aspect>`标签内。

  - method 属性：用于指定通知类中那个方法是前置通知。
  - pointcut 属性：指定切入点表达式，含义是指的是对业务层中的那些方法进行增强。
  - `<aop:after-returning>`标签：表示后置通知。
  - `<aop:after-throwing>`标签：表示异常通知。
  - `<aop:after>`标签：表示最终通知。
  - `<aop:around>`标签：表示环绕通知。
    - 使用`ProceedingJoinPoint`接口的方法`proceed()`方法调用切入点方法，使用`getArgs()`方法获取参数：

- 切入点表达式：`execution(表达式)`。

  - 表达式：`访问修饰符 返回值 全类名.方法名(参数列表)`。
  - 全通配写法：`* *..*.*(..)`。
  - 一般而言，切入点写到业务层实现类下的所有方法。
  - 通用切入点表达式`<aop:pointcut>`：
    - id 属性：指定被索引的键。在`<aop:before>`标签使用 pointcut-ref 指定。
    - expression 属性：切入点表达式。

### （2）注解配置

- AOP 注解配置的约束：AOP的约束 + 注解的约束。

- 配置Spring开启AOP注解支持：

  ```xml
  <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
  ```

  - 也可以使用注解`@EnableAspectJAutoProxy`。

- `@Aspect`表示当前类是一个切面类（通知类），在Logger类上。

- `@Before(切入点表达式)`：前置通知标签。

- `@AfterReturning(切入点表达式)`：后置通知标签。

- `@AfterThrowing(切入点表达式)`：异常通知标签。

- `@After(切入点表达式)`：最终通知标签。

- `@Around(切入点表达式)`：环绕通知标签。

- 通用切入点表达式：

  ```java
  @Pointcut("表达式")
  private void pt(){}
  @Before("pt()")
  public void before PrintLog(){}
  ```




## 3、事务控制

### （1）XML配置

- 事务控制的 bena.xml 配置：

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns:tx="http://www.springframework.org/schema/tx"
      xsi:schemaLocation="http://www.springframework.org/schema/tx
          http://www.springframework.org/schema/tx/spring-tx.xsd">
  </beans>
  ```

- maven 注册事务控制：

  ```xml
  <groupId>org.springframework</groupId>
  <artifactId>spring-tx</artifactId>
  ```

- 配置事务管理器：

  ```xml
  <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
      <property name="dataSource" ref="datasource"></property>
  </bean>
  ```

- `<tx:advice>`标签：

  - 配置事务通知。
  - id 属性：事务通知的唯一标志。
  - transaction-manager 属性：给事务通知提供一个事务管理器引用。

- `<aop:advisor>`标签：

  - 在`<aop:config>`标签内，建立事务通知和切入点表达式的对应关系。
  - advice-ref 属性：指向事务通知。
  -  pointcut-ref 属性：指向切入点表达式。

- `<tx:attributes>`标签：

  - 在`<tx:advice>`标签内，配置事务的属性。

- `<tx:method>`标签：

  - 在`<tx:attributes>`标签内，配置所要拦截的方法。
  - name 属性：指定Service中的的方法名，可以用通配符。
  - isolation 属性：指定事务的隔离级别，默认DEFAULT使用数据库的隔离级别。
  - propagation 属性：指定事务的传播行为，默认REQUIRED一定有事务（增删改）。查询选择SUPPORTS。
  - read-only 属性：指定事务是否只读，只有查询方法可以设置为true，默认false。
  - timeout 属性：指定事务的超时时间，默认-1永不超时。
  - no-rollback-for 属性：指定一个异常，产生该异常时，事务不回滚，产生其他异常时，事务回滚。没有默认值，表示任何异常都回滚。
  - rollback-for 属性：指定一个异常，产生该异常时，事务回滚，产生其他异常时，事务不回滚。没有默认值，表示任何异常都回滚。

### （2）注解配置

- 配置事务管理器并，开启Spring对注解事务的支持：

  ```xml
  <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
      <property name="dataSource" ref="datasource"></property>
  </bean>
  <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
  ```

- `@Transactional`注解：

  - 在需要事务支持的地方使用该注解。
  - 在注解的属性中进行对事务属性的控制。

