# 43、Filter&Listener

## 1、Filter

> 当浏览器请求服务器资源时，过滤器可以将请求拦截下来，完成一些特殊的功能。

- 过滤器的作用：一般用于完成通用的操作。比如登录验证、字符编码等。

- 过滤器的使用步骤：

  1. 定义一个类，实现接口 Filter （javax.Servlet下）。

  2. 复写接口中的方法，主要是 `doFilter()`方法。

  3. 配置拦截路径。

     - 注解配置：在实现类上 `WebFilter("拦截路径")`。为 /\* 时，访问任何资源都会执行。

     - web.xml 下配置：

       ```xml
       <filter>
           <filter-name>过滤器名称</filter-name>
           <filter-name>过滤器全类名</filter-name>
       </filter>
       <filter-mapping>
           <filter-name>过滤器名称</filter-name>
           <url-pattern>拦截路径</url-pattern>
       </filter-mapping>
       ```

  4.  `doFilter()`方法中确定是拦截还是放行。

     - 放行，则会执行完过滤器后再访问到对应的资源：`filerChain.doFilter(servletRequest, servletResponse)`。
     -  不放行则默认拦截。

- 过滤器的执行流程：

  1. 执行过滤器在放行前的操作。一般是对 request 中的数据增强。
  2. 放行，执行拦截路径所指向的资源。访问资源，根据增强后的 request 返回 response。
  3. 执行过滤器在放行后的操作。一般是对 response 中的数据增强。

- 过滤器的生命周期：

  - `init()`方法：在服务器启动后，创建 Filter 对象，直接调用 init 方法。一般用于加载资源，执行一次。
  - `doFilter()`方法：每次请求被拦截时，会被执行一次。执行多次。
  - `destroy()`方法：服务器关闭后，Filter 对象被销毁。正常关闭则执行 destroy 方法。一般用于释放资源，执行一次。

- 拦截路径配置：

  - 具体的资源路径：只有访问该具体资源时，过滤器才会被执行。
  - 目录拦截： `/目录/*`，访问该目录下的所有资源时，过滤器都会被执行。
    - 目录：配置 Servlet 路径时，在前边加一级目录：`/目录/Servlet路径`。
  - 后缀名拦截：`*.后缀名`，访问所有该后缀名的资源时，过滤器都会被执行。
  - 拦截所有资源：`/*`，访问所有资源时，过滤器都会被执行。

- 拦截方式配置：

  - 拦截方式：资源被访问的方式，浏览器直接访问、转发等。只有资源是以所设置的属性值的方式被访问时，过滤器才会被执行。
  - 注解配置：设置 dispatcherTypes 属性，`dispatcherTypes=DispatcherType.属性值`。
    - REQUEST：默认值，浏览器直接请求资源。
    - FORWARD：转发访问资源。
    - INCLUDE：包含访问资源。
    - ERROR：发生错误后跳转的资源。
    - ASYNC：异步访问资源。
    - 配置多个值：用 { } 包含多个 DispatcherType.属性值。
  - web.xml 配置：`<filter-mapping></filter-mapping>`标签下，在`<dispatcher></dispatcher>`中配置属性。

- 过滤器链：配置多个过滤器。

  - 执行顺序：以两个过滤器：过滤器1（在前）和过滤器2（在后），为例。
    1. 过滤器1放行前。
    2. 过滤器2放行前。
    3. 资源执行。
    4. 过滤器2放行后。
    5. 过滤器1放行后。
  - 过滤器的先后顺序问题：
    - 注解配置：按照类名的字符串比较规则比较，值小的先执行。
    - web.xml：哪一个过滤器的的`<filter-mapping>`标签在 xml 文件中配置的位置靠前，就先执行。
  
- 代理模式：

  - 真实对象：被代理的对象。

  - 代理对象。

  - 代理模式：代理对象代理真实对象，达到增强真实对象功能的目的。

  - 实现方式：

    - 静态代理：有一个类文件描述代理模式。
    - 动态代理：在内存中生成代理类。

  - 动态代理实现步骤：

    1. 代理对象和真实对象实现相同的接口。
    2. `Proxy.newProxyInstance()`获取代理对象。
       - 第一个参数：类加载器，`真实对象.getClass().getClassLoader()`。
       - 第二个参数：接口数组，`真实对象.getClass().getInterfaces()`。
       - 第三个参数：处理器。在这个处理器中的 invoke 方法中实现增强。
       - 返回值是代理对象，可将其强转为接口类型。
    3. 使用代理对象调用方法。
    4. 增强方法。

  - 动态代理实现：

    ```java
    //接口
    public interface ProxyInt {
        public String show(int num);
    }
    //真实对象
    public class ProxyDemo implements ProxyInt{
        public String show(int num){
            System.out.println(num);
            return Integer.toString(num + 1);
        }
    }
    //动态代理测试
    public class ProxyTest {
        public static void main(String[] args) {
            ProxyDemo demo = new ProxyDemo();
            ProxyInt proxy_demo = (ProxyInt) Proxy.newProxyInstance(demo.getClass().getClassLoader(), demo.getClass().getInterfaces(), new InvocationHandler() {
                @Override
                public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                    System.out.println(method.getName());
                    System.out.println(args[0]);
                    return method.invoke(demo, args);
                }
            });
            String s = proxy_demo.show(20);
            System.out.println(s);
        }
    }
    ```

  - 处理器中的 invoke 方法：

    - 参数 proxy 表示代理对象，参数 method 表示调用的真实对象的方法对象，参数 args 表示传入所调用方法的参数列表。
    - 使用真实对象调用该方法：`Object obj = method.invoke(真实对象, args)`。

  - 增强方法的方式：

    1. 增强参数。
    2. 增强返回值。
    3. 增强方法体。

  - 在 invoke 方法中增强方法：

    1. 通过 `method.getName()` 判断调用的是哪个方法。
    2. 更改调用真实方法的参数，增强参数。
    3. 使用真实对象调用该方法前后，进行其他操作，增强方法体。
    4. return 返回值时对返回值进行操作，增强返回值。

## 2、Filter案例

-  登陆验证：

  -  步骤：

    1. 获取请求的资源路径 uri 。
    2. 判断 uri 中是否包括登陆相关的资源，排除包括 jsp 、Servlet 和 css、js 资源。
    3. 如果已经登陆则放行。
    4. 如果没有登录则跳转到登陆页面。

  - 实现：

    ```java
    @WebFilter("/*")
    public class LoginFilter implements Filter {
        public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
            //强制转换为HttpServletRequest
            HttpServletRequest request = (HttpServletRequest)req;
            String uri = request.getRequestURI();
            if(uri.contains("/login.jsp") || uri.contains("/loginServlet") || uri.contains("/checkCodeServlet") ||
                    uri.contains("/css/") || uri.contains("/js/") || uri.contains("/fonts/")){
                chain.doFilter(req, resp);
            } else {
                Object user = request.getSession().getAttribute("user");
                if(user != null){
                    chain.doFilter(req, resp);
                } else {
                    request.setAttribute("login_msg", "请先登陆");
                    request.getRequestDispatcher("/login.jsp").forward(req, resp);
                }
            }
        }
    }
    ```

    

- 敏感词替换：

  - 因为 request 没有 setParameter 方法，所以对 request 域中的数据替换只能通过动态代理，增强 getParameter 方法实现。

  - 步骤：

    1. 在 `init()`方法中，读入txt文件中的敏感词，并存入一个成员变量集合中。
    2. 创建 ServletRequest 的动态代理。
    3. 判断方法名是否是 getParameter，是则搜索返回值中是否有敏感词，并进行替换。
    4. 放行过滤器。一定要主要此时的`chain.doFilter()`方法的第一个参数是代理对象。

  - 实现：

    ```java
    @WebFilter("/*")
    public class WordsFilter implements Filter {
        private List<String> list = new ArrayList<String>();
        
        public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
            ServletRequest proxy_req = (ServletRequest) Proxy.newProxyInstance(req.getClass().getClassLoader(), req.getClass().getInterfaces(), new InvocationHandler() {
                @Override
                public Object invoke(Object o, Method method, Object[] objects) throws Throwable {
                    if(method.getName().equals("getParameter")){
                        String value = (String)method.invoke(req, objects);
                        if(value != null){
                            for(String s : list){
                                if(value.contains(s)){
                                    value = value.replaceAll(s, "***");
                                }
                            }
                        }
                        return value;
                    }
                    return method.invoke(req, objects);
                }
            });
            chain.doFilter(proxy_req, resp);
        }
    
        public void init(FilterConfig config) throws ServletException {
            ServletContext servletContext = config.getServletContext();
            String realPath = servletContext.getRealPath("/WEB-INF/classes/wordsFilter.txt");
            System.out.println(realPath);
            try {
                BufferedReader br = new BufferedReader(new FileReader(realPath));
                String line = null;
                while((line = br.readLine()) != null){
                    list.add(line);
                }
                br.close();
                System.out.println(list);
            } catch (FileNotFoundException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
    ```

    



## 3、Listener

- 事件监听机制：

  - 事件。
  - 事件源：事件发生的源头。
  - 监听器：一个对象。
  - 注册监听：将事件源、监听器绑定在一起。当事件源上发生某个事件后，执行监听器代码。

- ServletContextListener 接口监听器：

  - `void contextInitalized(ServletContextEvent sce)`：ServletContext 对象被创建后会调用该方法。ServletContext 对象会在服务器启动后自动创建。
  - `void contextDestroyed(ServletContextEvent sce)`：ServletContext 对象被销毁前会调用该方法。ServletContext 对象会在服务器正常关闭后被销毁。

- 步骤：

  1. 定义一个类，实现 ServletContextListener 接口。

  2. 实现接口中的所有方法。

  3. 配置监听器。

     - web.xml 下。

       ```xml
       <listener>
       	<listener-class>全类名</listener-class>
       </listener>
       ```

     -  注解：加`@WebListener`在实现类上。

- 监听器的使用：加载全局资源文件。

  - 在 web.xml 中配置 context 参数：src 下的资源文件。

    ```xml
    <context-param>
        <param-name>资源名称</param-name>
        <param-value>WEB-INF/classes/资源文件</param-value>
    </context-param>
    ```

  - 监听器实现：加载全局资源文件。

    ```java
    @WebListener
    public class Listener implements ServletContextListener {
        @Override
        public void contextInitialized(ServletContextEvent servletContextEvent) {
            //获取ServletContext对象
            ServletContext servletContext = servletContextEvent.getServletContext();
            //获取资源文件参数
            String contextConfigLocation = servletContext.getInitParameter("contextConfigLocation");
            //获取真实路径
            String realPath = servletContext.getRealPath(contextConfigLocation);
            try {
                //加载进内存
                FileInputStream fis = new FileInputStream(realPath);
            } catch (FileNotFoundException e) {
                e.printStackTrace();
            }
        }
    }
    ```

    

