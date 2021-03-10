# 37、Tomcat和Servlet基础

## 1、Web基础概念回顾

- 软件架构：
  - C/S：客户端/服务器端。
  - B/S：浏览器/服务器端。
- 资源分类：
  - 静态资源：所有用户访问后得到的记过都是一样的。
  - 动态资源：每个用户访问访问相同资源后得到的结果可能不一样。
  - 静态资源可以直接被浏览器解析，动态资源被访问需要先被转换为静态资源，再返回浏览器。
- 网络通信三要素：
  - IP地址：设备再网络中的唯一标识。
  - 端口：应用程序再计算机中的唯一标识。
  - 传输协议：规定了数据传输的规则。
- 服务器软件：接受用户的请求，处理请求并作出响应。
- 服务器：安装了服务器软件的计算机。
- Web服务器软件：可以部署Web项目，让用户通过浏览器来访问。动态资源必须在Web服务器中运行，也叫Web容器。

## 2、Tomcat

- 启动Tomcat：
  - bin  目录下的 start.bat 。
  - 浏览器访问：`ip地址:端口号`。端口号默认8080。
  - 启动时出现的问题：
    - 命令行黑窗口一闪而过：JAVA_HOME环境变量未正确配置。
    - 启动报错：端口号已被占用。查看占用端口号的进程的PID：命令行下输入`netstat -ano`；或更改 conf 下的 server.xml 的Connector端口号，一般更改为80。
- 关闭Tomcat：
  - bin 目录下的 shutdown.bat。
  - 在 Tomcat 命令行中 ctrl+c 。
- 部署项目的方式：
  1. 直接将项目放到 webapps 目录下。
     - 访问方式：`ip:端口号/项目名称/资源名称`。/项目名称是项目的访问路径，称为虚拟目录。
     - 简化部署：将项目打包为 .war 压缩包，再将其 放置到 webapps 下，自动解压缩。
  2.  在其他目录下通过配置文件部署。
     - 配置方式： conf 下的 server.xml 的 host 标签体中配置：`<Context docBase="/项目路径" path="/虚拟目录" />`。
  3.  为每个项目单一热配置
     - 配置方式：conf/Catalina/localhost 下创建 文件名.xml文件。
     - 在文件中编写 `<Context docBase="/项目路径" />`。
     - 虚拟目录就是 xml 文件的名称。
- Java动态项目的目录结构：
  - 根目录下有 WEB-INF 目录。该目录下有：
  - web.xml ：web项目的核心配置文件。
  - classes 目录：放置字节码文件的目录。
  - lib 目录：放置依赖 jar 包。
- IDEA集成：
  - 导入：Run 下的 Edit Configurations 。选择 Tomcat Server，配置Tomcat的目录。
  - 创建项目：创建Java Enterprise 项目，勾选 Web Application。
  - 热部署：将 Edit Configurations 下的 Tomcat Server中的，On Update action 和 On frame deactivation 置为 Update resources。

## 3、Servlet

> serber applet：运行在服务器端的小程序。
>
> Servlet就是一个接口，定义了Java类被浏览器访问到（用Tomcat识别）的规则（接口）。

- 使用方法：自定义一个类，实现 Servlet 接口，复写方法。

- 使用步骤：

  1. 创建 JavaEE 项目。

  2. 定义一个类，实现 Servlet 接口。

  3. 实现接口中的所有抽象方法。

     ```java
     public class ServletDemo1 implements Servlet {
         @Override
         public void init(ServletConfig servletConfig) throws ServletException {
         }
         @Override
         public ServletConfig getServletConfig() {
             return null;
         }
         @Override
         public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
         }
         @Override
         public String getServletInfo() {
             return null;
         }
         @Override
         public void destroy() {
         }
     }
     ```

  4. 配置 Servlet 。在 web.xml 配置文件中。

     ```xml
     <servlet>
         <servlet-name>Servlet名</servlet-name>
         <servlet_class>全类名</servlet_class>
     </servlet>
     
     <servlet-mapping>
     	<servlet-name>Servlet名</servlet-name>
         <url-pattern>子路径</url-pattern>
     </servlet-mapping>
     ```

     - servlet 标签中，将 Servlet名 与全类名捆绑，全类名即各级包名.类名。
     - servlet-mapping 标签中，将 Servlet名 与所要映射子路径捆绑。

- Servlet 执行原理：

  1. 当服务器接收到客户端浏览器的请求后，会解析 url 路径，获取所要访问的 Servlet 资源路径。
  2. 查找 web.xml 文件，是否有对应的 `<url-pattern>` 标签体内容。
  3. 如果有，通过 Servlet 名找到对应的`<servlet_class>`全类名。
  4. Tomcat 将全类名对应的类加载进内存，并创建对象。
  5. 调用其方法。

- Servlet 的方法与生命周期：

  - `init()`：初始化方法，在 Servlet 被创建时执行，只会执行一次。
    - 默认情况下，第一次被访问时，Servlet 被创建。
    - 可以指定 Servlet 被创建的时机，`<load-on-startup></load-on-startup>`。为负数时表示第一次被访问时创建，为0或正整数时为在服务器启动时创建。
    - 一个 Servlet 在内存中只存在一个对象，Servlet 是单例的。多个用户同时访问时，可能出现线程安全问题。尽量不要在 Servlet 中定义成员变量，即使定义了也不要对其进行修改。
  - `service()`：提供服务的方法，每次 Servlet 被访问时都会被执行一次。
  - `destroy()`：销毁方法，在服务器正常关闭时执行，只会执行一次。
    - 在 Servlet 被销毁前执行，用于释放资源。
  - `ServletConfig()`：获取 Servlet 的配置对象 ServletConfig对象。
  - `getServletInfo()`：获取 Servlet 的一些信息。

- Servlet 3.0 注解配置：可以不在 web.xml 中配置。

  - 创建 JavaEE 项目时可以不创建 web.xml 。
  - 在类上使用 `@WebServlet(urlPatterns="/子路径")`注解进行配置。可以简写为`@WebServlet("/子路径")`。

- IDEA 与 Tomcat 配置：

  - IDEA 会为每个 Tomcat 部署的项目单独建立一份配置文件。
  - 工作空间项目和 Tomcat 部署的 web 项目的内容相同，但是存储空间不同。Tomcat 真正访问的是Tomcat 部署的 web 项目，对应于工作空间项目下的所有资源。
  - WEB-INF 目录下的资源不能直接被浏览器访问。
  
- Servlet 体系结构：

  - `GenericServlet`抽象类：是 Servlet 接口的实现，对除了 service() 方法作为抽象，需要在子类中重写。如果继承该类，只需要实现 service() 方法。
  - `HttpServlet`抽象类：是 GenericServlet 抽象类的子类，是对 http 协议的一种封装。需要复写 doGet() 和 doPost() 方法，分别用于不同的请求方式。

- urlpartten 配置：
  - 一个 Servlet 可以定义多个访问路径，在注解上的配置方式为：`@WebServlet({"/路径1","/路径2"})`。
  - 路径配置规则：
    - 单层路径：`/xxx` 。
    - 多层路径：`/xxx/xxx `。（\*是通配符，/\*表示任意路径）
    - 扩展名匹配：`*.扩展名`。（即任意路径 . 扩展名可访问到）

