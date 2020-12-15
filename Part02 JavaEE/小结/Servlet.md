## 1、Request

- 获取请求行数据：
  - `String getContextPath()`：获取虚拟目录。
  - `String getServletPath()`：获取 Servlet 路径。
  - `String getRequestURI()`：获取请求 uri ，即虚拟目录+ Servlet 路径。
  - `String getRequestURL()`：获取请求 url ，即完整路径。
- 获取请求参数：
  - `String getPatameter(String name)`：根据参数名称获取参数值。
  - `Map<String, String[]> getParameterMap()`：获取所有参数的 map 集合。
- 请求转发：一种在服务器内部的资源跳转方式。
  - 步骤：
    1. 通过 request 对象获取请求转发器对象：`RequestDispatcher getRequsetDispatcher (Stirng path)`。
    2. 使用请求转发器对象进行转发：`void forward(ServletRequest request, ServletResponse response)`。
  - 特点：
    1. 浏览器地址栏路径不发生变化。
    2. 只能转发到当前服务器内部资源中。
    3. 转发是一次请求。
- 共享数据。
  - 域对象：一个有作用范围的对象，可以在范围内共享数据。
  - request 的域：一次请求的范围，一般用于请求转发的多个资源共享数据。
  - `void setAttribute(String name, Object obj)`：存储数据。
  - `Object get Attribute(String name)`：通过键获取值。
  - `void removeAttribute(String name)`：通过键移除键值对。

## 2、Response

- 获取输出流：
  - 字节输出流：`ServletOutputStream getOutputStream()`。
  - 字符输出流：`PrintWriter getWriter()`。
- 重定向。

  - 一种资源跳转方式，浏览器访问A资源，A资源要求浏览器访问B资源实现。
  - 步骤：

    1. 指定重定向的状态码302，告知客户端浏览器。
    2. 设置响应头指定跳转资源。
  - `void response.sendRedirect(重定向路径)`。
  - 重定向的特点：

    - 地址栏发生变化。
    - 重定向可以访问其他服务器的资源。
    - 重定向是两次请求，不能用 request 对象共享数据。
    - 对比转发的特点：地址栏路径不变、只能访问当前服务器下的资源、是一次请求。
    - 对比而言，转发是由第一次访问的 Servlet 执行的，重定向是在访问第一个 Servlet 后，客户端浏览器器根据响应访问第二个 Servlet 。
- ServletConntext：
  - 代表整个web应用，可以与程序的容器（服务器）通信。
  - 获取 MIME 类型。
    - `String getMimeType(String filename)`：传入文件名，返回 MIME 类型。
  - 是一个域对象，可用于共享数据。
    - `setAttribute(String name, Object value)`：设置保存数据。
    - `getAttribute(String name)`：获取数据。
    - `removeAttribute(String name)`：删除数据。
    - 作用范围，可以获取所有用户所有请求的数据。
  - 获取文件的真实（服务器）路径。
    - `String getRealPath(String path)`：所要获取的文件如果在 web 目录下，传入参数为 /文件名。

