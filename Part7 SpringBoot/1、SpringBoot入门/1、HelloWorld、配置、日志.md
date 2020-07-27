## 1、SpringBoot入门

- SpringBoot：通过整合整个Spring的技术栈，快速简单的创建产品级应用，简化Spring应用开发，约定大于配置。

- 优点：

  - 快速创建独立运行的Spring项目及主流框架集成。
  - 使用嵌入式的Servlet容器，应用无序打成WAR包。
  - starters自动依赖与版本控制。
  - 大量的自动配置，简化开发，也可以修改默认值。
  - 无需配置xml，无代码生成。

- 微服务：

  - 与之前的单体应用相对应，将每个功能单一独立出来。
  - 是一种架构风格，提倡开发应用时应是一组小型服务的集合。
  - 这些小型服务都被放在独立的服务中，可以通过HTTP通信。
  - 每个服务都是可独立替换可独立升级的软件单元。

- HelloWorld：

  - 浏览器发送一个hello请求，服务器接收请求并处理，响应Hello World字符串。

  - 创建一个maven工程并且导入依赖：

    ```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
    </parent>
    
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
    ```

  - 编写一个主程序，启动SpringBoot应用：

    ```java
    /*
        标注一个主程序类，表示这是一个SpringBoot应用
     */
    @SpringBootApplication
    public class HelloWorldMainApplication {
    
        public static void main(String[] args) {
            SpringApplication.run(HelloWorldMainApplication.class, args);
        }
    }
    ```

  - 编写相关的业务逻辑：

    ```java
    @Controller
    public class HelloController {
    
        @ResponseBody
        @RequestMapping("/hello")
        public String hello() {
            return "Hello World";
        }
    }
    ```

  - 启动主程序，在浏览器就可以访问/hello了。

  - 简化部署：

    - 导入maven插件：

      ```xml
      <build>
          <plugins>
              <plugin>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-maven-plugin</artifactId>
              </plugin>
          </plugins>
      </build>
      ```

    - 在maven侧边栏中运行package命令进行打包，项目被打包在target目录下。

- HelloWorld探究：

  - pom.xml下：

    - 父项目：
      - spring-boot-starter-parent的父项目是spring-boot-dependencies，它真正乱来的SpringBoot应用中的所有依赖版本，是SPringBoot的版本仲裁中心。
      - 在这个版本仲裁中心中管理的依赖，之后再导入不需要写版本。
    - 导入的依赖：
      - spring-boot-starter-web：spring-boot-starter是SpringBoot场景启动器。帮我们导入了web模型正常运行所需要的模块。
      - SpringBoot将所有的功能场景抽取出来，做成starts（启动器），引入starts，相关场景的依赖就会全部导入进来。

  - 主程序类（主入口类）：

    - `@SpringBootApplication`注解：标注在某个类上，说明这个类是SpringBoot的主配置类，SpringBoot就应该启动这个类的main方法来启动SpringBoot应用。

      ```java
      @Target({ElementType.TYPE})
      @Retention(RetentionPolicy.RUNTIME)
      @Documented
      @Inherited
      @SpringBootConfiguration	//表示这个是一个SpringBoot的配置类（类似于之前的配置文件），配置类也是容器内的一个组件
      @EnableAutoConfiguration	//开启自动配置功能
      @ComponentScan(
          excludeFilters = {@Filter(
          type = FilterType.CUSTOM,
          classes = {TypeExcludeFilter.class}
      ), @Filter(
          type = FilterType.CUSTOM,
          classes = {AutoConfigurationExcludeFilter.class}
      )}
      )
      public @interface SpringBootApplication {
      ```

- 使用向导创建SpringBoot应用：

  - Spring Initializer：SpringBoot创建向导。

  - 首先，在IDEA的插件中使SpringBoot插件为可用。

  - 使用Spring Initializer创建工程，选用模块只使用web下的springweb。

  - 这样主程序就生成好了，只需要编写业务逻辑。

    ```java
    @RestController
    public class HelloController {
        @RequestMapping("/hello")
        public String hello() {
            return "hello world quick";
        }
    }
    ```

    - `@RestController`相当于`@Controller`加`@ResponseBody。`

  - resources文件夹中的目录结构：

    - static：保存所有的静态资源。
    - templates：保存了所有的模板页面。
    - application.properties：SpringBoot应用的配置文件，可以修改默认设置。



## 2、SpringBoot配置

- 配置文件：

  - 默认使用两种配置文件application.properties或application.yaml。

    - yaml：是一种标记语言，以数据为中心。
    - 基本语法：
      - 表示键值对：`key: value`，冒号后必须有个空格。
      - 以空格的缩进来控制层级关系，左对齐的一列数据都是同一层级的。
      - 属性和值是大小写敏感的。
      - 不需要写分号。
    - 值的写法：
      - 字面量（数字、字符串、布尔），字符串不用加引号。如果用的话，双引号内特殊字符会转义，单引号内特殊字符原样显示。
      - 键值对：在下一行缩进写键值对。行内写法是用大括号括起来。
      - 数组：`- value`表示数组中的一个元素。行内写法用中括号括起来，不用写 - 。

  - 配置文件的作用：修改SpringBoot自动配置的默认值。

  - SpringBoot单元测试，在测试类上加注解：

    ```java
    @RunWith(SpringRunner.class)
    @SpringBootTest
    ```

  - 将yaml中的配置映射到实体类：在实体类上加注解`@ConfigurationProperties(perfix="person")`，配置和那个属性下一一对应。并且加入容器中。

    - 需要导入：配置文件处理器spring-boot-configuration-processor。

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-configuration-processor</artifactId>
          <optional>true</optional>
      </dependency>
      ```

      

    - 也可以直接在Javabean的属性上加`@Value`，用`${}`获取配置文件中的值。无法获取复杂类型，比如map、list。

  - 在实体类上加`@Validated`，属性上加入具体校验规则可以进行数据校验。

  - `@PropertySource()`注解：加载指定的配置文件。

  - `@ImportResource(locations={})`注解：导入Spring的配置文件。

  - 配置文件占位符，`${}`。

- SpringBoot推荐给容器中添加组件的方式：全注解。

  - 创建配置类，加上`@Configuration`注解。
  - 在方法上添加注解`@Bean`，容器中的默认id为方法名。

- Profile：

  - 多Profile文件实现多环境支持，文件名可以是application-{profile}.properties。

  - 激活指定的profile：默认配置文件中指定，spring.profiles.active={profile}。

  - yaml支持多文档块：

    - 用三个`-`分为多文档块。

    - 为每个文档块设置属性并激活：

      ```yaml
      spring:
      	profiles: 
      		active: prod
      ---
      spring:
      	profiles: prod
      ```

  - 也可以使用指定的命令行参数或虚拟机参数进行激活。

- 配置路径的加载位置：优先级从高到低。

  - file:./config/
  - file:./
  - classpath:/config/
  - classpath:/
  - 高优先级的配置会覆盖低优先级的配置，配置文件会全部加载，可以进行互补配置。
  - 项目打包好后，可以使用命令行参数，使用spring.config.location来设置配置文件位置。

- SpringBoot外部配置的加载顺序（从高到低）：

  1. 命令行参数。
  2. jar包外部的application-{profile}.properties或application.yml(带spring.profile)配置文件。
  3. jar包内部的application-{profile}.properties或application.yml(带spring.profile)配置文件。
  4. jar包外部的application.properties或application.yml(不带spring.profile)配置文件。
  5. jar包内部的application.properties或application.yml(不带spring.profile)配置文件。

- 自动配置原理：

  - SpringBoot启动时，加载主配置类，开启了自动配置功能`@EnableAutoConfiguration`。

  - `@EnableAutoConfiguration`利用selector为容器中导入组件。

  - 将jar包类路径下 META-INF/spring.factories里所有配置的EnableAutoConfiguration.class的值加入到容器中。

  - 每一个自动配置类进行自动配置功能。

  - 以HttpEncodingAutoConfiguration（Http编码自动配置）为例解释自动配置原理：

    ```java
    @Configuration   //表示这是一个配置类，以前编写的配置文件一样，也可以给容器中添加组件
    @EnableConfigurationProperties(HttpEncodingProperties.class)  //启动指定类的ConfigurationProperties功能；将配置文件中对应的值和HttpEncodingProperties绑定起来；并把HttpEncodingProperties加入到ioc容器中
    
    @ConditionalOnWebApplication //Spring底层@Conditional注解，根据不同的条件，如果满足指定的条件，整个配置类里面的配置就会生效；    判断当前应用是否是web应用，如果是，当前配置类生效
    
    @ConditionalOnClass(CharacterEncodingFilter.class)  //判断当前项目有没有这个类CharacterEncodingFilter；SpringMVC中进行乱码解决的过滤器；
    
    @ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true)  //判断配置文件中是否存在某个配置  spring.http.encoding.enabled；如果不存在，判断也是成立的
    //即使我们配置文件中不配置pring.http.encoding.enabled=true，也是默认生效的；
    public class HttpEncodingAutoConfiguration {
      
      	//他已经和SpringBoot的配置文件映射了
      	private final HttpEncodingProperties properties;
      
       //只有一个有参构造器的情况下，参数的值就会从容器中拿
      	public HttpEncodingAutoConfiguration(HttpEncodingProperties properties) {
    		this.properties = properties;
    	}
      
        @Bean   //给容器中添加一个组件，这个组件的某些值需要从properties中获取
    	@ConditionalOnMissingBean(CharacterEncodingFilter.class) //判断容器没有这个组件？
    	public CharacterEncodingFilter characterEncodingFilter() {
    		CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
    		filter.setEncoding(this.properties.getCharset().name());
    		filter.setForceRequestEncoding(this.properties.shouldForce(Type.REQUEST));
    		filter.setForceResponseEncoding(this.properties.shouldForce(Type.RESPONSE));
    		return filter;
    	}
    }
    ```

  - 一旦这个配置类生效，这个配置类就会给容器中添加各种组件。这些组件的属性是从对应的properties类中获取的，这些类里面的每一个属性又是和配置文件绑定的。

- SpringBoot的精髓：

  ​	1）、SpringBoot启动会加载大量的自动配置类

  ​	2）、我们看我们需要的功能有没有SpringBoot默认写好的自动配置类；

  ​	3）、我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件有，我们就不需要再来配置了）

  ​	4）、给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们就可以在配置文件中指定这些属性的值；
  
  - xxxxAutoConfigurartion：自动配置类，给容器中添加组件。
  - xxxxProperties：封装配置文件中相关属性。
  
- `@Conditional`注解判断：

  - 指定一个条件判断类，指定的条件成立，才给容器中添加组件，配置的内容才生效。
  - 派生了许多注解，用于判断各种情况，比如容器中是否有这个bean。
  - 这些注解使得自动配置类在一定的条件下才会生效。

- 自动配置报告：

  - 在配置文件中启用debug=true，在控制台打印自动配置报告，就知道那些自动配置类生效了。



## 3、日志

- SpringBoot选用SLF4j日志门面和logback日志实现。

- 如何使用SLF4j：

  - 开发时日志记录日志的调用，不能直接调用日志的实现类，而是调用日志抽象层的方法。
  - 配置文件还是做成日志实现框架的配置文件。
  - 让日志框架都统一到SLF4j，使用中间包和适配层包。

- SpringBoot日志关系：

  - SpringBoot使用Spring-boot-starter-loggin进行日志记录。
  - 底层使用logback记录日志。
  - 使用中间包，把其他日志框架转为SLF4j。
  - 在此之前需要排除其他日志框架。

- SpringBoot中使用日志：

  - 生成日志记录器并进行日志记录：

    ```java
    Logger logger = LoggerFactory.getLogger(getClass());
    //日志级别由低到高
    logger.trace();
    logger.debug();
    logger.info()；
    logger.warn();
    logger.error();
    ```

  - 可以调整日志的级别，日志就只会在这个级别及以上生效。

  - 在配置文件中设置日志级别，SpringBoot默认info级别：

    ```properties
    logger.level.包名=日志级别
    logging.file=日志记录的位置
    logging.path=日志记录的文件夹
    logging.pattern.console=控制台输出的日志格式
    logging.pattern.file=文件中输出的日志格式
    ```

