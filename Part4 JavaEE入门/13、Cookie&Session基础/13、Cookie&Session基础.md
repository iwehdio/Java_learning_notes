## 1、 Cookie 会话技术

> 一次会话：浏览器第一次给服务器资源发送请求，会话建立；直到有一方断开为止。一次会话中包含多次请求和响应。

- 会话的功能：在一次会话的范围内的多次请求间，共享资源。

- 方式（数据存储在哪）：

  1. 客户端会话技术：Cookie。
  2. 服务器端会话技术：Session。

- Cookie 是客户端会话技术，将数据保存在客户端。服务器端创建 Cookie 并通过响应发送到客户端，客户端在下一次发送请求时同时发送 Cookie 。

- 使用 Cookie 对象的步骤：

  1. 服务器端创建 Cookie 对象，绑定数据。
     - `new Cookie(String name, String value)`。
  2. 服务器端发送 Cookie 对象。
     - `response.addCookie(Cookie cookie)`。
  3. 服务器端获取 Cookie 对象。
     - `Cookie[] request.getCookies()`。

  - 简单实现：

    ```java
    @WebServlet("/Cookie1")
    public class Cookie1 extends HttpServlet {
        protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            //创建Cookie
            Cookie c = new Cookie("msg", "hello");
            //发送Cookie
            response.addCookie(c);
        }
        protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            this.doPost(request, response);
        }
    }
    
    @WebServlet("/Cookie2")
    public class Cookie2 extends HttpServlet {
        protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            //获取Cookie并遍历
            Cookie[] cs = request.getCookies();
            if(cs != null){
                for(Cookie cc : cs){
                    String name = cc.getName();
                    String value = cc.getValue();
                    System.out.println(name + "_" + value);
                }
            }
        }
        protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            this.doPost(request, response);
        }
    }
    ```

- Cookie 原理分析：

  - 第一次响应，服务器端使用响应头中的 set-cookie 头发送 Cookie。
  - 第二次请求，浏览器使用请求头中的 cookie 头发送 Cookie。

- Cookie 的相关问题：

  - 一次能否发送多个 Cookie ？
    - 一次可以发送多个 Cookie，只需创建多个对象。
    - 实质上的同一个响应头 / 请求头下用逗号分隔的多个键值对。
  - Cookie 在浏览器中保存多长时间。
    - 默认情况下，当浏览器关闭后，Cookie数据被销毁。
    - 也可以设置 Cookie 的生命周期，使其持久化存储。
    - `setMaxAge(int seconds)`：传入正数表示将Cookie数据写到硬盘的文件中，数字表示cookie的存活时间；传入负数表示默认状态；传入零表示删除 Cookie 信息。
  - Cookie 能否存储中文数据？
    - 在 Tomcat8 之后才支持存储中文数据，但仍不支持特殊字符。
    - 如果在 Tomcat8 之前，需要将中文数据转码，一般使用 URL 编码。
    - `URLEncoder.encode(String s, String charset)`编码。
    - `URLDecoder.decode(String s, String charset)`解码。
  - Cookie 的共享：
    - 默认情况下，在 Tomcat 服务器中部署的多个项目之间，Cookie 不能共享。
    - `setPath(String path)`：设置 Cookie 的获取范围，默认为当前的虚拟目录。设置为 "/" 表示设置为当前服务器的根目录，可以在同一个服务器下的多个项目间共享。
    - 不同的 Tomcat 服务器间，通过`setDomain(String path)`设置一级域名相同，那么多个服务器间 Cookie 可以共享。如传入 .baidu.com 那么 tieba.baidu.com 和 news.baidu.com 可以共享。

- Cookie 的特点 ：

  - Cookie 存储数据在客户端浏览器，安全性一般。
  - 浏览器对单个 Cookie 的大小（一般为4KB）和对同一个域名下的总 Cookie 数量也有限制（一般为20个）。

- Cookie 的作用：

  - 一般用于存储少量的、不太注重安全性的数据。
  - 用于在不登录的情况下，完成服务器对客户端的身份识别。

- 案例：记住上一次访问时间。

  - 步骤：

    1. 判断是否有 lasttime 的 Cookie ，根据结果进行响应。
    2. 更新时间写回 Cookie 。

  - 实现：

    ```java
    @WebServlet("/CookieTest")
    public class CookieTest extends HttpServlet {
        protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            //设置编码
            response.setContentType(("text/html;charset=utf-8"));
            boolean flag = false;
            Cookie[] cs = request.getCookies();
            if(cs != null && cs.length > 0){
                for(Cookie c : cs){
                    String name = c.getName();
                    if("lasttime".equals(name)){
                        //不是首次访问
                        flag = true;
                        //显示上次访问
                        String value = c.getValue();
                        value = URLDecoder.decode(value, "utf-8");
                        response.getWriter().write("上次访问" + value);
                        //更新时间
                        Date date = new Date();
                        SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
                        String str_date = sdf.format(date);
                        str_date = URLEncoder.encode(str_date, "utf-8");
                        c.setValue(str_date);
                        c.setMaxAge(60 * 60 * 24 * 30);
                        response.addCookie(c);
                        break;
                    }
                }
            }
            if(cs == null || cs.length == 0 || !flag) {
                //首次访问
                Date date = new Date();
                SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
                String str_date = sdf.format(date);
                str_date = URLEncoder.encode(str_date, "utf-8");
                Cookie c = new Cookie("lasttime", str_date);
                c.setMaxAge(60 * 60 * 24 * 30);
                response.addCookie(c);
                response.getWriter().write("首次访问");
            }
        }
        protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            this.doPost(request, response);
        }
    }
    ```



## 2、JSP概述

> Java Server Pages：Java服务器端页面。既可以定义 html 标签，也可以定义 Java 代码。用于简化书写。

- 访问 JSP 的原理：
  1. 浏览器请求服务器 .jsp 页面。
  2. 服务器解析请求消息，查看是否有这个 .jsp 资源。
  3. 将 .jsp 页面转换为 .java 文件。
  4. 编译 .java 文件为 .class 字节码文件。
  5. 由字节码文件提供访问，JSP 实际上就是一个 Servlet 。
- JSP 脚本：JSP 中定义 Java 代码的方式。
  - `<% 代码内容 %>`：定义了 service 方法中的 Java 代码。
  - `<!% 代码内容 %>`：定义了JSP转换后，类中的成员变量或成员方法的 Java 代码。
  - `<=% 代码内容 %>`：将内容输出到页面上。
- JSP 内置对象：
  - 在 JSP 页面中不需要创建和获取，可以直接获取的对象。
  - 共 9 个，包括 request 、response 和 out（字符输出流，输出到页面） 等。
  - response.getWriter().write() 的输出总会先于 out.write() 输出。

## 3、Session

> 服务器端会话技术，将数据保存在服务器端对象中。

- HttpSession 对象的方法：

  - `Object getAttribute(String name)`：获取数据。
  - `void setAttribute(String name, Object value)`：设置数据。
  - `void removeAttribute(String name)`：移除数据。

- 使用 HttpSession 对象的步骤：

  1. 在第一个 Servlet 中获取 Session 。
     - `HttpSession session = request.getSession()`。
  2. 在第一个 Servlet 中存储数据。
     - `session.setAttribute("name", "value")`。
  3. 在第二个 Servlet 中获取 Session 。
  4. 在第二个 Servlet 中获取数据。
     - `session.getAttribute("name")`。

- Session 原理：

  - 服务器确保在一次会话范围内，多次获取的 Session 是同一个。
  - Session 的实现实际上是依赖于 Cookie 的。
  - 第一次获取 Session 时，在内存中创建一个新的 Session 。创建后，服务器会向浏览器响应 Cookie ，内容为 Session 的 ID 即 JSESSIONID 。
  - 之后在同一次会话中，浏览器都会携带相同的  JSESSIONID 请求服务器，这样服务器每次都获取同一个 Session 。

- Session 的相关问题：

  - 客户端关闭后，服务器不关闭，两次获取 Session 默认情况下不是同一个。

    - 如果要在客户端关闭后， 获取的 Session 也相同，可以手动创建 Cookie 。

    - 实现：

      ```java
      //创建 Cookie ，内容为 Session 的 id
      Cookie c = new Cookie("JSESSIONID", session.getId);
      //设置 Cookie 的最大存活时间
      c.setMaxAge(60*60);
      //响应 Cookie
      response.addCookie(c);
      ```

  - 客户端不关闭，服务器关闭，两次获取 Session 默认情况下不是同一个。

    - 获取的不是一个对象，但保证数据不丢失，即对应的 Session id 不变。
    - Session 的钝化：在服务器正常关闭之后，将 Session 对象序列化存储到硬盘上。
    - Session 的活化：在服务器启动后，将 Session 文件转化为内存中的 Session 对象。
    - 直接使用 Tomcat 部署，Session 存储在 项目的 work 目录下，再次启动时自动活化。IDEA 启动项目时会将 work 目录删除，因此无法完成活化。

  - Session 被销毁。

    - 服务器被关闭。
    - Session 对象调用 `invalidate()` 方法。
    - 默认失效时间30分钟。可以在 web.xml 下的 session-config 标签下配置。

- Session 的特点：

  - 用于存储一次会话的多次请求的数据，存在服务器端。
  - 可以存储任意类型、任意大小的数据。
  - 相较于 Cookie 数据较为安全。

- 案例：用户登录。

  - 步骤：

    1. 编写页面，生成验证码并存储到 Session。
    2. 提交表单，获取 request 和 Session 参数。
    3. 先比较验证码，再比较用户名密码。全部正确则重定向到登录成功。有一个不正确则转发到登录页面。

  - 实现：

    - login.jsp ：

      ```jsp
      <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      <html>
      <head>
          <title>Login</title>
          <script>
              //点击更换验证码
              window.onload = function () {
                  document.getElementById("img").onclick = function () {
                      this.src="/Session/Check?time=" +  new Date().getDate();
                  }
              }
          </script>
      </head>
      <body>
          <form action="/Session/Session1" method="post">
              <table>
                  <tr>
                      <td>登录</td>
                      <td><input type="text" name="username" ></td>
                  </tr>
                  <tr>
                      <td>密码</td>
                      <td><input type="password" name="password" ></td>
                  </tr>
                  <tr>
                      <td>验证码</td>
                      <td><input type="text" name="checkcode" ></td>
                  </tr>
                  <tr>
                      <td colspan="2"><img src="/Session/Check" id="img" ></td>
                  </tr>
                  <tr>
                      <td colspan="2"><input type="submit" value="登录"></td>
                  </tr>
              </table>
          </form>
          <div><%= request.getAttribute("login_error")==null? "" : request.getAttribute("login_error") %></div>
          <div><%= request.getAttribute("checkcode_error")==null? "" : request.getAttribute("checkcode_error") %></div>
      </body>
      </html>
      ```

    - Session.java :

      ```java
      @WebServlet("/Session1")
      public class Session1 extends HttpServlet {
          protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
              //获取 request 参数 （多参数应使用 Map 获取）
              request.setCharacterEncoding("utf-8");
              String username = request.getParameter("username");
              String password = request.getParameter("password");
              String checkcode = request.getParameter("checkcode");
              //获取 Session 并设置存储验证
              HttpSession session = request.getSession();
              String session_checkcode = (String) session.getAttribute("checkcode_session");
              session.removeAttribute("checkcode_session");
              //先比较验证码（不区分大小写）
              if(session_checkcode!=null && session_checkcode.equalsIgnoreCase(checkcode)){
                  //如果验证码正确，比较用户名密码 （具体应使用数据库）
                  if("uadmin".equals(username) && "padmin".equals(password)){
                      //如果用户名密码正确，重定向到 success.jsp
                      session.setAttribute("username", username);
                      response.sendRedirect(request.getContextPath() + "/success.jsp");
                  } else {
                      //如果用户名密码错误，转发到 login.jsp
                      request.setAttribute("login_error", "用户名或密码错误");
                      request.getRequestDispatcher("/login.jsp").forward(request, response);
                  }
              } else {
                  //如果验证码错误，转发到 login.jsp
                  request.setAttribute("checkcode_error", "验证码错误");
                  request.getRequestDispatcher("/login.jsp").forward(request, response);
              }
          }
          protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
              this.doPost(request, response);
          }
      }
      ```

    - Check.java : 其他同 response 中的验证码，只添加存储到 Session 中。

      ```java
      StringBuilder sb = new StringBuilder();
      for (int i = 1; i <= 4; i++) {
          int index = ran.nextInt(str.length());
          //获取字符
          char ch = str.charAt(index);//随机字符
          sb.append(ch);
          //写验证码
          g.drawString(ch+"",width/5*i,height/2);
      }
      String check_code_session = sb.toString();
      request.getSession().setAttribute("checkcode_session", check_code_session);
      ```

    - success.jsp :

      ```jsp
      <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      <html>
      <head>
          <title>success</title>
      </head>
      <body>
          <h1><%= request.getSession().getAttribute("username") %></h1>
      </body>
      </html>
      ```

      

