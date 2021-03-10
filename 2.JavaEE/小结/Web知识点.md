### Servlet

- Servlet要实现的方法与生命周期。
- Servlet执行原理。
- 体系结构。
- 访问路径配置。



### Request

- 浏览器请求Tomcat服务器的流程。
- 功能：
  - 获取请求数据（请求行/请求头/请求体）。
  - 获取请求参数。
  - 请求转发。
  - 共享数据。
  - 获取 ServletContext 。
- BeanUtils工具类。



### Response

- 功能：
  - 设置响应（响应行/响应头/响应体）。
  - 重定向。
- ServletContext：
  - 获取。
  - 共享数据。
  - 获取服务器路径。



### Cookie&Session

- 使用Cookie：
  - 服务器创建。
  - 服务器发送。
  - 服务器获取。
- Cookie原理。
- Cookie相关问题：
  - 一次发送多个。
  - 生命周期。
  - 存储中文。
  - 项目/服务器间共享。
- Cookie的特点和作用。
- HttpSession的方法。
- 使用HttpSession的步骤。
- Session原理。
- Session相关问题：
  - 客户端关闭，服务器不关闭，看Cookie存活时间。
  - 客户端不关闭，服务器关闭，Session的钝化与活化。
  - 被销毁。
- Session的特点。



### 用户信息案例

- 资源跳转：
  - 资源路径是否需要写虚拟路径。
  - 转发与重定向。
- 标准类封装。



### Filter

- 作用。
- 使用步骤。
- 执行流程。
- 生命周期。
- 拦截路径和方式配置。
- 过滤器链。



### Listener

- 事件监听。
- ServletContextListener 接口监听器。