## 1、JPA

- ORM：对象关系映射。

  - 主要目的：操作实体类就相当于操作数据库表。
  - 需要映射关系：
    - 建立实体类和表的关系。
    - 建立实体类中属性和表中字段的关系。
  - 不需要再重点关注sql语句。

- JPA规范：

  - ORM框架的规范，内部由接口和抽象类组成。
  - 使用一些注解表示映射方式
  - 类比JDBC：
    - MySQL驱动和Oracle驱动都实现了JDBC规范。
    - 实现了JDBC的接口，更换数据库Java代码不需要改变。
  - 优势：标准化、容器级特性的支持、简单方便、查询能力、高级特性。

- 搭建环境的过程：

  - 导入maven坐标。

  - 配置JPA核心配置文件：

    - 需要配置到类路径下的 META-INF 文件夹下。
    - 命名为 persistence.xml。

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <persistence xmlns="http://java.sun.com/xml/ns/persistence" version="2.0">
        <!--持久化单元：
        name持久化单元名称；
        transaction-type：事务管理的方式。RESOURCE_LOCAL本地事务管理；JTA：分布式事务管理-->
        <persistence-unit name="myJpa" transaction-type="RESOURCE_LOCAL">
    
            <!--  jpa的实现方式  -->
            <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider>
            <properties>
                <!-- 数据库信息-->
                <property name="javax.persistence.jdbc.user" value="root"/>
                <property name="javax.persistence.jdbc.password" value="root"/>
                <property name="javax.persistence.jdbc.driver" value="com.mysql.jdbc.Driver"/>
                <property name="javax.persistence.jdbc.url" value="jdbc:mysql:///jpa"/>
                <!-- 可选配置：配置JPA实现方式(Hibernate)的配置 -->
                <!-- 显示sql(true,flase);自动创建数据库表(create,update,none) -->
                <property name="hibernate.show_sql" value="true"/>
                <property name="hibernate.hbm2ddl.auto" value="update"/>
            </properties>
        </persistence-unit>
    
    </persistence>
    ```

  - 编写客户的实体类。

  - 用JPA注解配置实体类和表、类中属性和表中字段的映射。

    - 实体类和表的映射：
      - 实体类上声明`@Entity`该类是一个实体类。
      - 映射关系`@Table(name="数据库表名")`。
    - 类中属性和表中字段的映射：
      - 主键对应的属性上加`@Id`。
      - 主键如果是自增的，还需要加`@GeneratedValue(strategy = GenerationType.IDENTITY)`。
      - 指定数据库表中的字段`@Column(name = "表中字段")`。
      - 普通属性只需要加`@Column`。

    ```java
    @Entity
    @Table(name = "cst_customer")
    public class Customer {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        @Column(name = "cust_id")
        private Long custId;
        @Column(name = "cust_name")
        private String custName;
        @Column(name = "cust_source")
        private String custSource;
        @Column(name = "cust_level")
        private String custLevel;
        @Column(name = "cust_industry")
        private String custIndustry;
        @Column(name = "cust_phone")
        private String custPhone;
        @Column(name = "cust_address")
        private String custAddress;
    }
    ```

    

  - JPA的操作步骤（保存一个客户到数据库）：

    - 加载配置文件，创建实体管理器工厂对象。
    - 通过工厂获取实体管理器。
    - 获取事务对象，开启事务。
    - 完成增删改查操作。
    - 提交/回滚事务。释放资源。

    ```java
    @Test
    public void testSave() {
    	EntityManagerFactory factory = Persistence.createEntityManagerFactory("myJpa");
        EntityManager entityManager = factory.createEntityManager();
        EntityTransaction transaction = entityManager.getTransaction();
        transaction.begin();
        Customer customer = new Customer();
        customer.setCustName("iwehdio");
        customer.setCustIndustry("java");
        entityManager.persist(customer);
        transaction.commit();
        entityManager.close();
        factory.close();
    }
    ```
    
  - `entityManager.persist()`方法：保存对象，传入一个实体类对象。
  
- API对象：

  - Persistence的静态方法createEntityManagerFactory：根据持久化单元名称创建实体管理器工厂EntityManagerFactory。
  - 实体管理器工厂EntityManagerFactory：创建实体管理器EntityManager。
    - EntityManagerFactory内部维护了数据库信息、缓存信息、实体管理器对象等。
    - 创建过程比较浪费资源。
    - 是线程安全的访问对象。
    - 可以创建一个公共的实体管理器工程，借助静态代码块的形式。
  - EntityManager实体类管理器对象：getTransaction创建事务对象EntityTransaction。
    - EntityManager实体类管理器对象是真正与数据库进行操作的对象。
    - presist保存、merge更新、remove删除、find/getReference根据id查询。
  - EntityTransaction事务对象：开启、提交、回滚。

- 主键生成策略：

  - 使用注解`@GenratedValue`。
  - 自增：`GenerationType.INDENTITY`，前提是底层数据库支持自动增长方式，对id自增（MySQL）。
  - 序列：`GenerationType.SEQUENCE`，前提是底层数据库支持序列（Oracle）。
  - JPA提供的机制，通过一张数据库表的形式完成主键自增：`GenerationType.TABLE`。
  - 程序自动选择主键生成测量：`GenerationType.AUTO`。

- 抽取JpaUtils：

  ```java
  public class JapUtils {
      private static EntityManagerFactory factory;
      static {
          //加载配置文件，创建实体管理器工厂
          factory = Persistence.createEntityManagerFactory("myJpa");
      }
      //获取实体管理器工厂的方法
      public static EntityManager getEntityManager() {
          return factory.createEntityManager();
      }
  }
  ```

  - 第一次访问，静态代码块创建工厂对象，在调用方法创建管理器对象。

- 根据id查询客户：

  - `entityManager.find()`方法：第一个参数是查询数据的结果需要封装的实体类对象的字节码，第二个的主键的取值。

    ```java
    @Test
    public void testfind() {
        EntityManager entityManager = JpaUtils.getEntityManager();
        EntityTransaction transaction = entityManager.getTransaction();
        transaction.begin();
        Customer customer = entityManager.find(Customer.class, 1L);
        System.out.println(customer);
        transaction.commit();
        entityManager.close();
    }
    ```

  - `entityManager.getReference()`：与find类似，也是查询方法，参数相同。
  - find方法查询的对象就是当前客户对象本身，在调用find方法时就发送SQL语句查询，也就是立即加载。
  - 而getReference方法查询的对象是客户对象的动态代理对象，调用getReference方法不会立即发送SQL语句查询。在调用查询对象时，才发送SQL语句，也就是延迟加载。

- 删除客户：

  - `entityManager.remove()`方法：传入的参数是一个对象。
  - 需要先根据id查询，再将查到的对象传入remove方法。

- 更新客户：

  - `entityManager.merge()`方法：传入的参数是一个对象。
  - 需要先根据id查询，再将查到的对象更新数据后传入merge方法。



- jpql查询：

  - JPA查询语句，查询的是实体类和类中的属性，与SQL语句语法相似。

  - 查询步骤：

    - 根据jpql语句创建Query对象。
    - 对参数赋值。
    - 发送查询，封装结果。

  - 查询全部：

    - `jpql="from Customer"`语句：查询所有。支持简写也支持全类名。
    - `entityManager.createQuery(jpql)`方法：创建Query查询对象，是执行jpql的对象。
    - `query.getResultList()`方法：发送查询，并封装结果集为List。

    ```java
    String jpql = "from Customer";
    Query query = entityManager.createQuery(jpql);
    List resultList = query.getResultList();
    for(Object object :resultList) {
        System.out.println(object);
    }
    ```

  - 根据id倒序查询：

    - `jpql = "from Customer order by custId desc "`语句：倒序查询所有。

  - 统计查询：

    - `jpql = "select count(custId) from Customer"`语句：统计客户总数。
    - `query.getSingleResult()`方法：发送查询，获取单个结果。

  - 分页查询：

    - jpql语句还是查询所有。
    - `query.setFirstResult()`方法：分页查询的起始页码。
    - `query.setMaxResults()`方法：分页查询的每页条数。

  - 条件查询：

    - `jpql = "from Customer where custName like ?"`语句：对名字进行模糊查询。
    - `query.setParameter()`方法：设置占位符，第一个是占位符索引从1开始，第二个是占位符的值。



## 2、SpringData JPA

- Spring基于ORM框架、JPA规范基础上封装的JPA应用框架，摆脱了DAO层的操作。

- 环境搭建：

  - maven坐标：

    ```xml
    <properties>
        <spring.version>4.2.4.RELEASE</spring.version>
        <hibernate.version>5.0.7.Final</hibernate.version>
        <slf4j.version>1.6.6</slf4j.version>
        <log4j.version>1.2.12</log4j.version>
        <c3p0.version>0.9.1.2</c3p0.version>
        <mysql.version>5.1.6</mysql.version>
    </properties>
    
    <dependencies>
        <!-- junit单元测试 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.9</version>
            <scope>test</scope>
        </dependency>
    
        <!-- spring beg -->
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
    
        <!-- spring end -->
    
        <!-- hibernate beg -->
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-core</artifactId>
            <version>${hibernate.version}</version>
        </dependency>
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-entitymanager</artifactId>
            <version>${hibernate.version}</version>
        </dependency>
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-validator</artifactId>
            <version>5.2.1.Final</version>
        </dependency>
        <!-- hibernate end -->
    
        <!-- c3p0 beg -->
        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>${c3p0.version}</version>
        </dependency>
        <!-- c3p0 end -->
    
        <!-- log end -->
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
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql.version}</version>
        </dependency>
    
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-jpa</artifactId>
            <version>1.9.0.RELEASE</version>
        </dependency>
    
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
        </dependency>
    
        <!-- el beg 使用spring data jpa 必须引入 -->
        <dependency>  
            <groupId>javax.el</groupId>  
            <artifactId>javax.el-api</artifactId>  
            <version>2.2.4</version>  
        </dependency>  
    
        <dependency>  
            <groupId>org.glassfish.web</groupId>  
            <artifactId>javax.el</artifactId>  
            <version>2.2.4</version>  
        </dependency> 
        <!-- el end -->
    </dependencies>
    ```

    

  - 配置Spring配置文件：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans> 
        <!-- 1.dataSource 配置数据库连接池-->
        <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
            <property name="driverClass" value="com.mysql.jdbc.Driver" />
            <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/jpa" />
            <property name="user" value="root" />
            <property name="password" value="root" />
        </bean>
    
        <!-- 2.配置entityManagerFactory -->
        <bean id="entityManagerFactory" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
            <property name="dataSource" ref="dataSource" />
            <!-- 配置扫描的实体类所在的包-->
            <property name="packagesToScan" value="cn.iwehdio.domain" />
            <!-- jpa的实现厂家-->
            <property name="persistenceProvider">
                <bean class="org.hibernate.jpa.HibernatePersistenceProvider" />
            </property>
            <!--JPA的供应商适配器-->
            <property name="jpaVendorAdapter">
                <bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter">
                    <!--配置是否自动创建数据库表-->
                    <property name="generateDdl" value="false" />
                    <!--数据库类型-->
                    <property name="database" value="MYSQL" />
                    <!-- 数据库方言（特有语法） -->
                    <property name="databasePlatform" value="org.hibernate.dialect.MySQLDialect" />
                    <!-- 是否显示SQL语句 -->
                    <property name="showSql" value="true" />
                </bean>
            </property>
            <!-- jpa方言 -->
            <property name="jpaDialect">
                <bean class="org.springframework.orm.jpa.vendor.HibernateJpaDialect" />
            </property>
        </bean>
    
    
        <!-- 3.事务管理器-->
        <!-- JPA事务管理器，配置实体管理器工厂  -->
        <bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager">
            <property name="entityManagerFactory" ref="entityManagerFactory" />
        </bean>
    
        <!-- 整合spring data jpa，配置dao接口包、事务和实体管理器工厂-->
        <jpa:repositories base-package="cn.iwehdio.dao"
                          transaction-manager-ref="transactionManager"
                          entity-manager-factory-ref="entityManagerFactory"></jpa:repositories>
    
        <!-- 4.txAdvice-->
        <tx:advice id="txAdvice" transaction-manager="transactionManager">
            <tx:attributes>
                <tx:method name="save*" propagation="REQUIRED"/>
                <tx:method name="insert*" propagation="REQUIRED"/>
                <tx:method name="update*" propagation="REQUIRED"/>
                <tx:method name="delete*" propagation="REQUIRED"/>
                <tx:method name="get*" read-only="true"/>
                <tx:method name="find*" read-only="true"/>
                <tx:method name="*" propagation="REQUIRED"/>
            </tx:attributes>
        </tx:advice>
    
        <!-- 5.aop-->
        <aop:config>
            <aop:pointcut id="pointcut" expression="execution(* cn.iwehdio.service.*.*(..))" />
            <aop:advisor advice-ref="txAdvice" pointcut-ref="pointcut" />
        </aop:config>
        <!-- 配置包扫描注解   -->
        <context:component-scan base-package="cn.iwehdio"></context:component-scan>
    
    </beans>
    ```

    

  - 编写实体类，使用jpa注解配置映射关系。

- 编写dao接口：

  - 符合SpringData JPA的接口规范：

    - 继承接口`JpaRepository`和`JpaSpecificationExecutor`。
    - 需要提供相应的泛型。
      - 接口`JpaRepository`：
        - 第一个泛型：操作的实体类类型。
        - 第二个泛型：实体类中主键属性的类型。
        - 封装了基本CRUD操作。
      - 接口`JpaSpecificationExecutor`：
        - 泛型：操作的实体类类型。
        - 封装了复杂查询。

    ```java
    public interface CustomerDao extends JpaRepository<Customer,Long>, JpaSpecificationExecutor<Customer> {
    }
    ```

  - 测试：

    - 测试环境：根据主键查询一个。

      ```java
      @RunWith(SpringJUnit4ClassRunner.class)
      @ContextConfiguration(locations = "classpath:applicationContext.xml")
      public class DaoTest {
      
          @Autowired
          private CustomerDao customerDao;
      
          @Test
          public void test1() {
              Customer one = customerDao.findOne(2L);
              System.out.println(one);
          }
      }
      ```

    - `findOne()`方法：根据主键查询一个。

    - `save()`方法：保存或更新。如果传入的对象存在主键，则为更新；没有主键，则为保存。

    - `delete()`方法：根据主键删除。

    - `findAll()`方法：查询所有。

- SpringData JPA运行过程：

  - 真正发挥作用的接口的实现类，在程序运行过程中，自动帮助我们动态生成了接口的实现类对象。
  - 通过动态代理，可以生成基于接口的实现类对象。
  - 最终是调用JPA的实体管理器进行操作。
  - 运行流程：
    - 通过JDKDynamicAopProxy的invoke方法创建动态代理对象SimpleJpaRepository。
    - SimpleJpaRepository实现了之前dao中继承的两个接口JpaRepository和JpaSpecificationExecutor。
    - 通过entityManager完成操作。
    - JPA规范调用hibernate封装的jdbc操作数据库。
  - 配置文件中`<jpa:repositories base-package=“”>` 指定对此包下的dao接口进行动态代理增强。

- 查询操作：

  - 借助接口中定义好的方法：

    -  `count()`方法：查询总数量。
    - `exists()`方法：传入主键，判断是否存在此条记录。是通过查询count实现的。
    - `getOne()`方法：根据主键从数据库查询。
      - 单元测试需要加上事务支持`@Transactional`。
      - 是延迟加载。

  - 支持jpql的查询方式：

    - 需要将jpql语句使用注解`@Query`的形式，配置到接口方法上。

    - 按名称查询：

      ```java
      @Query(value = "from Customer where custName = ?")
      public Customer findByName(String custName);
      ```

    - 按名称的主键id查询：

      ```java
      @Query(value = "from Customer where custName=? and custId=?")
      public Customer findByNameAndId(String name, Long id);
      ```

    - 多个参数传入，默认情况下jpql语句中的占位符赋值顺序与方法的参数列表一致。

    - 也可以指定顺序，在jpql语句中使用`?索引`。索引就是方法参数列表中的顺序，从1开始。

  - 支持jpql的更新方式：

    - 使用注解`@Modifying`表示当前是一个更新方法。

    - 进行更新或删除操作，需要事务支持。但是默认会回滚事务，需要加上`@Rollback(value=false)`。

    - 按主键id更新名称：

      ```java
      @Query(value = "update Customer set custName=?2 where custId=?1")
      @Modifying
      public void updateName(Long id, String name);
      ```

  - 支持sql的查询方式：

    - 使用注解`@Query`，使用属性nativeQuery指定哪种语句。true为本地查询表示使用sql，false（默认）表示使用jpql。

    - 查询所有：

      ```java
      @Query(value = "select * from cst_customer", nativeQuery = true)
      public List<Customer> findAllAsSQL();
      ```

    - 多个参数的赋值方式与jpql相同。

  - 方法名称规则查询：

    - 是对jpql更加深入的一层封装，只需要按照SpringData JPA提供的方法名称规则定义方法，不需要配置查询语句即可完成查询。

    - 方法命名规则：

      - `findBy`开头的表示查询：

        - 之后为对象中的属性名（首字母大写），表示查询的条件。

        - 如`findByCustName`表示根据客户名称查询。

          ```java
          public Customer findByCustName(String name);
          ```

        - 在之后可再加查询方式，比如Like。

          ```java
          public List<Customer> findByCustNameLike(String name);
          ```

        - 多条件查询`findBy`+属性名+查询方式+条件连接符（Or或者And）+其他属性查询。



## 3、复杂操作

- specificaions 动态查询：

  - 通过JpaSpecificationExcutor接口中的方法进行查询。

  - 所有方法都需要传入参数`Specification<T> spec`作为查询条件。

    - 是一个用于描述条件的接口。提供泛型，就是查询的对象类型。
    - 在查询时需要自定义实现类，实现`toPredicate()`方法封装查询条件。包括三个参数：
      - root：查询的根对象，查询的任何属性都可以从根对象中获取。
      - CriteriaQuery：顶层查询对象，一般不用。
      - CriteriaBuilder：查询的构造器，内部封装了许多查询条件。

  - 查询条件：

    - 查询方式：在CriteriaBuilder中。
    - 比较的属性名称：在root中。
    - `root.get("属性名")`获取比较的属性。

  - 动态查询：

    - 根据客户名称查询：

      - `criteriaBuilder.equal()`方法：第一个参数需要比较的属性，第二个参数属性的取值。

        ```java
        @Test
        public void testfindOne() {
            //匿名内部类
            Specification<Customer> spec = new Specification<Customer>() {
                @Override
                public Predicate toPredicate(Root<Customer> root, CriteriaQuery<?> criteriaQuery, CriteriaBuilder criteriaBuilder) {
                    //1、获取比较的属性（使用实体类中的属性名）
                    Path<Object> custName = root.get("custName");
                    //2、构造查询条件
                    Predicate predicate = criteriaBuilder.equal(custName, "iwehdio");
                    return predicate;
                }
            };
            Customer customer = customerDao.findOne(spec);
            System.out.println(customer);
        }
        ```

    - 多条件拼接：根据名称和行业查询。

      - 分别构造名称和行业的查询，然后将两个查询联系起来。

      - 将多个查询条件进行组合：`criteriaBuilder.and()`方法或`criteriaBuilder.or()`方法，传入多个查询条件。

        ```java
        @Test
        public void testmulti() {
            Specification<Customer> spec = new Specification<Customer>() {
                @Override
                public Predicate toPredicate(Root<Customer> root, CriteriaQuery<?> criteriaQuery, CriteriaBuilder criteriaBuilder) {
                    Path<Object> custName = root.get("custName");
                    Path<Object> custIndustry = root.get("custIndustry");
                    Predicate predicate1 = criteriaBuilder.equal(custName, "iwehdio");
                    Predicate predicate2 = criteriaBuilder.equal(custIndustry, "a");
                    Predicate and = criteriaBuilder.and(predicate1, predicate2);
                    return and;
                }
            };
            Customer customer = customerDao.findOne(spec);
            System.out.println(customer);
        }
        ```

    - 模糊查询：

      - `criteriaBuilder.like()`方法：模糊查询。除了like方法以外，都还需要使用`path.as(.class)`传入比较的参数类型。

        ```java
        @Test
        public void testlike() {
            Specification<Customer> spec = new Specification<Customer>() {
                @Override
                public Predicate toPredicate(Root<Customer> root, CriteriaQuery<?> criteriaQuery, CriteriaBuilder criteriaBuilder) {
                    Path<Object> custName = root.get("custName");
                    Predicate predicate = criteriaBuilder.like(custName.as(String.class), "iwe%");
                    return predicate;
                }
            };
            List<Customer> all = customerDao.findAll(spec);
            System.out.println(all);
        }
        ```

    - 指定数据顺序：

      - 添加排序`Sort`对象。

      - 创建排序对象`Sort`，构造方法传入参数。第一个参数传入升序还是降序，第二个参数是排序的属性名称。

        ```java
        Sort sort = new Sort(Sort.Direction.DESC, "custId");
        List<Customer> all = customerDao.findAll(spec, sort);
        ```

    - 分页查询：

      - 添加分页参数`Pageable`对象，返回`Page`分页对象。

      - 创建分页参数对象`PageRequest`，构造方法传入参数。第一个参数当前查询的页数（从0开始），第二个参数每页查询的数量。

        ```java
        @Test
        public void testpage() {
            Specification<Customer> spec = null;
            Pageable pageable = new PageRequest(0, 2);
            Page<Customer> all = customerDao.findAll(spec, pageable);
            System.out.println(all.getContent());
        
        }
        ```

      - `.getContent()`方法：得到数据列表。



- 多表关系：

  - 可以通过实体类中的包含关系，描述表关系。

  - 步骤：

    - 通过外键和中间表确定表关系，
    - 编写实体类，在实体类中用包含关系描述表关系。
    - 配置映射关系。

  - 一对多操作：

    - 客户到联系人的一对多关系：

      - 声明关系：`@OneToMany`注解，targetEntity 指定对方对象的字节码。

      - 配置外键：`@JoinColumn`注解，name指定外键字段名称，referencedColumnName指定参照的主表的主键字段名称。

        ```java
        @OneToMany(targetEntity = LinkMan.class)
        @JoinColumn(name = "lkm_cust_id",referencedColumnName = "cust_id")
        private Set<LinkMan> linkMans = new HashSet<>();
        ```

    - 联系人到客户的多对一关系：

      ```java
      @ManyToOne(targetEntity = Customer.class)
      @JoinColumn(name = "lkm_cust_id", referencedColumnName = "cust_id")
      private Customer customer;
      ```

      - 配置联系人的实体类和Dao接口。

    - 在一的一方放弃外键维护：

      ```java
      @OneToMany(mappedBy = "customer")
      private Set<LinkMan> linkMans = new HashSet<>();
      ```

      - 指定外键中，对方配置关系的属性名称。

    - 删除操作：

      - 删除从表数据，可以随时任意删除。
      - 删除主表数据：
        - 有从表数据的话：
          - 默认情况下，将外键置为null，删除主表数据。
          - 如果一的一方放弃了外键维护，则不能删除。
          - 如果还要删除，需要使用级联删除引用。
        - 没有从表数据引用，随便删除。
      - 级联操作：
        - 需要区分操作主体。
        - 需要在操作主体的实体类上，添加级联属性，使用`@OneToMany(cascade=)`配置级联。
        - 可选值包括：ALL所有，MERGE更新，PERSIST保存，REMOVE删除。

  - 多对多操作：

    - 用户与角色之间的多对多关系：

      - 声明关系：使用`@ManyToMany`注解，targetEntity 指定对方对象的字节码。

      - 配置中间表：使用`@JoinTable`注解，name指定中间表名称，joinColumns指定当前对象在中间表中的外键，inverseJoinColumns指定对方对象在中间表的外键。

        ```java
        @ManyToMany(targetEntity = Role.class)
        @JoinTable(name = "sys_user_role",
                   joinColumns = {@JoinColumn(name = "sys_user_id",referencedColumnName = "user_id")},
                   inverseJoinColumns = {@JoinColumn(name = "sys_role_id",referencedColumnName = "role_id")})
        private Set<Role> role = new HashSet<>();
        ```

    - 多对多放弃维护权：被动的一方放弃维护权。设置同一对多。

    - 多对多级联操作：设置同一对多。

- 对象导航查询：
  - 通过查询一个对象，查询其所有的关联对象。
  - 通过的方法是调用这个对象的`get()`方法来查询。
  - 从一方查询多方默认使用延迟加载。调用`get()`方法并不会立即进行查询。
  - 关闭延迟加载：修改配置为立即加载，配置到多表映射的注解中，即`@OneToMany`等注解中的fetch属性。EAGER表示立即加载，LAZY表示延迟加载。
  - 从多方查询一方默认使用立即加载。

