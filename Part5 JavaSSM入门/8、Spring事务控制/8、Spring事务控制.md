## 1、JdbcTemplate

> Spring框架提供的一个对象，对原始Jdbc API对象的简单封装。

- 作用：用于与数据库交互，实现对表的CRUD操作。

- 创建工程，导入依赖：

  ```xml
  <dependencies>
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-context</artifactId>
          <version>5.0.2.RELEASE</version>
      </dependency>
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-jdbc</artifactId>
          <version>5.0.2.RELEASE</version>
      </dependency>
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-tx</artifactId>
          <version>5.0.2.RELEASE</version>
      </dependency>
      <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
          <version>5.1.6</version>
      </dependency>
  </dependencies>
  ```

- JdbcTemplate的基本使用：

  ```java
  public class demo1 {
      public static void main(String[] args) {
  
          //Spring的内置数据源
          DriverManagerDataSource ds = new DriverManagerDataSource();
          ds.setDriverClassName("com.mysql.jdbc.Driver");
          ds.setUrl("jdbc:mysql://localhost:3306/spring");
          ds.setUsername("root");
          ds.setPassword("root");
          //设置数据源
          JdbcTemplate jt = new JdbcTemplate();
          jt.setDataSource(ds);
          jt.execute("insert into account(name,money) values('ccc', 1000)");
      }
  }
  ```

- 使用IOC注入：

  - bean.xml：

    ```xml
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
        <property name="url" value="jdbc:mysql://localhost:3306/spring"></property>
        <property name="username" value="root"></property>
        <property name="password" value="root"></property>
    </bean>
    ```

  - demo2.java：

    ```java
    public class demo2 {
        public static void main(String[] args) {
    
            ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
            JdbcTemplate jt = ac.getBean("jdbcTemplate", JdbcTemplate.class);
            jt.execute("insert into account(name,money) values('ddd', 1200)");
        }
    }
    ```

- JdbcDaoSupport的使用：

  - 简化创建JdbcTemplate，可以让Dao实现类继承JdbcDaoSupport。
  - 在实现类中使用`super.getJdbcTemplate()`获取父类对象。



## 2、事务控制

> Spring的事务控制也可以用配置实现。

- Spring提供了接口`PlatformTransactionManager`进行事务控制：

  - 实现类`DataSourceTransactionManager`。

- Spring中基于XML的声明式事务控制：

  - 导入约束：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:aop="http://www.springframework.org/schema/aop"
        xmlns:tx="http://www.springframework.org/schema/tx"
        xsi:schemaLocation="
            http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/tx
            http://www.springframework.org/schema/tx/spring-tx.xsd
            http://www.springframework.org/schema/aop
            http://www.springframework.org/schema/aop/spring-aop.xsd">
    </beans>
    ```

    

  - 配置步骤：

    1. 配置事务管理器：

       ```xml
       <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
           <property name="dataSource" ref="datasource"></property>
       </bean>
       ```

    2. 配置事务通知：

       ```xml
       <tx:advice id="txAdvice" transaction-manager="transactionManager"></tx:advice>
       ```

       - id 属性：事务通知的唯一标志。
       - transaction-manager 属性：给事务通知提供一个事务管理器引用。

    3. 配置AOP中的通用切入点表达式：

       ```xml
       <aop:config>
           <aop:pointcut id="pt" expression="execution(* cn.iwehdio.service.Impl.*.*(..))"></aop:pointcut>
       </aop:config>
       ```

    4. 建立事务通知和切入点表达式的对应关系（`<aop:config>`标签内）：

       ```xml
       <aop:advisor advice-ref="txAdvice" pointcut-ref="pt"></aop:advisor>
       ```

    5. 配置事务的属性（`<tx:advice>`标签内）：

       ```xml
       <tx:attributes>
           <tx:method name="*" propagation="REQUIRED" read-only="false" ></tx:method>
           <tx:method name="find*" propagation="SUPPORTS" read-only="true"></tx:method>
       </tx:attributes>
       ```

       - name 属性：指定Service中的的方法名。
       - isolation 属性：指定事务的隔离级别，默认DEFAULT使用数据库的隔离级别。
       - propagation 属性：指定事务的传播行为，默认REQUIRED一定有事务（增删改）。查询选择SUPPORTS。
       - read-only 属性：指定事务是否只读，只有查询方法可以设置为true，默认false。
       - timeout 属性：指定事务的超时时间，默认-1永不超时。
       - no-rollback-for 属性：指定一个异常，产生该异常时，事务不回滚，产生其他异常时，事务回滚。没有默认值，表示任何异常都回滚。
       - rollback-for 属性：指定一个异常，产生该异常时，事务回滚，产生其他异常时，事务不回滚。没有默认值，表示任何异常都回滚。

- 基于注解的声明式事务控制：

  1. 配置事务管理器：

     ```xml
     <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
         <property name="dataSource" ref="datasource"></property>
     </bean>
     ```

     

  2. 开启Spring对注解事务的支持：

     ```xml
     <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
     ```

  3. 在需要事务支持的地方使用`@Transactional`注解。

  4. 在`@Transactional`注解的属性中进行对事务属性的控制。

- 基于纯注解的声明式事务控制：

  - 开启纯注解事务支持：`@EnableTransactionManagement`。

- 编程式事务控制：

  1. 在配置文件中配置事务管理器。
  2. 在配置文件中配置事务模板对象，注入事务管理器对象。
  3. 在需要进行事务控制的方法内，重写事务模板对象的`TransactionCallback()`下的`doInTransaction()`方法，将事务代码放入其中。
  4. 在bean.xml中再业务层注入事务模板对象。