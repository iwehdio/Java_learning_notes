## 1、Web应用

- 使用SpringBoot：

  1. 创建SpringBoot应用，选中需要的模块。
  2. SpringBoot默认将这些场景配置好了。只需要在配置文件中指定少量配置。
  3. 编写业务逻辑代码。

- SpringBoot对静态资源的映射规则：

  - 所有/webjars/**的请求，都去classpath：/META-INF/resources/webjars下找资源。webjars就是以jar包的形式引入静态资源。
  - 访问任何资源（/**），自己的静态资源保存在classpath：/META-INF/resources/、classpath：/resources/、classpath：/static/、classpath：/public/或/。
  - 欢迎页：静态资源文件夹下的所有index.html页面，被/**映射。
  - 页面的小图标：静态资源文件夹下的**/favicon.ico。
  - 自定义静态资源文件夹：配置文件下的spring.resources.static-locations。

- thymeleaf 模板引擎：

  - 引入thymeleaf 模板引擎：

    ```xml
    <properties>
      	<thymeleaf.version>3.0.9.RELEASE</thymeleaf.version>
        <thymeleaf-layout-dialect.version>2.2.2</thymeleaf-layout-dialect.version>
    </properties>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

  - 将HTML页面放在classpath：/templates/下，thymeleaf就可以自动渲染。

  - thymeleaf 语法：

    - 导入thymeleaf名称空间：

      ```html
      <html xmlns:th="http://www.thymeleaf.org">
      ```

    - 将div中的文本内容设置为指定的值：

      ```java
      @RequestMapping("/success")
      public String success(Map<String, String> map) {
          map.put("hello", "testHello");
          return "success";
      }
      ```

      ```html
      <div th:text="${hello}">    
      </div>
      ```

    - `th:text`：改变当前元素里的文本信息，不会转义。`th:任意html属性`都可以替换原有html属性的值。

- SpringMVC自动配置原理：

  - SpringBoot对SpringMVC的默认配置：
    - 在WebMvcAutoConfiguration下，自动配置了视图解析器。
    - 支持静态首页访问、静态资源文件夹路径等。
    - 自动注册了Converter、Formatter，即类型转换器、格式化器。
    - 支持HttpMessageConverters即Http信息转换器，用来转换Http请求和响应。
    - 在SpringBoot中会有非常多的xxxConfigurer帮助我们进行扩展配置，会有很多的xxxCustomizer帮助我们进行定制配置。
  - 如果需要修改默认配置，只需要将一个实现了对应接口的组件加入容器中。
  - SpringBoot自动配置的模式：
    
    - 配置组件前，先看容器中有没有用户配置的，没有才会执行自动配置。多个组件会将用户配置和默认的组合使用。
  - 扩展SpringMVC：
    - 编写一个`@Configuration`标注的配置类，需要继承WebMvcConfigurationAdapter抽象类，且不能加`@EnableWebMvc`。
    
    - 这样保留了自动配置，也可以用自己的扩展配置。
    
    - 在做其他自动配置时，会导入EnableWebMvcConfiguration。从容器中获取所有的WebMvcConfigurer，所有的WebMvcConfigurer都会起作用。包括默认配置和自己的配置类。
    
    - 自己的配置类上加了`@EnableWebMvc`就会完全接管SpringMVC。去掉所有默认配置。
    
      ```java
      //使用WebMvcConfigurerAdapter可以来扩展SpringMVC的功能
      @Configuration
      public class MyMvcConfig extends WebMvcConfigurerAdapter {
      
          @Override
          public void addViewControllers(ViewControllerRegistry registry) {
              //浏览器发送 /atguigu 请求来到 success
              registry.addViewController("/atguigu").setViewName("success");
          }
      }
      ```
    
      

- REST-CRUD实验：

  - 引入资源：静态资源放入static文件夹下，html页面放入templates文件夹下。

  - 首页访问：

    - 在控制器中编写一个方法。

      ```java
      @RequestMapping({"/index","/"})
      public String index() {
          return "index";
      }
      ```

    -  在WebMvcConfigurer中添加视图映射：

      ```java
      @Configuration
      public class MyMvcConfig extends WebMvcConfigurerAdapter {
          @Bean
          public WebMvcConfigurerAdapter webMvcConfigurerAdapter() {
              WebMvcConfigurerAdapter adapter = new WebMvcConfigurerAdapter() {
                  @Override
                  public void addViewControllers(ViewControllerRegistry registry) {
                      registry.addViewController("/").setViewName("index");
                      registry.addViewController("/index.html").setViewName("index");
                  }
              };
              return adapter;
          }
      }
      ```

  - 国际化：

    - 编写国际化配置文件。
    - SpringBoot自动配置好了国际化管理组件。
    - 在配置文件中配置国际化文件的路径。
    - 在前端用`th:text="#{}"`获取。

  - 登陆：

    - 控制器获取参数并判断：

      ```java
      @PostMapping("/user/login")
      public String login(String username, String password, Map<String, Object> map, HttpSession session) {
          if(!StringUtils.isEmpty(username) && "123456".equals(password)) {
              session.setAttribute("loginUser", username);
              return "dashboard";
          } else {
              map.put("msg","用户名密码错误");
              return "index";
          }
      
      }
      ```

    - 前端提示错误：

      ```html
      <p style="color:red;" th:text="${msg}" th:if="${not #strings.isEmpty(msg)}"></p>
      ```

    - 为了防止表单重复提交：

      ```java
      //在MyMvcConfig中再设置一个视图映射
      registry.addViewController("/main.html").setViewName("dashboard");
      
      //在控制器中重定向到该映射
      return "redirect:/main.html";
      ```

  - 拦截器进行登陆检查：

    - 编写一个拦截器，如果没有从session中获取到username就不放行并跳转到登录页面：

      ```java
      public class MyInterceptor implements HandlerInterceptor {
          @Override
          public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o) throws Exception {
              Object user = httpServletRequest.getSession().getAttribute("loginUser");
              if(user == null) {
                  httpServletRequest.setAttribute("msg", "权限不足");
                  httpServletRequest.getRequestDispatcher("/index.html").forward(httpServletRequest, httpServletResponse);
                  return false;
              } else {
                  return true;
              }
          }
      }
      ```

    - 在配置类MyMvcConfig下添加拦截器组件，并配置拦截规则：

      ```java
      @Configuration
      public class MyMvcConfig extends WebMvcConfigurerAdapter {
          @Bean
          public WebMvcConfigurerAdapter webMvcConfigurerAdapter() {
              WebMvcConfigurerAdapter adapter = new WebMvcConfigurerAdapter() {
                  @Override
                  public void addInterceptors(InterceptorRegistry registry) {
                      registry.addInterceptor(new MyInterceptor()).addPathPatterns("/**")
                              .excludePathPatterns("/", "/index.html", "/user/login");
                  }
              };
              return adapter;
          }
      }
      ```

  - Restful请求架构：

    |                                     | 请求URI  | 请求方式 |
    | ----------------------------------- | -------- | -------- |
    | 查询所有员工                        | emps     | GET      |
    | 查询/修改某个员工（来到其修改页面） | emp/{id} | GET      |
    | 来到添加页面                        | emp      | GET      |
    | 添加员工                            | emp      | POST     |
    | 修改员工                            | emp      | PUT      |
    | 删除员工                            | emp/{id} | DELETE   |

  - 员工列表：

    - 编写控制器：

      ```java
      @Controller
      public class EmployeeController {
          @Autowired
          EmployeeDao employeeDao;
      
          @GetMapping("/emps")
          public String list(Model model) {
              Collection<Employee> employees = employeeDao.getAll();
      
              model.addAttribute("emps", employees);
      
              return "emp/list";
          }
      }
      ```

  - 公共页抽取：

    - 抽取公共片段：`th:fragment="片段id"`。

      - 抽取`th:fragment="sidebar"`。

    - 引入公共片段：`th:insert="~{模板名::片段id"}`。片段id也可以用选择器替代。

      - 还可以用`th:repclace`或`th:include`。
      - 三种效果不同，第一种是将公共片段整个插入声明引入的标签内。第二种是将声明引入的标签用公共片段取代。第三中是只将公共片段的内容引入，不引入标签。

      - 使用抽取：`th:replace="commons/bar::topbar"`。

    - 动态引入参数（class为active则高亮）：

      - 抽取出的页面：`th:class="${activeUri=='main.html'?'nav-link active':'nav-link'}"`。
      - 抽取页面被调用：`th:replace="commons/bar::sidebar(activeUri='main.html')"`。

  - 遍历取出员工数据：

    ```html
    <tr th:each="emp:${emps}">
        <td th:text="${emp.id}"></td>
        <td>[[${emp.lastName}]]</td>
        <td th:text="${emp.email}"></td>
        <td th:text="${emp.gender}==0?'女':'男'"></td>
        <td th:text="${emp.department.departmentName}"></td>
        <td th:text="${emp.birth}"></td>
    </tr>
    ```

    - 日期的格式化：`th:text="${#dates.format(emp.birth,'yyyy-MM-dd HH:mm')}"`。

  - 员工添加：

    - 来到添加页面：

      ```java
      @GetMapping("/emp")
      public String add(Model model) {
          Collection<Department> departments = departmentDao.getDepartments();
          model.addAttribute("depts", departments);
          return "emp/add";
      }
      ```

    - 前端是一个form表单，并且下拉菜单通过遍历depts动态获取。

    - 添加功能：

      - 前端以post方式提交表单。提交完成后直接来到员工列表页面。

      - 提交form表单：`<form th:action="@{/emp}" th:method="post">`。

      - 处理并保存：

        ```java
        @PostMapping("/emp")
        public String addEmp(Employee employee) {
            employeeDao.save(employee);
            return "redirect:emps";
        }
        ```

    - 处理提交日期的格式不对：

      - 默认日期按照 / 的方式分隔。
      - 可以通过spring.mvc.date-format配置。

  - 员工修改：

    - 来到修改页面：查出当前员工，在页面回显。

      ```java
      @GetMapping("/emp/{id}")
      public String toEditPage(@PathVariable("id") Integer id, Model model) {
          Employee employee = employeeDao.get(id);
          model.addAttribute("emp", employee);
          Collection<Department> departments = departmentDao.getDepartments();
          model.addAttribute("depts", departments);
          return "emp/add";
      }
      ```

    - 前端用`${emp!=null}`判断是添加页面还是修改页面。用__method指定请求方式。

      ```html
      <input type="hidden" name="_method" value="put">
      ```
      
      ```java
      @PutMapping("/emp")
      public String edit(Employee employee) {
          employeeDao.save(employee);
        System.out.println(employee);
          return "redirect:emps";
    }
      ```
  
  - 员工删除：
  
    ```java
    @DeleteMapping("/emp/{id}")
    public String delete(@PathVariable("id") Integer id) {
        employeeDao.delete(id);
        return "redirect:/emps";
    }
    ```



- 错误处理机制：

  - 默认效果：浏览器返回一个默认的错误页面，有错误类型、状态码和错误信息。其他客户端默认返回JSON。

  - 错误处理原理：

    - 往容器中添加了几个组件。
    - 系统出现4xx或者5xx的错误：ErrorPageCustomizer就会生效。来到/error请求。
    - 处理默认/error请求：BasicErrorController处理错误请求。
    - 去往那个视图：DefaultErrorViewResolver解析视图。
    - 获取相关错误信息：DefaultErrorAttributes。

  - 如何定制错误页面：

    - 有模板引擎的情况下，在templates下创建/error下的定制页面 状态码.html。
    - 没有模板引擎的情况下，在静态资源文件夹下找。
    - 以上都没有错误页面，默认来到SpringBoot的空白页面。

  - 定制错误JSON数据：

    - 创建异常处理器，添加`@ControllerAdvice`注解，增强的控制器，可进行全局异常处理。

    - 处理异常的方法，上加注解`@ExceptionHandler(错误类型.class)`。

    - 返回JSON数据：将写出的数据放入一个map中并返回，并且加上`@ResponseBody`。

      ```java
      @ControllerAdvice
      public class MyExceptionHandler {
      
          @ResponseBody
          @ExceptionHandler(UserNotExistException.class)
          public Map<String,Object> handleException(Exception e){
              Map<String,Object> map = new HashMap<>();
              map.put("code","user.notexist");
              map.put("message",e.getMessage());
              return map;
          }
      }
      ```

    - 如果需要自适应不同终端：转发到/error进行处理。需要自己设置错误状态码。

    - 如果还需要携带定制数据：编写一个继承ErrorController的子类。或者重写ErrorAttributes.getErrorAttributes错误数据处理方法。

- 配置嵌入式Servlet容器：

  - Springboot默认是使用嵌入式的Tomcat。

  - 如何定制和修改Servlet容器的配置：

    - 在项目配置文件下修改server下的配置。

    - 编写一个定制器EmbeddedServletContainerCustomizer。

      ```java
      @Configuration
      public class MyServerConfig {
      	@Bean  //一定要将这个定制器加入到容器中
          public EmbeddedServletContainerCustomizer embeddedServletContainerCustomizer(){
              return new EmbeddedServletContainerCustomizer() {
      
                  //定制嵌入式的Servlet容器相关的规则
                  @Override
                  public void customize(ConfigurableEmbeddedServletContainer container) {
                      container.setPort(8083);
                  }
              };
          }
      }
      ```

      

  - 注册Servlet、Filter、Listener：

    - 因为是嵌入式Servlet容器，没有web.xml。

    - 创建一个加了`@Configuration`注解的配置类。在配置类中创建方法，并将返回值加入容器。

    - 注册Servlet：创建ServletRegistrationBean，传入一个Servlet和映射的路径，并返回。

    - 注册Filter：创建FilterRegistrationBean，传入一个Servlet和拦截的路径，并返回。

    - 注册Listener：创建ServletListenerRegistrationBean，传入一个Servlet和监听的路径，并返回。

    - 都是在前端控制器自动配置时DispatcherServletAutoConfiguration注册的。

    - server.servletPath可以更改前端控制器的拦截路径。

      ```java
      @Configuration
      public class MyServerConfig {
      
          //注册三大组件
          @Bean
          public ServletRegistrationBean myServlet(){
              ServletRegistrationBean registrationBean = new ServletRegistrationBean(new MyServlet(),"/myServlet");
              registrationBean.setLoadOnStartup(1);
              return registrationBean;
          }
      
          @Bean
          public FilterRegistrationBean myFilter(){
              FilterRegistrationBean registrationBean = new FilterRegistrationBean();
              registrationBean.setFilter(new MyFilter());
              registrationBean.setUrlPatterns(Arrays.asList("/hello","/myServlet"));
              return registrationBean;
          }
      
          @Bean
          public ServletListenerRegistrationBean myListener(){
              ServletListenerRegistrationBean<MyListener> registrationBean = new ServletListenerRegistrationBean<>(new MyListener());
              return registrationBean;
          }
      }
      ```

      

  - 支持其他的Servlet容器：

    - Springboot支持Jetty和Undertow容器。
    - 在maven中排除Tomcat的依赖，引入其他的Servlet容器。

  - 嵌入式Servlet容器自动配置原理：

    - 自动配置类EmbeddedServletContainerAutoConfiguration。
    - 判断当前是否引入了Tomcat依赖，判断有没有用户自定义的嵌入式Servlet容器工厂。
    - 创建对应的嵌入式Servlet容器工厂。
    - 创建一个Tomcat对象，并且进行相关环境配置。
    - 通过后置处理器，调用定制器处理我们对容器的相关配置。

  - 嵌入式Servlet容器的启动原理：

    - 什么时候创建嵌入式的Servlet容器工厂：
      - Springboot启动运行run方法。
      - 创建并初始化IOC容器对象。
      - WebIOC容器创建嵌入式的Servlet容器。
      - 获取嵌入式Servlet容器工厂。
      - 使用容器工厂创建容器。
      - 容器创建对象并启动Servlet容器。
      - 总之就是，IOC容器启动创建嵌入式的Servlet容器。

- 使用外置的Servlet容器：

  - 嵌入式Servlet容器把应用打成jar包，不支持JSP，优化定制比较复杂。
  - 外置的Servlet容器，需要创建一个war工厂，将嵌入式Tomcat环境指为provided。
  - 必须编写一个SpringBootServletInitializer的子类，并调用Configure方法。
  - 使用war包，先启动服务器，服务器启动Springboot应用，再启动IOC容器。
