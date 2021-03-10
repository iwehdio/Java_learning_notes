## 1、Docker

> 一个开源的应用容器引擎。

- 支持将软件编译成一个镜像，然后再镜像中各种软件做好配置，将镜像发布出去。其他使用者可以直接使用这个镜像。
- 运行中的这个镜像被称为容器。
- Docker核心概念：
  - docker主机：安装了Docker程序的机器。
  - docker客户端：连接docker主机进行操作。
  - docker仓库：用来保存各种打包好的软件镜像。分为公共仓库和私人仓库等。
  - docker镜像：配置好的软件打包好的镜像，放在docker仓库中。
  - docker容器：镜像启动后的实例被成为一个容器。
- 使用docker的步骤：
  - 安装docker。
  - 去docker仓库找到这个软件对应的镜像，
  - 使用docker运行这个镜像，生成一个docker容器。
  - 容器的运行与停止就是软件的启动停止。
- 使用Docker：
  - 安装Docker：`yum install docker`。
  - 启动Docker：`systemctl start docker`。
  - docker开机启动：`systemctl enable docker`。
  - 停止docker：`systemctl stop docker`。
- Docker常用操作：
  - 镜像操作：
    - 检索：`docker search 镜像名`。
    - 拉取：`docker pull 镜像名:标签名`。不加标签名默认最新。
    - 查看所有镜像：`docker images`。
    - 删除：`docker rmi 镜像id`。
  - 容器操作：
    - 软件镜像 > 运行镜像 > 产生容器（正在运行的软件）。
    - 根据镜像启动容器：`docker run --name 容器名 -d 镜像名:标签名`。--name后写自定义容器名，-d表示后台运行。
      - 端口映射：`-p 主机端口:容器端口`。
      - 外部连接容器需要进行端口映射。
    - 查看运行的容器：`docker ps`。-a表示查看所有容器。
    - 停止运行中的容器：`docker stop 容器名或id`。
    - 删除容器：`docker rm 容器名或id`。
    - 启动容器：`docker start 容器名或id`。
    - 容器日志：`docker logs 容器名或id`。
- Docker启动MYSQL：
  - 启动时指定密码：`-e MYSQL_ROOT_PASSWORD=`。
- 使用阿里云服务器，需要在安全组中开放端口8080（tomcat）、3306（mysql）
- 访问阿里云上Docker部署的Tomcat：https://blog.csdn.net/abcde123_123/article/details/103879385。



## 2、数据访问

- 底层使用SpringData访问关系型数据库和非关系型数据库。

- 配置连接的数据库：
  - spring.datasource下的username、password、url和driver-class-name。
  - 自动注入Datasource，默认使用tomcat的jdbc连接池。
  - 自动配置了JdbcTemplate操作数据库。
  - SQLyog远程连接阿里云：https://blog.csdn.net/weixin_39520967/article/details/103849185。
  
- SpringBoot2.0配置Druid：

  - 配置文件：

    ```yaml
    spring:
      datasource:
        type: com.alibaba.druid.pool.DruidDataSource
        driverClassName: com.mysql.cj.jdbc.Driver
        platform: mysql
        url: jdbc:mysql://IP地址:3306/springboot_jdbc
        username: root
        password: 123456
        initialSize: 5
        minIdle: 5
        maxActive: 20
        maxWait: 60000
        timeBetweenEvictionRunsMillis: 60000
        minEvictableIdleTimeMillis: 300000
        validationQuery: SELECT1FROMDUAL
        testWhileIdle: true
        testOnBorrow: false
        testOnReturn: false
    ```

  - 实现监控：

    ```java
    @Configuration
    public class DruidConfig {
    
        @Bean
        public ServletRegistrationBean druidServlet() {// 主要实现web监控的配置处理
            ServletRegistrationBean servletRegistrationBean = new ServletRegistrationBean(
                    new StatViewServlet(), "/druid/*");//表示进行druid监控的配置处理操作
            servletRegistrationBean.addInitParameter("loginUsername", "root");//用户名
            servletRegistrationBean.addInitParameter("loginPassword", "root");//密码
            servletRegistrationBean.addInitParameter("resetEnable", "false");//是否可以重置数据源
            return servletRegistrationBean;
        }
    
        @Bean    //监控
        public FilterRegistrationBean filterRegistrationBean() {
            FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
            filterRegistrationBean.setFilter(new WebStatFilter());
            filterRegistrationBean.addUrlPatterns("/*");//所有请求进行监控处理
            filterRegistrationBean.addInitParameter("exclusions", "/static/*,*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*");//排除
            return filterRegistrationBean;
        }
    
        @Bean
        @ConfigurationProperties(prefix = "spring.datasource")
        public DataSource druidDataSource() {
            return new DruidDataSource();
        }
    
    }
    ```

- 整合Mybatis（注解）：

  - 编写操作数据库的mapper：

    ```java
    @Mapper
    public interface DepartmentMapper {
        @Select("select * from department where id=#{id}")
        public Department getDeptById(Integer id);
        @Delete("delete from department where id=#{id}")
        public int deleteDeptById(Integer id);
        @Options(useGeneratedKeys = true, keyProperty = "id")
        @Insert("insert into department(departmentName) value(#{departmentName})")
        public int insertDept(Department department);
        @Update("update department set departmentName=#{departmentName} where id=#{id}")
        public int updateDept(Department department);
    }
    ```

  - 编写控制器：

    ```java
    @RestController
    public class DeptController {
        @Autowired
        DepartmentMapper departmentMapper;
        @GetMapping("/dept/{id}")
        public Department getDept(@PathVariable("id") Integer id) {
            return departmentMapper.getDeptById(id);
        }
        @GetMapping("/dept")
        public Department insertDept(Department department) {
            departmentMapper.insertDept(department);
            return department;
        }
    }
    ```
    
  - 或者在主程序中使用`@MapperScan`指定批量扫描。

- 整合Mybatis（配置文件）：

  - 在Springboot配置文件中指定配置文件的位置。
  - mybatis.config-locations指定全局配置文件的位置。
  - mybatis.mapper-locations指定映射配置文件的位置。



- 整合JPA：
  
  - application.yaml配置文件：
  
    ```yaml
    spring:
      datasource:
        url: jdbc:mysql:///jpa
        username: root
        password: root
        driver-class-name: com.mysql.jdbc.Driver
      jpa:
        hibernate:
          ddl-auto: update
        show-sql: true
    ```
  
  - 实体类：
  
    ```java
    @Entity
    @Table(name = "tbl_user")
    public class User {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        @Column
        private Integer id;
        @Column
        private String lastName;
        @Column
        private String email;
    }
    ```
  
  - UserDao接口：
  
    ```java
    public interface UserDao extends JpaRepository<User, Integer> {
    }
    ```
  
  - 控制器：
  
    ```java
    @RestController
    public class UserController {
    
        @Autowired
        UserDao userDao;
        
        @GetMapping("/user/{id}")
        public User getUser(@PathVariable("id") Integer id) {
            User one = userDao.findOne(id);
            return one;
        }
    
        @GetMapping("/user")
        public User insertUser(User user) {
            User save = userDao.save(user);
            return save;
        }
    }
    ```



## 3、Springboot原理

- 启动运行流程：

  - 创建SpringApplication对象。

    ```java
    initialize(sources);
    private void initialize(Object[] sources) {
        //保存主配置类
        if (sources != null && sources.length > 0) {
            this.sources.addAll(Arrays.asList(sources));
        }
        //判断当前是否一个web应用
        this.webEnvironment = deduceWebEnvironment();
        //从类路径下找到META-INF/spring.factories配置的所有ApplicationContextInitializer；然后保存起来
        setInitializers((Collection) getSpringFactoriesInstances(
            ApplicationContextInitializer.class));
        //从类路径下找到META-INF/spring.factories配置的所有ApplicationListener
        setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
        //从多个配置类中找到有main方法的主配置类
        this.mainApplicationClass = deduceMainApplicationClass();
    }
    ```

  - 运行run()方法。

    ```java
    public ConfigurableApplicationContext run(String... args) {
       StopWatch stopWatch = new StopWatch();
       stopWatch.start();
       ConfigurableApplicationContext context = null;
       FailureAnalyzers analyzers = null;
       configureHeadlessProperty();
        
       //获取SpringApplicationRunListeners；从类路径下META-INF/spring.factories
       SpringApplicationRunListeners listeners = getRunListeners(args);
        //回调所有的获取SpringApplicationRunListener.starting()方法
       listeners.starting();
       try {
           //封装命令行参数
          ApplicationArguments applicationArguments = new DefaultApplicationArguments(
                args);
          //准备环境
          //创建环境完成后回调SpringApplicationRunListener.environmentPrepared()；表示环境准备完成
          ConfigurableEnvironment environment = prepareEnvironment(listeners,
                applicationArguments);
           		
          Banner printedBanner = printBanner(environment);
           //创建ApplicationContext；决定创建web的ioc还是普通的ioc
          context = createApplicationContext();
           
          analyzers = new FailureAnalyzers(context);
           //准备上下文环境;将environment保存到ioc中；而且applyInitializers()；
           //applyInitializers()：回调之前保存的所有的ApplicationContextInitializer的initialize方法
           //回调所有的SpringApplicationRunListener的contextPrepared()；
          prepareContext(context, environment, listeners, applicationArguments,
                printedBanner);
           //prepareContext运行完成以后回调所有的SpringApplicationRunListener的contextLoaded（）；
           
           //刷新容器；ioc容器初始化（如果是web应用还会创建嵌入式的Tomcat）
           //扫描，创建，加载所有组件的地方；（配置类，组件，自动配置）
          refreshContext(context);
           //从ioc容器中获取所有的ApplicationRunner和CommandLineRunner进行回调
           //ApplicationRunner先回调，CommandLineRunner再回调
          afterRefresh(context, applicationArguments);
           //所有的SpringApplicationRunListener回调finished方法
          listeners.finished(context, null);
          stopWatch.stop();
          if (this.logStartupInfo) {
             new StartupInfoLogger(this.mainApplicationClass)
                   .logStarted(getApplicationLog(), stopWatch);
          }
           //整个SpringBoot应用启动完成以后返回启动的ioc容器；
          return context;
       }
       catch (Throwable ex) {
          handleRunFailure(context, listeners, analyzers, ex);
          throw new IllegalStateException(ex);
       }
    }
    ```

  

- 几个重要的事件回调机制:

  - 配置在META-INF/spring.factories:

    - ApplicationContextInitializer

      ```java
      public class HelloApplicationContextInitializer implements ApplicationContextInitializer<ConfigurableApplicationContext> {
          @Override
          public void initialize(ConfigurableApplicationContext applicationContext) {
              System.out.println("ApplicationContextInitializer...initialize..."+applicationContext);
          }
      }
      ```

    - SpringApplicationRunListener

      ```java
      public class HelloSpringApplicationRunListener implements SpringApplicationRunListener {
          //必须有的构造器
          public HelloSpringApplicationRunListener(SpringApplication application, String[] args){
          }
          @Override
          public void starting() {
              System.out.println("SpringApplicationRunListener...starting...");
          }
          @Override
          public void environmentPrepared(ConfigurableEnvironment environment) {
              Object o = environment.getSystemProperties().get("os.name");
              System.out.println("SpringApplicationRunListener...environmentPrepared.."+o);
          }
          @Override
          public void contextPrepared(ConfigurableApplicationContext context) {
              System.out.println("SpringApplicationRunListener...contextPrepared...");
          }
          @Override
          public void contextLoaded(ConfigurableApplicationContext context) {
              System.out.println("SpringApplicationRunListener...contextLoaded...");
          }
          @Override
          public void finished(ConfigurableApplicationContext context, Throwable exception) {
              System.out.println("SpringApplicationRunListener...finished...");
          }
      }
      ```

    - 配置文件META-INF/spring.factories：

      ```properties
      org.springframework.context.ApplicationContextInitializer=\
      com.atguigu.springboot.listener.HelloApplicationContextInitializer
      
      org.springframework.boot.SpringApplicationRunListener=\
      com.atguigu.springboot.listener.HelloSpringApplicationRunListener
      ```

      

  - 只需要放在ioc容器中:

    - ApplicationRunner

      ```java
      @Component
      public class HelloApplicationRunner implements ApplicationRunner {
          @Override
          public void run(ApplicationArguments args) throws Exception {
              System.out.println("ApplicationRunner...run....");
          }
      }
      ```

      

    - CommandLineRunner

      ```java
      @Component
      public class HelloCommandLineRunner implements CommandLineRunner {
          @Override
          public void run(String... args) throws Exception {
              System.out.println("CommandLineRunner...run..."+ Arrays.asList(args));
          }
      }
      ```

      



- 自定义starters：

  - 如何编写自动配置：

    ```java
    @Configuration  //指定这个类是一个配置类
    @ConditionalOnXXX  //在指定XXX条件成立的情况下自动配置类生效
    @AutoConfigureAfter  //指定自动配置类的顺序
    @Bean  //给容器中添加组件
    
    @ConfigurationPropertie结合相关xxxProperties类来绑定相关的配置
    @EnableConfigurationProperties //让xxxProperties生效加入到容器中
    
    自动配置类要能加载
    将需要启动就加载的自动配置类，配置在META-INF/spring.factories
    ```

  - 模式：

    - 启动器starter只用来做依赖导入，专门写一个自动配置模块。
    - 启动器模块需要引入自动配置模块。自动配置模块都需要引入spring-boot-starter-parent。
    - 命名：启动器名-spring-boot-starter。

  - 自定义步骤：

    - 创建HelloProperties，绑定主配置文件中的atguigu.hello属性：

      ```java
      @ConfigurationProperties(prefix = "atguigu.hello")
      public class HelloProperties {
          private String prefix;
          private String suffix;
      
          public String getPrefix() {
              return prefix;
          }
          public void setPrefix(String prefix) {
              this.prefix = prefix;
          }
          public String getSuffix() {
              return suffix;
          }
          public void setSuffix(String suffix) {
              this.suffix = suffix;
          }
      }
      ```

    - 创建HelloService组件，创建sayHellAtguigu打招呼方法：

      ```java
      public class HelloService {
      
          HelloProperties helloProperties;
      
          public HelloProperties getHelloProperties() {
              return helloProperties;
          }
          public void setHelloProperties(HelloProperties helloProperties) {
              this.helloProperties = helloProperties;
          }
      
          public String sayHellAtguigu(String name){
              return helloProperties.getPrefix()+"-" +name + helloProperties.getSuffix();
          }
      }
      ```

    - 创建HelloServiceAutoConfiguration自动配置类，向容器中添加HelloService：

      ```java
      @Configuration
      @ConditionalOnWebApplication //web应用才生效
      @EnableConfigurationProperties(HelloProperties.class) //属性配置文件生效
      public class HelloServiceAutoConfiguration {
      
          @Autowired
          HelloProperties helloProperties;
          @Bean
          public HelloService helloService(){
              HelloService service = new HelloService();
              service.setHelloProperties(helloProperties);
              return service;
          }
      }
      ```

    - 在META-INF下创建spring.factories：

      ```properties
      org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
      com.atguigu.starter.HelloServiceAutoConfiguration
      ```

    - 在其他项目中引入该starter并使用：

      ```java
      @RestController
      public class HelloController {
      
          @Autowired
          HelloService helloService;
      
          @GetMapping("/hello")
          public String hello(){
              return helloService.sayHellAtguigu("haha");
          }
      }
      ```

      