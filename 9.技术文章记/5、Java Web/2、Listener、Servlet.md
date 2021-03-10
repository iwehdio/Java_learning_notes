学习自：

- [Listener](https://zhuanlan.zhihu.com/p/65219353)
- [ContextLoaderListener解析](https://zhuanlan.zhihu.com/p/65258266)
- [Filter](https://zhuanlan.zhihu.com/p/65335657)
- [Servlet（上）](https://zhuanlan.zhihu.com/p/65588465)
- [Servlet（下）](https://zhuanlan.zhihu.com/p/65658315)
- [Java3y Sevlet](https://github.com/ZhongFuCheng3y/3y#dollarservlettomcat)
- [Java3y Listen、Filter](https://github.com/ZhongFuCheng3y/3y#microscope%E7%9B%91%E5%90%AC%E5%99%A8%E5%92%8C%E8%BF%87%E6%BB%A4%E5%99%A8)



## 1、Listener

- 常用的监听器：

  ```html
  
  6个常规监听器
      |---ServletContext
              |---ServletContextListener（生命周期监听）
              |---ServletContextAttributeListener（属性监听）
      |---HttpSession
              |---HttpSessionListener（生命周期监听）
              |---HttpSessionAttributeListener（属性监听）
      |---ServletRequest
              |---ServletRequestListener（生命周期监听）
              |---ServletRequestAttributeListener（属性监听）
  
  2个感知监听
      |---HttpSessionBindingListener
      |---HttpSessionActivationListener
       
  ```

  - 6个常规监听器，分属三类，分别对应JavaWeb三大域对象（服务、会话、请求）：ServletContext、HttpSession、ServletRequest。共三对，每一对都包括1个生命周期监听和1个属性监听。
  - 所谓生命周期监听器，就是监听三大域对象的创建和销毁。每当Tomcat创建或销毁三大域对象，都会被这些监听器察觉，然后它们会做相应的操作（调用自身的特定方法）。
  - 属性监听器则专门监听三大域对象get/setAttribute()。每当我们给域对象设置值或者从里面取值，都会被它们监听到，然后还是触发它们特定的方法。

  ![image-20210129115445888](http://iwehdio.gitee.io/img/my-img/21-01/image-20210129115445888.png)

- 三大生命周期监听器，各自在何时创建、销毁，顺序是怎么样的？

  1. **在项目启动时**ServletContextListener监听到ServletContext对象创建
  2. **每一次请求**Tomcat都会创建一个Request，它的创建会被ServletRequestListener监听到
  3. **如果**Servlet中调用了request.getSession()，则Tomcat会创建Session（如果根据JSESSIONID找不到对应的），这会被HttpSessionListener监听到
  4. 请求结束，Request销毁，被监听到
  5. 用户30分钟未访问，Session过期销毁，被监听到
  6. 项目关停，ServletContext销毁，被监听到

- 访问一个Servlet，HttpSessionListener一定会触发吗？（换个角度就是，访问Servlet，Session一定会创建吗？）

  - 只有当在Servlet中调用request.getSession()，且根据JSESSIONID找不到对于的Session时，才会创建新的Session对象，才会被监听到。
  - 第二次请求，浏览器会带上JSESSIONID，此时虽然还是request.getSession()，但是会返回上次那个。根据JSESSIONID去查找Session这个过程是隐式的，我们看到的就是getSession()。

- 三大属性监听何时触发？

  - get/setAttribute()时，会触发属性监听。

- 如何实现监听的？

  - 观察者模式。在被监听对象中注册监听器。被监听对象执行某些操作时，通知监听器执行相应的方法。

- ContextLoaderListener是一个由Spring编写并提供的监听器：

  - 我们搭建SSM框架时，需要做的仅仅是在web.xml中配置它。

  - ContextLoaderListener实现了ServletContextListener（三大生命周期监听之一）。

  - Spring实现了Tomcat提供的ServletContextListener接口，写了一个监听器来监听项目启动。一旦项目启动，会触发ContextLoaderListener中的特定方法。

    ![image-20210129120012585](http://iwehdio.gitee.io/img/my-img/21-01/image-20210129120012585.png)

  - Tomcat的ServletContext创建时，会调用ContextLoaderListener的contextInitialized()，这个方法内部的initWebApplicationContext()就是用来初始化Spring的IOC容器的。

    - ServletContext对象是Tomcat的
    - ServletContextListener是Tomcat提供的接口
    - ContextLoaderListener是Spring写的，实现了ServletContextListener
    - Spring自己写的监听器，用来创建Spring IOC

  - 怎么注册ContextLoaderListener的？

    - xml中配置了ContextLoaderListener
    - Tomcat会解析web.xml，反射创建ContextLoaderListener。

  - 创建IOC时，要ServletContext对象作什么？

    - 因为，最终IOC容器其实是存放在ServletContext对象（容器）中。

  - 初始化IOC的步骤：

    1. 传入servletContext对象创建空的IOC容器
    2. 以成员变量CONFIG_LOCATION_PARAM为key , 从servletContext对象中得到web.xml中配置的spring-context.xml文件位置
    3. 根据spring-context.xml初始化IOC容器
    4. 将IOC容器存入servletContext

    ![image-20210129121024222](http://iwehdio.gitee.io/img/my-img/21-01/image-20210129121024222.png)

- Filter的拦截方式：

  - REQUEST：当⽤户直接访问⻚⾯时，Web容器将会调⽤过滤器。如果⽬标资源是通过RequestDispatcher的include()或forward()⽅法访问时，那么该过滤器就不会被调⽤。
  - INCLUDE：如果⽬标资源是通过RequestDispatcher的include()⽅法访问时，那么该过滤器将被调⽤。除此之外，该过滤器不会被调⽤。
  - FORWARD：如果⽬标资源是通过RequestDispatcher的forward()⽅法访问时，那么该过滤器将被调⽤，除此之外，该过滤器不会被调⽤。
  - ERROR：如果⽬标资源是通过声明式异常处理机制调⽤时，那么该过滤器将被调⽤。除此之外，过滤器不会被调⽤。

- 监听器涉及三个组件：事件源，事件对象，事件监听器

  - 当事件源发⽣某个动作的时候，它会调⽤事件监听器的⽅法，并在调⽤事件监听器⽅法的时候把事件对象传递进去。
  - 我们在监听器中就可以通过事件对象获取得到事件源，从⽽对事件源进⾏操作。

  - 监听器定义为接⼝，监听的⽅法需要事件对象传递进来，从⽽在监听器上通过事件对象获取得到事件
    源，对事件源进⾏修改。
  - 事件源需要注册监听器(即在事件源上关联监听器对象)。如果触发了相关⽅法的时候，会调⽤监听器的⽅法，并将事件对象传递进去。
  - 事件对象封装了事件源。监听器可以从事件对象上获取得到事件源的对象(信息)。

- 在Servlet规范中定义了多种类型的监听器，它们⽤于监听的事件源分别 ServletContext,HttpSession和ServletRequest这三个域对象

  - 和其它事件监听器略有不同的是，servlet监听器的注册不是直接注册在事件源上，⽽是由WEB容器负
    责注册，开发⼈员只需在web.xml⽂件中使⽤`<listener>` 标签配置好监听器。
  - HttpSessionListener、ServletContextListener、ServletRequestListener分别监控着Session、
    Context、Request对象的创建和销毁。
  - ServletContextAttributeListener、HttpSessionAttributeListener、ServletRequestAttributeListener
    分别监听着Context、Session、Request对象属性的变化。

- 除了上⾯的6种Listener，还有两种Linstener监听Session内的对象，分别是HttpSessionBindingListener和HttpSessionActivationListener，实现这两个接⼝并不需要在web.xml⽂件中注册

  - 实现HttpSessionBindingListener接⼝，JavaBean 对象可以感知⾃⼰被绑定到 Session 中和从Session 中删除的事件【和HttpSessionAttributeListener的作⽤是差不多的】
  - 实现HttpSessionActivationListener接⼝，JavaBean 对象可以感知⾃⼰被活化和钝化的事件（当服务器关闭时，会将Session的内容保存在硬盘上【钝化】，当服务器开启时，会将Session的内容在硬盘式重新加载【活化】）





## 2、Servlet

- 什么是Servlet？
  - Servlet是**Ser**ver App**let**（运行在服务端的小程序）
  - Servlet其实就是⼀个遵循Servlet开发的java类。Servlet是由服务器调⽤的，运⾏在服务器端。
- 为什么要⽤到Servlet？
  
- 我们编写java程序想要在⽹上实现 聊天、发帖、这样⼀些的交互功能，普通的java技术是⾮常难完成的。sun公司就提供了Servlet这种技术供我们使⽤。
  
- Servlet的生命周期：
  - 加载Servlet。当Tomcat第⼀次访问Servlet的时候，Tomcat会负责创建Servlet的实例
  - 初始化。当Servlet被实例化后，Tomcat会调⽤init()⽅法初始化这个对象
  - 处理服务。当浏览器访问Servlet的时候，Servlet 会调⽤service()⽅法处理请求
  - 销毁。当Tomcat关闭时或者检测到Servlet要从Tomcat删除的时候会⾃动调⽤destroy()⽅法，让该
    实例释放掉所占的资源。⼀个Servlet如果⻓时间不被使⽤的话，也会被Tomcat⾃动销毁
  - 卸载。当Servlet调⽤完destroy()⽅法后，等待垃圾回收。如果有需要再次使⽤这个Servlet，会重
    新调⽤init()⽅法进⾏初始化操作。

- Tomcat其实是Web服务器和Servlet容器的结合体。

  - Web服务器的作用说穿了就是：将某个主机上的资源映射为一个URL供外界访问。
  - Servlet容器，顾名思义里面存放着Servlet对象。

- 为什么能通过Web服务器映射的URL访问资源？肯定需要写程序处理请求，主要3个过程：

  - 接收请求、处理请求、响应请求。
  - 其中接收请求和响应请求是共性功能，且没有差异性。访问淘宝和访问京东，都是接收[www.taobao.com/brandNo=1](https://link.zhihu.com/?target=http%3A//www.taobao.com/brandNo%3D1)，响应给浏览器的都是JSON数据。于是，大家就把接收和响应两个步骤抽取成Web服务器。
  - 但处理请求的逻辑是不同的。没关系，抽取出来做成Servlet，交给程序员自己编写。
  - 随着后期互联网发展，出现了三层架构，所以一些逻辑就从Servlet抽取出来，分担到Service和Dao。

  ![image-20210122193430451](http://iwehdio.gitee.io/img/my-img/21-01/image-20210122193430451.png)

- Java Web三大组件：

  - Servlet/Filter/Listener的使用。没有main，也没有new，写一个类然后在web.xml中配个标签，它们就这么兀自运行了。

  - 实现的原理简单来说就是“注入”和“回调”。想象一下，Tomcat里有个main方法，假设是这样的：

    ![image-20210122193851991](http://iwehdio.gitee.io/img/my-img/21-01/image-20210122193851991.png)

  ![image-20210122194017111](http://iwehdio.gitee.io/img/my-img/21-01/image-20210122194017111.png)

- Servlet虽然是个接口，但实现类只是个空壳，我们写点业务逻辑就好了。Tomcat往Servlet接口中传入三个对象：ServletConfig、ServletRequest、ServletResponse。

- ServletConfig：

  - servletConfig对象封装了servlet的一些参数信息。如果需要，我们可以从它获取。

  ![image-20210122194308294](http://iwehdio.gitee.io/img/my-img/21-01/image-20210122194308294.png)

- Request/Response：

  - HTTP请求到了Tomcat后，Tomcat通过字符串解析，把各个请求头（Header），请求地址（URL），请求参数（QueryString）都封装进了Request对象中。
  - 至于Response，Tomcat传给Servlet时，它还是空的对象。Servlet逻辑处理后得到结果，最终通过response.write()方法，将结果写入response内部的缓冲区。Tomcat会在servlet处理结束后，拿到response，遍历里面的信息，组装成HTTP响应发给客户端。

  ![image-20210122194538002](http://iwehdio.gitee.io/img/my-img/21-01/image-20210122194538002.png)

  - Servlet接口5个方法，其中init、service、destroy是生命周期方法。init和destroy各自只执行一次，即servlet创建和销毁时。而service会在每次有新请求到来时被调用。也就是说，我们主要的业务代码需要写在service中。

- GenericServlet抽象类实现了javax.servlet接口：

  ![image-20210122194723277](http://iwehdio.gitee.io/img/my-img/21-01/image-20210122194723277.png)

  - 提升了init方法中原本是形参的servletConfig对象的作用域（成员变量），方便其他方法使用
  - init方法中还调用了一个init空参方法，如果我们希望在servlet创建时做一些什么初始化操作，可以继承GenericServlet后，覆盖init空参方法
  - 由于其他方法内也可以使用servletConfig，于是写了一个getServletContext方法

- 抽象类HttpServlet实现了javax.servlet接口：

  ![](http://iwehdio.gitee.io/img/my-img/21-01/v2-73b703e690ce018ffe88280376a67dc0_r.png)

  - HttpServlet的service方法已经替我们完成了复杂的请求方法判断。但是针对每一种请求，业务逻辑代码是不同的，HttpServlet无法知晓子类想干嘛，所以就抽出七个方法，并且提供了默认实现：报405、400错误，提示请求不支持。
  - 一个类声明成抽象方法，一般有两个原因：
    - 有抽象方法
    - 没有抽象方法，但是不希望被实例化
  - HttpServlet做成抽象类，仅仅是为了不让new。要求子类重写doGet、doPost等方法。

  ![image-20210122195214111](http://iwehdio.gitee.io/img/my-img/21-01/image-20210122195214111.png)

  - 如果没有重写，浏览器页面会显示：405（http.method_get_not_supported）。

  ![image-20210122195235885](http://iwehdio.gitee.io/img/my-img/21-01/image-20210122195235885.png)

  

- ServletContext，直译的话叫做“Servlet上下文”。它其实就是个大容器，是个map。服务器会为每个应用创建一个ServletContext对象：

  - ServletContext对象的创建是在服务器启动时完成的
  - ServletContext对象的销毁是在服务器关闭时完成的

  ![image-20210129111749009](http://iwehdio.gitee.io/img/my-img/21-01/image-20210129111749009.png)

  - ServletContext对象的作用是在整个Web应用的动态资源（Servlet/JSP）之间共享数据。例如在AServlet中向ServletContext对象保存一个值，然后在BServlet中就可以获取这个值。
  - 这种用来装载共享数据的对象，在JavaWeb中共有4个，而且更习惯被成为“域对象”：
    - ServletContext域（Servlet间共享数据）
    - Session域（一次会话间共享数据，也可以理解为多次请求间共享数据）
    - Request域（同一次请求共享数据）
    - Page域（JSP页面内共享数据）
  - 它们都可以看做是map，都有getAttribute()/setAttribute()方法。
  - 每一个动态web工程，都应该在WEB-INF下创建一个web.xml，它代表当前整个应用。Tomcat会根据这个配置文件创建ServletContext对象。

- 获取ServletContext的方法共5种：

  - ServletConfig#getServletContext();
  - GenericServlet#getServletContext();
  - HttpSession#getServletContext();
  - HttpServletRequest#getServletContext();
  - ServletContextEvent#getServletContext()

- 配置Filter时可以设置4种拦截方式：

  ![image-20210129112433964](http://iwehdio.gitee.io/img/my-img/21-01/image-20210129112433964.png)

  - 重定向和REQUEST/FORWARD/INCLUDE/ERROR最大区别在于：
    - 重定向会导致浏览器发送**2**次请求，FORWARD们是服务器内部的**1**次请求
  - 因为FORWARD/INCLUDE等请求的分发是服务器内部的流程，不涉及浏览器。
  - ServletContext拥有分发器Dispatcher，用于分发请求：REQUEST/FORWARD/INCLUDE/ERROR。REQUEST是浏览器发起的，而ERROR是发生页面错误时发生的，稍微特殊些。

  ![image-20210129112630294](http://iwehdio.gitee.io/img/my-img/21-01/image-20210129112630294.png)

  - 最外层那个圈，可以理解成ServletContext，FORWARD/INCLUDE这些都是内部请求。如果在web.xml中配置Filter时4种拦截方式全配上，那么服务器内部的分发跳转都会被过滤。

- 每一个URL要交给哪个Servlet处理，具体的映射规则都由一个映射器决定：

  ![image-20210129112758524](http://iwehdio.gitee.io/img/my-img/21-01/image-20210129112758524.png)

  - 所谓的映射器，其实就是Tomcat中一个叫Mapper的类。它里面有个internalMapWrapper方法：

    ![image-20210129112923007](http://iwehdio.gitee.io/img/my-img/21-01/image-20210129112923007.png)

  - 定义了映射规则：

    1. 对于静态资源，Tomcat最后会交由一个叫做DefaultServlet的类来处理
    2. 对于Servlet ，Tomcat最后会交由一个叫做 InvokerServlet的类来处理
    3. 对于JSP，Tomcat最后会交由一个叫做JspServlet的类来处理

  ![image-20210129113052123](http://iwehdio.gitee.io/img/my-img/21-01/image-20210129113052123.png)

  - 如果一个Servlet的拦截路径被设置为`/*`，表示拦截所有，相当于把DefaultServlet、JspServlet以及我们自己写的其他Servlet都“短路”了，它们都失效了。

  - 如果一个Servlet的拦截路径被设置为`/`，截所有，但不包括JSP。虽然JSP不拦截了，但是静态资源还是不会被拦截。针对这种情况，SpringMVC提供了一个标签，解决上面/无法读取静态资源的问题：

    ```xml
    <!-- 静态资源处理  css js imgs -->
    <mvc:resources location="/resources/**" mapping="/resources"/>
    ```

- conf/web.xml指的是Tomcat全局配置web.xml。

  - 它里面配置了两个Servlet：JspServlet的映射路径为`*.jsp`和DefaultServlet的映射路径为`/`。
  - conf/web.xml中的配置相当于写在了每一个应用的web.xml中。即每个应用默认都配置了JSPServlet和DefaultServlet处理JSP和静态资源。
  - 如果我们在应用的web.xml中为DispatcherServlet配置`/`，会和DefaultServlet产生路径冲突，从而覆盖DefaultServlet。此时，所有对静态资源的请求，映射器都会分发给我们自己写的DispatcherServlet处理。遗憾的是，它只写了业务代码，并不能IO读取并返回静态资源。JspServlet的映射路径没有被覆盖，所以动态资源照常响应。
  - 如果我们在应用的web.xml中为DispatcherServlet配置`/*`，虽然JspServlet和DefaultServlet拦截路径还是.jsp和/，没有被覆盖，但无奈的是在到达它们之前，请求已经被DispatcherServlet抢去，所以最终不仅无法处理JSP，也无法处理静态资源。

- Servlet的细节：

  - ⼀个已经注册的Servlet可以被多次映射。同⼀个Servlet可以被映射到多个URL上。
  - Servlet映射的URL可以使⽤通配符，比如`*.jsp`或`/*`。

  - 为什么Servlet是单例的？
    - 浏览器多次对Servlet的请求，⼀般情况下，服务器只创建⼀个Servlet对象，也就是说，Servlet对象⼀旦创建了，就会驻留在内存中，为后续的请求做服务，直到服务器关闭。
  - 每次访问请求对象和响应对象都是新的：
    - 对于每次访问请求，Servlet引擎都会创建⼀个新的HttpServletRequest请求对象和⼀个新的
      HttpServletResponse响应对象，然后将这两个对象作为参数传递给它调⽤的Servlet的service()⽅
      法，service⽅法再根据请求⽅式分别调⽤doXXX⽅法。
  - 线程安全问题：
    - 当多个⽤户访问Servlet的时候，服务器会为每个⽤户创建⼀个线程。当多个⽤户并发访问Servlet共享资源的时候就会出现线程安全问题。
  - load-on-startup：
    - 如果在`<servlet> `元素中配置了⼀个`<load-on-startup>` 元素，那么WEB应⽤程序在启动时，就会装载并创建Servlet的实例对象、以及调⽤Servlet实例对象的init()⽅法。

- 读取资源文件：

  - 资源文件在类目录src下：

    - 通过ServletContext对象获取：

      ```java
      //获取到ServletContext对象
      ServletContext servletContext = this.getServletContext();
      //调⽤ServletContext⽅法获取到读取⽂件的流
      InputStream inputStream = servletContext.getResourceAsStream("/WEBINF/
      classes/zhongfucheng/web/1.png");
      ```

    - 通过类加载器获取：

      ```java
      //获取到类装载器
      ClassLoader classLoader = Servlet111.class.getClassLoader();
      //通过类装载器获取到读取⽂件流
      InputStream inputStream =
      classLoader.getResourceAsStream("/zhongfucheng/web/1.png");
      ```

    - ：如果⽂件太⼤，就不能⽤类装载器的⽅式去读取，会导致内存溢出。

  - 资源文件在web目录下：

    - 通过ServletContext对象获取：

      ```java
      //获取到ServletContext对象
      ServletContext servletContext = this.getServletContext();
      //调⽤ServletContext⽅法获取到读取⽂件的流
      InputStream inputStream = servletContext.getResourceAsStream("2.png");
      ```

- HttpServletRequest常⽤⽅法

  - 获得客户机【浏览器】信息
    - getRequestURL⽅法返回客户端发出请求时的完整URL。
    - getRequestURI⽅法返回请求⾏中的资源名部分。
    - getQueryString ⽅法返回请求⾏中的参数部分。
    - getPathInfo⽅法返回请求URL中的额外路径信息。额外路径信息是请求URL中的位于Servlet的路径之后和查询参数之前的内容，它以“/”开头。
    - getRemoteAddr⽅法返回发出请求的客户机的IP地址
    - getRemoteHost⽅法返回发出请求的客户机的完整主机名
    - getRemotePort⽅法返回客户机所使⽤的⽹络端⼝号
    - getLocalAddr⽅法返回WEB服务器的IP地址。
    - getLocalName⽅法返回WEB服务器的主机名
  - 获得客户机请求头
    - getHeader⽅法
    - getHeaders⽅法
    - getHeaderNames⽅法
  - 获得客户机请求参数(客户端提交的数据)
    - getParameter⽅法
    - getParameterValues（String name）⽅法
    - getParameterNames⽅法
    - getParameterMap⽅法

- 转发和重定向的区别：

  - 实际发⽣位置不同，地址栏不同。
    - 转发是发⽣在服务器的。浏览器的地址栏是没有发⽣变化的，浏览器是不知道该跳转的动作，转发是对浏览器透明的。⼀次转发中request和
      response对象都是同⼀个。
    - 重定向是发⽣在浏览器的。浏览器的地址会发⽣变化的，重定向会发出两个http请求，request域对象是⽆效的。
  - ⽤法不同。
    - 资源地址给服务器⽤的直接从资源名开始写，给浏览器⽤的要把应⽤名写上。
    - `request.getRequestDispatcher("/资源名 URI").forward(request,response)`：
      转发时"/"代表的是本应⽤程序的根⽬录【zhongfucheng】
    - `response.send("/web应⽤/资源名 URI")`：
      重定向时"/"代表的是webapps⽬录
  - 能够去往的URL的范围不⼀样
    - 转发是服务器跳转只能去往当前web应⽤的资源
    - 重定向是服务器跳转，可以去往任何的资源
  - 传递数据的类型不同
    - 转发的request对象可以传递各种类型的数据，包括对象
    - 重定向只能传递字符串
  - 跳转的时间不同
    - 转发时：执⾏到跳转语句时就会⽴刻跳转
    - 重定向：整个⻚⾯执⾏完之后才执⾏跳转













