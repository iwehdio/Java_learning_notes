# 39、Response、ServletContext

## 1、HTTP-响应

- 响应消息数据格式：
  1. 响应行。
  2. 响应头。
  3. 响应空行。
  4. 响应体。
- 响应行：
  - 格式：协议 / 版本 响应状态码 状态码描述。
  - 响应状态码：服务器告诉客户端浏览器本次请求和响应的状态。
    - 状态码都是三位数字。
    - 分类：
      1. 1xx ：1开头，服务器接收客户端浏览器消息但没有完成，等待后发送给浏览器状态码询问。
      2. 2xx ：2开头，代表成功。如200代表成功
      3. 3xx ：3开头，代表重定向，是一种资源跳转方式。如302代表重定向，304代表访问缓存资源。
      4. 4xx ：4开头，客户端错误。如404代表请求路径没有对应的资源，405代表请求方式没有对应的方法实现。
      5. 5xx ：5开头，服务器端错误。如500代表服务器内部异常。
- 响应头：
  - 格式：头名称：值。
  - 常见响应头：
    - Content-Type：服务器告诉客户端浏览器本次响应体数据格式以及编码格式。
    - Conten-disposition：服务器告诉客户端浏览器以何种格式打开响应体数据，默认为 in-line 在当前页面内打开。attachment: filename=文件名 表示以附件形式打开响应体。
- 响应体：传输的数据。

## 2、Response

- 功能：用来设置响应消息。

- 设置响应行：`void setStatus(in sc)`：设置状态码。

- 设置响应头：`void setHeader(String name, String value)`。

- 设置响应体：

  - 获取输出流：
    - 字节输出流：`ServletOutputStream getOutputStream()`。
    - 字符输出流：`PrintWriter getWriter()`。
  - 使用输出流将数据输出到客户端浏览器。

- 示例1：重定向。

  - 重定向：一种资源跳转方式，浏览器访问A资源，A资源要求浏览器访问B资源实现。

  - IDEA导入 HttpServlet：在项目结构下的库中，添加 Tomcat 目录下的 lib 下的 servlet-api.jar 。

  - 步骤：

    1. 指定重定向的状态码302，告知客户端浏览器。
    2. 设置响应头指定跳转资源。

  - 实现：

    ```java
    @WebServlet("/ResDemo1")
    public class ex1_1 extends HttpServlet {
        protected void doPost(HttpServletRequest request, HttpServletResponse response) throws javax.servlet.ServletException, IOException {
            System.out.println("demo1...");
            response.setStatus(302);
            response.setHeader("location", "/Response/ResDemo2");
            /* 这个方法相当于集成了前两个，只需传入资源路径
            response.sendRedirect("/Response/ResDemo2");
            */
        }
        protected void doGet(HttpServletRequest request, HttpServletResponse response) throws javax.servlet.ServletException, IOException {
            this.doPost(request, response);
        }
    }
    
    @WebServlet("/ResDemo2")
    public class ex1_2 extends HttpServlet {
        protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            System.out.println("demo2...");
        }
        protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            this.doPost(request, response);
        }
    }
    ```

  - 重定向（redirect）的特点：

    - 地址栏发生变化。
    - 重定向可以访问其他服务器的资源。
    - 重定向是两次请求，不能用 request 对象共享数据。

    - 对比转发（forward）的特点：地址栏路径不变、只能访问当前服务器下的资源、是一次请求。
    - 对比而言，转发是由第一次访问的 Servlet 执行的，重定向是在访问第一个 Servlet 后，客户端浏览器器根据响应访问第二个 Servlet 。

  - 资源路径的写法：

    - 分类：相对路径和绝对路径。
    - 绝对路径：可以确定唯一资源。在这里指在 http://域名 后的，以 / 开头的资源路径。
    - 相对路径：不能确定唯一资源。不以 / 开头，而以 . 开头。
    - 要使用相对路径，先要找到当前资源与目标资源的相对位置关系。如要找在同一目录下的，为 ./目标资源路径 。 ./ 表示当前路径，可省略。../ 表示向上一级目录。
    - 要使用绝对路径，先要判断所定义的路径是给谁用。如果要给客户端浏览器用，就要在其前面加虚拟路径，如浏览器访问、重定向。如果要给服务器用，则直接写定义的资源路径，如转发。
    - 对于要加虚拟路径的，建议用 request 对象的 `getContextPath()` 动态获取，可以在虚拟目录改变后仍能正常使用。
  
- 示例2：输出字符数据。

  - 解决中文乱码问题：在获取输出流之前，设置服务器编码，并且告诉浏览器服务器使用的何种编码。

  - 步骤：

    1. 设置服务器编码，并用响应头告知浏览器。
    2. 获取字符输出流。
    3. 输出数据。

  - 实现：

    ```java
    @WebServlet("/ResDemo3")
    public class ex2 extends HttpServlet {
        protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            /* 设置服务器编码，并用响应头告知浏览器
            response.setCharacterEncoding("utf-8");
            response.setHeader("content-type", "text/html;charset=utf-8");
            下边这句完成了以上两句的功能 */
            response.setContentType("text/html;charset=utf-8");
            PrintWriter pw = response.getWriter();
            pw.write("输出字符数据，<h2>utf-8</h2>");
        }
        protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            this.doPost(request, response);
        }
    }
    ```

- 实例3：输出字节数据。

  - 与2不同的是获取的是字节输出流。

  - 实现：

    ```java
    @WebServlet("/ResDemo4")
    public class ex3 extends HttpServlet {
        protected void doPost(HttpServletRequest request, HttpServletResponse response) throws IOException {
            response.setContentType("text/html;charset=utf-8");
            ServletOutputStream sos = response.getOutputStream();
            sos.write("输出字符数据,utf-8".getBytes("utf-8"));
        }
        protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            this.doPost(request, response);
        }
    }
    ```

- 示例4：验证码。

  - 实现：

    ```java
    @WebServlet("/Check")
    public class Check extends HttpServlet {
        protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            int width = 100;
            int height = 50;
            //创建一对象，在内存中图片(验证码图片对象)
            BufferedImage image = new BufferedImage(width,height,BufferedImage.TYPE_INT_RGB);
            //填充背景色
            Graphics g = image.getGraphics();//画笔对象
            g.setColor(Color.PINK);//设置画笔颜色
            g.fillRect(0,0,width,height);
            //画边框
            g.setColor(Color.BLUE);
            g.drawRect(0,0,width - 1,height - 1);
            String str = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghigklmnopqrstuvwxyz0123456789";
            //生成随机角标
            Random ran = new Random();
            for (int i = 1; i <= 4; i++) {
                int index = ran.nextInt(str.length());
                //获取字符
                char ch = str.charAt(index);//随机字符
                //写验证码
                g.drawString(ch+"",width/5*i,height/2);
            }
            //画干扰线
            g.setColor(Color.GREEN);
            //随机生成坐标点
            for (int i = 0; i < 10; i++) {
                int x1 = ran.nextInt(width);
                int x2 = ran.nextInt(width);
                int y1 = ran.nextInt(height);
                int y2 = ran.nextInt(height);
                g.drawLine(x1,y1,x2,y2);
            }
            //将图片输出到页面展示
            ImageIO.write(image,"jpg",response.getOutputStream());
        }
        protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            this.doPost(request,response);
        }
    }
    ```

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <script>
        window.onload = function(){
            //获取图片对象
            var img = document.getElementById("checkCode");
            //绑定单击事件
            img.onclick = function(){
                //加时间戳
                var date = new Date().getTime();
                img.src = "/Response/Check?"+date;
            }
        }
        </script>
    </head>
    <body>
        <img id="checkCode" src="/Response/Check" />
    </body>
    </html>
    ```

## 3、ServletContext

> 代表整个web应用，可以与程序的容器（服务器）通信。

- 获取：
  - request 对象的 `getServletContext()`方法。
  - HttpServlet 对象的 `getServletContext()`方法。
- 功能：
  - 获取 MIME 类型。
    - MIME 类型：互联网通信过程中定义的一种文件数据类型，格式为 大类型 / 小类型 。
    - `String getMimeType(String filename)`：传入文件名，返回 MIME 类型。
  - 是一个域对象，可用于共享数据。
    - `setAttribute(String name, Object value)`：设置保存数据。
    - `getAttribute(String name)`：获取数据。
    - `removeAttribute(String name)`：删除数据。
    - 作用范围，可以获取所有用户所有请求的数据。
  - 获取文件的真实（服务器）路径。
    - `String getRealPath(String path)`：所要获取的文件如果在 web 目录下，传入参数为 /文件名。
    - 如果在 WEB-INF 下，传入参数为 /WEB-INF/文件名。
    - 如果在 src 目录下，传入参数为 /WEB-INF/classes/文件名。

## 4、案例

- 文件下载：页面显示超链接，点击超链接后弹出下载提示框。

- 分析：

  - 超链接指向的资源如果能被浏览器解析，则会在浏览器内显示，否则才弹出下载框。
  - 需要使用响应头设置资源打开方式为附件。

- 步骤：

  1. 在 html 文件中，编辑超链接 href 属性，指向 Servlet 并传递资源名称。
  2. 定义 Servlet 。
     1. 获取文件名称。
     2. 使用字节输入流加载文件。
     3. 设置响应头。
     4. 将数据写出到 response 输出流。

- 实现：

  ```java
  @WebServlet("/Download")
  public class Download extends HttpServlet {
      protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          //获取传入的filename
          String filename = request.getParameter("filename");
          //使用ServletContext将filename转换为真实（服务器）路径
          ServletContext sc = this.getServletContext();
          String RealPath = sc.getRealPath("/img/" + filename);
          //使用ServletContext获取资源的MimeType
          String MimeType = sc.getMimeType(filename);
          //设置响应头
          response.setHeader("content-type", MimeType);
          response.setHeader("content-disposition", "attachment;filename=" + filename);
          //获取字节输入流，将文件用response下的输出流输出
          FileInputStream fis = new FileInputStream(RealPath);
          ServletOutputStream sos = response.getOutputStream();
          byte[] bys = new byte[1024 * 8];
          int len;
          while((len = fis.read(bys))!= -1){
              sos.write(bys, 0, len);
          }
          fis.close();
      }
      protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          this.doPost(request, response);
      }
  }
  ```

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
  </head>
  <body>
      <a href="/Response/Download?filename=poluo.jpg">图片</a>
  </body>
  </html>
  ```

- 中文文件名乱码问题：

  - 使用 DownLoadUtils 工具类。

  - 通过获取请求头 user-agent，获得浏览器类型，然后使用工具类对不同类型浏览器进行文件名编码。

  - 在设置响应头打开方式之前。

  - 实现：

    ```java
    //获取请求头
    String agent = request.getHeader("user-agent");
    //使用工具类编码文件名
    filename = DownLoadUtils.getFileName(agent, filename);
    ```

  -  DownLoadUtils 工具类：

    ```java
    import java.util.Base64;
    import  java.util.Base64.Encoder;
    import java.io.UnsupportedEncodingException;
    import java.net.URLEncoder;
    
    public class DownLoadUtils {
        public static String getFileName(String agent, String filename) throws UnsupportedEncodingException {
            if (agent.contains("MSIE")) {
                // IE浏览器
                filename = URLEncoder.encode(filename, "utf-8");
                filename = filename.replace("+", " ");
            } else if (agent.contains("Firefox")) {
                // 火狐浏览器
                Encoder base64Encoder = Base64.getEncoder();
                filename = "=?utf-8?B?" + base64Encoder.encode(filename.getBytes("utf-8")) + "?=";
            } else {
                // 其它浏览器
                filename = URLEncoder.encode(filename, "utf-8");
            }
            return filename;
        }
    }
    ```

    

