# 46、Ajax&JSON

## 1、Ajax

> Asynchronous JavaScript And XML，异步的JavaScript和XML。在无需加载整个页面的情况下，能够更新部分网页。

- 异步和同步：

  - 客户端和服务器端相互通信的基础上。
  - 同步指，客户端必须等待服务器端的响应，这期间客户端不能做任何操作。
  - 异步指，客户端不必等待服务器端的响应，这期间客户端可以做其他操作。

- 原生 JS 实现方式：

  1. 绑定事件函数。

     ```html
     <script>
         function func(){
             /* 内容 */
         }
     </script>
     ```

  2. 创建核心对象。

     ```javascript
     var xmlhttp;
     if (window.XMLHttpRequest)
     {// code for IE7+, Firefox, Chrome, Opera, Safari
         xmlhttp=new XMLHttpRequest();
     }
     else
     {// code for IE6, IE5
         xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
     }
     ```

  3. 建立连接。`xmlhttp.open("GET", "text1,txt", true)`。三个参数分别为，请求类型、请求 URL 和同步（false）或异步（true）。

  4. 发送请求。`xmlhttp.send()`。对于 get 方式，请求参数在 URL 后拼接，send 方法中为空参；对于post 请求，请求参数在 send 方法中。 

  5. 接收来自服务器的响应。就绪状态码为 4 时表示请求完成且响应就绪，响应状态码为 200 表示响应成功。

     ```javascript
     xmlhttp.onreadystatechange=function()
     {
         if (xmlhttp.readyState==4 && xmlhttp.status==200)
         {
             var responseText = xmlhttp.responseText;
         }
     }
     ```

- JQuery 实现方式：

  - `$.ajax(url, [settings])`或`$.ajax({键值对})`：发送异步请求。如果写成键值对形式，键包括 url（请求路径参数）、type（请求方式）、data（请求参数）、success（响应成功后的回调函数）、error（出错后的回调函数）、dataType（接收到的响应数据格式）等。
  - `$.get(url, [data], [callback], [type])`：发送 get 请求。参数分别为请求路径、请求参数、回调函数和响应数据类型。
  - `$.post(url, [data], [callback], [type])`：发送 post 请求。

## 2、JSON

> JavaScript Object Notation，JavaScript对象表示法。是存储或交换文本信息的语法。

- 基本语法：

  - 数据在名称 / 值对中：JSON 数据是由键值对构成的。每对键值对用逗号分隔。键可以用用不用引号都可以，但要一致。
  - 嵌套格式：大括号定义保存对象，中括号定义保存数组。
    - 保存对象：`var v = {"key":{"key1":value1, "key2":value2}}`。
    - 保存式中：`var v = {"key":[{"key1":value1}, {"key2":value2}]}`。
  - 值的取值类型可以玩数组、字符串、逻辑值、数组、对象或 null 。

- 获取数据：

  - `json对象.键名`。

  - `json对象["键名"]`：单引号双引号都可以。

  - `json数组对象[索引]`。

  - 遍历：

    ```javascript
    for(var key in json_object){
        //在这里key是字符串形式，不能直接 person.key 获取
        alert(key + ":" + person[key]);
    }
    ```

- JSON 数据与 Java 对象的转换：jackson解析器。

  - 使用步骤：
    1. 导入依赖 jar 包。
    2. 创建 Jackson 核心对象 ObjectMapper 。
    3. 进行转换。
  - JSON 转 Java 对象：
    - `mapper.readValue(JSON字符串数组, class类型)`：class类型是所要转换成的类对象。
  - Java 对象转 JSON：
    - `mapper.writeValueAsString(obj)`：将Java对象转为JSON字符串。
    - `mapper.writeValue(参数,obj)`：将Java对象转为JSON字符串，并存入文件（参数为 File 对象）、字符输出流（参数为 Writer 对象）、字节输出流（参数为 OutputStream 对象）。
    - 注解：
      - `@JsonIgnore`：忽略该属性，转JSON时不会出现此属性。加在该成员变量上。
      - `@JsonFormat(格式)`：属性值的格式化。加在该成员变量上。如对于 Date，格式可为 `pattern = "yyy-MM-dd"`。
    - 复杂的 Java 对象：
      - List 数组：转换为 JSON 数组。
      - Map 集合：转换为一个 JSON 对象。

## 3、案例

- 校验用户名是否存在：

  -  通过浏览器传给服务器用户名，服务器经过判断后，响应 JSON 数据给浏览器实现。

  - login.html：

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JSON</title>
        <script src="js/jquery-3.3.1.min.js"></script>
        <script>
            $(function () {
                $("#username").blur(function () {
                    var username = $(this).val();
                    $.get("findUsernameServlet", {username:username}, function (data) {
                        var span = $("#tips");
                        if(data.userExist){
                            span.css("color", "red");
                            span.html(data.msg);
                        } else {
                            span.css("color", "green");
                            span.html(data.msg);
                        }
                    }, "json")
                })
            })
        </script>
    </head>
    <body>
        <form>
            <input type="text" name="username" id="username" placeholder="请输入用户名"><br>
            <span id="tips"></span>
        </form>
    </body>
    </html>
    ```

  - FindUsernameServlet:

    ```java
    @WebServlet("/findUsernameServlet")
    public class FindUsernameServlet extends HttpServlet {
        protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            request.setCharacterEncoding("utf-8");
            response.setContentType("text/html;charset=utf-8");
            String username = request.getParameter("username");
            Map<String, Object> map = new HashMap<String, Object>();
    		//此处应使用数据库查询
            if("otto".equals(username)){
                map.put("userExist", true);
                map.put("msg", "用户名已被占用");
            } else {
                map.put("userExist", false);
                map.put("msg", "用户名可以使用");
            }
            ObjectMapper mapper = new ObjectMapper();
            mapper.writeValue(response.getWriter(), map);
        }
    
        protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            this.doPost(request, response);
        }
    }
    ```

  - 如何让浏览器获知响应的是 JSON 类型的数据，防止乱码：

    - 在`$.get()`方法的最后一个 type 参数指定为 "json"。
    - 在` response.setContentType("application/json;charset=utf-8")`，指定 MIME 类型为 json。