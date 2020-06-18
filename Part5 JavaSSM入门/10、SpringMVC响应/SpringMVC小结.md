### （1）配置

- web.xml的配置：

  - 配置前端控制器：

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

  - 解决中文乱码：

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

    

  

- springmvc.xml的配置：

  - 约束：
  
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
    </bean>
    ```
  
  -  所要扫描的包：
  
     ```xml
      <context:component-scan base-package="cn.iwehdio"></context:component-scan>
     ```
  
  -  加载视图解析器对象：
  
     ```xml
     <bean id="internalResourceView" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
         <property name="prefix" value="/WEB-INF/pages/"></property>
         <property name="suffix" value=".jsp"></property>
     </bean>
     ```
  
  -  开启springmvc注解支持，同时加载处理器适配器和处理器映射器：
  
     ```xml
     <mvc:annotation-driven></mvc:annotation-driven>
     ```
  
  -  SpringMVC过滤静态资源：在springmvc.xml下告诉前端控制器那些静态资源不拦截：
  
     ```xml
     <mvc:resources mapping="/js/**" location="/js/"></mvc:resources>
     ```

### （2）注解

- `@Controller`注解：
  - 指定这个类是控制器类，这个类中的方法用于处理请求。。
- `@RequsetMapping`注解：

  - 建立请求URL和处理请求方法之间的关系。
  - path/value 属性：执行方法对应的请求路径。
- 请求参数绑定：
  - 请求参数绑定JavaBean：
    - 在前端使用`<form>`表单，action 路径指向所要执行的方法。name 属性与JavaBean属性名相对应。
  - 请求参数绑定集合类型：
    - List集合使用`列表名[索引值]`，Map集合使用`集合名[键值]`。
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
- 将JSON数据封装进Java Bean实体类：

  - 导入jackson依赖。
  - 如果浏览器请求的JSON数据的键值对与实体类中的属性对应，则可以直接使用`(@RequestBody User user)`获取user对象。
  - 如果响应的JSON数据的键值对与实体类中的属性对应，则可以直接使用`public @ResponseBody User`指定返回值，SpringMVC会将该对象转为JSON数据发送给前端。

### （3）响应

- 返回值的分类：

  - 返回值为字符串：跳转到WEB-INF下的与字符串同名的页面。

  - 返回值为void：跳转到WEB-INF下的与请求路径名同名的页面。

    - 需要使用request和response对象进行请求转发或重定向。
    - 请求转发需要提供完整的路径。

    - 重定向需要加虚拟路径，并且不能访问WEB-INF下的资源。

    - 也可以直接用`response.gwtWriter()`进行响应。

  - 返回值为`ModelAndView`对象：

    - 创建：直接`new ModelAndView`对象。
    - 这个对象中可以用`addObject()`存储键值对到request域中。
    - 可以用`setView()`使用视图解析器跳转到其他资源。

- 使用关键字进行转发和重定向：

  - 请求转发：`return "forward:/WEB-INF/资源名称"`。
  - 重定向：`return "redirect:/资源路径"`。
  - 这时无法使用视图解析器。

### （4）组件

- 自定义类型转换器：

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

- 文件上传：

  - 文件上传的前提：

    - form表单的enctype取值必须是：multipart/form-data。
    - method属性取值必须是Post。
    - 提供一个文件选择域`<input type="file" />`。
    - 导入第三方组件 commons-fileupload。

  - SpringMVC上传文件：

    - springmvc.xml中配置文件解析器对象：

      ```xml
      <bean id="multipartResolver"
      class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
      	<property name="maxUploadSize" value="10485760"/>
      </bean>
      ```

    - SpringMVC上传文件：

      ```java
      @RequestMapping("/upload")
      public String fileupload1(HttpServletRequest request, MultipartFile upload) throws Exception {
          // 获取到要上传的文件目录
          String path = request.getSession().getServletContext().getRealPath("/uploads");
          File file = new File(path);
          if(!file.exists()) {
              file.mkdirs();
          }
          String filename = upload.getOriginalFilename();
          upload.transferTo(new File(path, filename));
          return "success";
      }
      ```

    - 前端页面：

      ```jsp
      <form action="user/upload" method="post" enctype="multipart/form-data">
          <input type="file" name="upload">
          <input type="submit">
      </form>
      ```

- 异常处理的过程：

  - 编写自定义异常类，进行信息的提示。

    ```java
    ublic class SysException extends Exception {
    
        private String message;
        public SysException(String message) {
            this.message = message;
        }
        public String getMessage() {
            return message;
        }
        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

  - 出现异常时捕获并且抛出自定义异常类：

    ```java
    @RequestMapping("/testException")
    public String testException() throws SysException {
        System.out.println("异常处理");
        try {
            int a = 1/ 0;
        } catch (Exception e) {
            e.printStackTrace();
            //抛出自定义异常信息
            throw new SysException("查询出错");
        }
        return "success";
    }
    ```

  - 编写异常处理器。

    ```java
    public class SysExceptionResolver implements HandlerExceptionResolver {
        @Override
        public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
            //获取异常对象
            SysException se = null;
            if(e instanceof SysException) {
                se = (SysException) e;
            } else {
                se = new SysException("维护");
            }
            ModelAndView modelAndView = new ModelAndView();
            modelAndView.addObject("errorMsg", se.getMessage());
            modelAndView.setViewName("error");
            return modelAndView;
        }
    }
    ```

  - 配置异常处理器。

    ```xml
    <bean id="sysExceptionResolver" class="cn.iwehdio.exception.SysExceptionResolver"></bean>
    ```

  - 跳转到提示页面，pages下的error.jsp。

    

- 拦截器：

  - 类似于过滤器。对处理器（Controller）进行预处理和后处理。

  - 只会拦截控制器方法，不会拦截前端的html、jsp、js等资源。

  - 必须实现`HandlerInterceptor`接口。

  - 编写拦截器：

    - 编写拦截器：

      ```java
      public class Interceptor implements HandlerInterceptor {
          //预处理，控制器执行前，return true表示放行
          @Override
          public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
              System.out.println("拦截器执行");
              return true;
          }
      }
      ```

    - 配置拦截器：

      ```xml
      <mvc:interceptors>
          <mvc:interceptor>
              <!-- 要拦截的方法 -->
              <mvc:mapping path="/user/*"/>
              <bean class="cn.iwehdio.interceptor.Interceptor"></bean>
          </mvc:interceptor>
      </mvc:interceptors>
      ```

  - 拦截器接口中的方法：

    - `preHandler()`：预处理方法，在控制器执行前执行。
    - `postHandler()`：后处理方法，在控制器执行后执行。
    - `afterCompletion()`：在jsp页面执行后执行。