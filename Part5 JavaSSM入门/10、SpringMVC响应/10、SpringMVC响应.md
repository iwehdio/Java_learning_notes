## 1、响应

- 返回值的分类：

  - 返回值为字符串：跳转到WEB-INF下的与字符串同名的页面。

  - 返回值为void：跳转到WEB-INF下的与请求路径名同名的页面。

    - 需要使用request和response对象进行请求转发或重定向。
    - 请求转发需要提供完整的路径。

    - 重定向需要加虚拟路径，并且不能访问WEB-INF下的资源。

    - 也可以直接用`response.getWriter()`进行响应。

  - 返回值为`ModelAndView`对象：

    - 创建：直接`new ModelAndView`对象。
    - 这个对象中可以用`addObject()`存储键值对到request域中。
    - 可以用`setView()`使用视图解析器跳转到其他资源。

- 使用关键字进行转发和重定向：

  - 请求转发：`return "forward:/WEB-INF/资源名称"`。
  - 重定向：`return "redirect:/资源路径"`。
  - 这时无法使用视图解析器。

- `@ResponseBody`响应JSON数据：

  - 引入JQuery。

    ```xml
    <script src="http://libs.baidu.com/jquery/1.11.1/jquery.min.js"></script>
    ```

  - SpringMVC过滤静态资源：在springmvc.xml下告诉前端控制器那些静态资源不拦截：

    ```xml
    <mvc:resources mapping="/js/**" location="/js/"></mvc:resources>
    ```

  - 发送Ajax请求：

    ```javascript
    <script>
        $(function () {
        $("#btn").click(function () {
            $.ajax({
                url:"user/testAjax",
                contentType:"application/json;charset=utf-8",
                data:'{"name":"hh","age":30}',
                dataType:"json",
                type:"post",
                success:function (data) {
    
                }
            });
        });
    });
    </script>
    ```

  - 将JSON数据封装进Java Bean实体类：

    - 导入jackson依赖。
    - 如果浏览器请求的JSON数据的键值对与实体类中的属性对应，则可以直接使用`(@RequestBody User user)`获取user对象。
    - 如果响应的JSON数据的键值对与实体类中的属性对应，则可以直接使用`public @ResponseBody User`指定返回值，SpringMVC会将该对象转为JSON数据发送给前端。

## 2、文件上传

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

- SpringMVC跨服务器上传：

  - 请求由应用服务器获取。
  - 文件存储到文件服务器中。
  - 使用 jersey 包。



## 3、异常处理

- 异常处理的思路：
  - Controller 调用 Service，Service调用Dao。
  - 出现异常后，都向上抛出，最后由前端控制器的异常处理器处理异常。
  
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

