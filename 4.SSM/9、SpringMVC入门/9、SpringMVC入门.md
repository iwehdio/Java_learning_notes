## 1、SpringMVC

> 一个表现层框架，与浏览器进行数据交互。

- MVC设计模型：

  - model 模型（如Java Bean）。
  - view 视图（如HTML）。
  - controller 控制器（如Servlet）。

- SpringMVC入门（搭建环境）：

  - 需求：点击超链接转发页面。

  - maven 下搭建配置，选择webapp：

    ```xml
    <properties>
        <!-- 版本锁定 -->
        <spring.version>5.0.2.RELEASE</spring.version>
    </properties>
    
    <dependencies>
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
      </dependencies>
    ```
  
- 在webapp/WEB-INF下的web.xml中配置前端控制器，并加载配置文件：
  
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
  ```
  
- 在resources下创建springmvc.xml。
  
- 配置Tomcat。
  
- SpringMVC入门程序：

  - 在webapp下创建index.jsp：

    ```jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <title>Title</title>
    </head>
    <body>
        <h3>入门程序</h3>
        <a href="hello">入门</a>
    </body>
    </html>
    ```

  - 创建controller.Hello类：

    ```java
    @Controller
    public class Hello {
        @RequestMapping(path = "/hello")
        public String sayHello() {
            System.out.println("hello");
            return "success";
        }
    }
    ```

  - 配置springmvc.xml配置文件：

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
        <!-- 扫描包 -->
        <context:component-scan base-package="cn.iwehdio"></context:component-scan>
        <!-- 视图解析器对象 -->
        <bean id="internalResourceView" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
            <property name="prefix" value="/WEB-INF/pages/"></property>
            <property name="suffix" value=".jsp"></property>
        </bean>
        <!-- 开启springmvc注解支持，同时加载处理器适配器和处理器映射器 -->
        <mvc:annotation-driven></mvc:annotation-driven>
    </beans>
    ```

  - 跳转目标：

    ```jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <title>Title</title>
    </head>
    <body>
        <h3>success</h3>
    </body>
    </html>
    ```

- 入门程序的流程：

  1. 启动服务器，加载配置文件。
     -  `<load-on-startup>`标签，启动服务器就创建DispatcherServlet对象。
     - 加载springmvc.xml配置文件。
     - Hello类被创建为对象。
     - 创建视图解析器对象。
     - 开启注解支持，Hello类上的注解生效。
  2. 发送请求，后台处理请求。
     - 点击超链接，发送请求。
     - DispatcherServlet前端控制器对象拦截了所有请求，根据扫描到的注解找到与路径对应的方法并执行。返回的是页面的名称。
     - 前端控制器找到视图解析器对象，跳转到对应的请求并响应客户端。

- SpringMVC底层是基于组件的方式执行流程：

  - 前端控制器DispatcherServlet：

    - 获取用户的请求。
    - 请求查找Handler。

  - 处理映射器HandlerMapping：

    - 查找请求路径，搜索加了`@Controller`路径的类中的方法中被`@RequestMapping`注解的path属性。
    - 返回找到的对应的方法。

  - 处理器适配器HandlerAdapter：

    - 前端控制器请求其执行Handler。

  - 处理器Handler（controller）：

    - 处理器适配器请求其执行。
    - 返回所要执行的视图。

  - 视图解析器ViewResolver：

    - 解析视图，跳转到视图页面。
    - 前端控制器响应浏览器。

  - 执行流程：

    ![springmvc执行流程原理](img/SpringMVC入门/springmvc执行流程原理.jpg)

- `@Controller`注解：

  -  用于标记在一个类上，使用它标记的类就是一个SpringMVC Controller 对象。分发处理器将会扫描使用了该注解的类的方法。
  - 通俗来说，被Controller标记的类就是一个控制器，这个类中的方法用于处理请求。
  
- `@RequsetMapping`注解：

  - 建立请求URL和处理请求方法之间的关系。
  - path/value 属性：执行方法对应的请求路径。
  - 可以放在类上或者方法上。放在类上可以用于多级目录中的前几级。
  - method 属性：请求的方式，枚举类型。
  - param 属性：用于限定请求参数的条件。指定请求的参数必须包含哪些内容，或者请求参数的值必须为什么。
    - 如params={"at=q"}，则指定请求参数中必须有at属性且值为q。
  - headers 属性：发送的请求必须包含该消息头。

- 请求参数的绑定：

  - 绑定机制：
    - 如果请求参数的键与对应方法的参数名称相同，则就可以进行绑定，该方法可以直接读入参数。
  - 数据类型：
    - 基本数据类型、String、JavaBean和集合。
  - 请求参数绑定JavaBean：
    - 创建实体类。
    - 在前端使用`<form>`表单，action 路径指向所要执行的方法。name 属性与JavaBean属性名相对应。
    - 如果实体类中有引用类型，name 属性写为 `引用类型.属性`。
    - 在对应方法的参数列表中添加所要读入的实体类对象。
  - 请求参数绑定集合类型：
    - 实体类中存在List和Map集合。
    - List集合使用`列表名[索引值]`，Map集合使用`集合名[键值]`。

- 解决中文乱码的过滤器：

  - web.xml 配置：

    ```xml
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    ```

- 自定义类型转换器：

  - 页面提交的任何数据都是字符串类型的。如果提交的是数字，页面提交的也是字符串，但是MVC自动进行了从String到Integer类型的转换。

  - 比如日期写 2000/11/11 会自动封装为Date类型，但如果写2000-11-11会出现错误。需要自定义类型转换器。

  - 第一步，定义一个类，实现Converter接口，有两个泛型，第一个表示输入类型，第二个表示所要转换成什么类型。

    ```java
    public class StringToDate implements Converter<String, Date> {
        @Override
        public Date convert(String source) {
            if(source==null){
                throw new RuntimeException("请传入数据");
            }
            DateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
            try {
                return dateFormat.parse(source);
            } catch (ParseException e) {
                throw new RuntimeException("类型转换出错");
            }
        }
    }
    ```

  - 第二步，配置自定义类型转换器。

    - 在springmvc.xml配置文件下。配置自定义类型转换器：

      ```xml
      <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
          <property name="converters">
              <set>
                  <bean class="cn.iwehdio.utils.StringToDate"></bean>
              </set>
          </property>
      </bean>
      ```

    - 开启类型转换：

      ```xml
      <mvc:annotation-driven conversion-service="conversionService"></mvc:annotation-driven>
      ```

- 获取Servlet的Request和Response的对象：

  - 在方法的参数列表中直接写`(HttpServletRequest request,HttpServletResponse response)`。

- `@RequestParam`注解：

  - 把请求中指定名称的参数给控制器中的形参赋值。
  - 在方法的参数列表中，`(@RequestParam(name="name") String username)`，指的是把传入的name参数赋给username。

- `@RequestBody`注解：

  - 用于获得请求体内容，直接使用获得键值对结构的数据（`key1=v1&key2=v2`），get方式不适用。
  - 在方法的参数列表中，`(@RequestBody String body)`，指的是获取的把请求体数据存入body。

- `@PathVariable`注解：

  - 用于绑定url中的占位符。
  - 如果调用方法的路径为`@RequestMapping("/user/{sid}")`，则实际访问该方法时应输入 /user/10,10为sid的值。
  - 在方法的参数列表中，`(@PathVariable(name="sid") String id)`，在方法中获得sid的值并赋值给id。

- `@RequestHeader`注解：

  - 获取请求消息头。
  - 在方法的参数列表中，`(@RequestHeader(value="Accept") String header)`，在方法中获得请求头Accept的值并赋值给header。

- `@CookieValue`注解：

  - 获得指定cookie的值。
  - 在方法的参数列表中，`(@CookieValue(name="JSESSIONID") String cookieValue)`，在方法中获得cookie中键为JSESSIONID的值并赋值给cookieValue。

- `@ModelAttribute`注解：

  - 出现在方法上，表示当前方法会在控制器的方法执行之前先执行。
    - 如果方法有返回值，则控制器方法会接收到同名的该返回值。
    - 如果方法没有返回值，需要在该方法的参数列表声明一个map集合，将数据存入该map集合中。
    - 然后在控制器方法的参数列表中，用`(@ModelAttribute("abc") String value)`获取到数据。
  - 出现在参数上，获取指定的数据。

- `@SessionAttributes`注解：

  - 用于多次执行控制器方法间的参数共享。
  - 只能作用在类上。
  - 在控制器参数列表中传入`(Model model)`，然后调用`model.addAttribute("msg",value)`方法传入键值对，可以将其存入request域中。
  - 在类上写`@SessionAttributes(value={"msg"})`表示将该数据也存入session中。

- `HiddentHttpMethodFilter`过滤器：

  - 将浏览器的请求方式改为指定的请求方式。

