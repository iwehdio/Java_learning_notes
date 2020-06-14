## 1、JSP

- JSP指令：用于配置JSP页面，导入资源文件。
  - 格式：`<%@ 指令名称 属性名1=属性值1 属性名2=属性值2 %>`。
  - 分类：
    1. page：配置JSP页面。
       - `contentType`：设置响应体MIME类型与字符集，等同于 resource.setContentType() 。
       - `pageEncodeing`：设置当前页面的jsp页面编码。
       - `buffer`；缓冲区大小。
       - `import`：导包。
       - `errorPage`：当前页面发生异常后，所要跳转的错误页提示面。
       - `isErrorPage`：标识当前页面是一个错误提示页面，为true时才可以使用异常对象exception。
    2. include：包含其他页面的内容，导入页面的资源文件。
       - `<%@include file="top.jsp" %>`：当前页面就会导入top.jsp页面下的内容。
    3. taglib：导入资源。
       - `<%@ taglib prefix="c" uri="" %>`。导入标签库。
       - prefix：标签头前缀，可自定义。
       - uri：标签库的位置uri。
- JSP注释：
  - html注释：`<!-- 注释 -->`。只能注释html片段。
  - jsp注释：`<%-- 注释 --%>`。可以注释全部内容。
- JSP内置对象：在jsp页面中不需要创建，可直接使用。
  - pageContext：类型是PageContext。作用域是当前页面共享数据。
    - 可以使用 getXxx() 方法获取其他8个内置对象。
  - request：类型是HttpServletRequest。作用域是一起请求访问的多个资源（转发）。
  - session：类型是HttpSession。作用域是一次会话的多个请求间。
  - application：类型是ServletContext。作用域是所有用户间共享的数据。
  - response：类型是HttpServletResponse。作用域是响应对象。
  - page：类型是Object。作用域是当前页面（Servlet）对象。
  - out：类型是JspWriter。输出对象，将数据输出到页面上。
  - config：类型是ServletConfig。Servlet的配置对象。
  - exception：类型是Throwable。异常对象，需要先指定 isErrorPage 后才能使用。

## 2、EL表达式

- 概念：Expression Language，表达式语言。

- 作用：替换和简化jsp页面中的java代码。

- 语法：`${表达式}`。

- 注意：

  - 在 page 指令中 isELIgnored 为true，可以忽略当前页面下的EL表达式。
  - EL表达式前加 \ 可以忽略表达式，而是原样输出。

- 使用：

  - 运算：运算符。
    - 算数运算符：div 可替换 /，mod 可替换 % 。
    - 比较运算符。
    - 逻辑运算符：and 可替换 &&，or 可替换 ||，not 可替换 ！。
    - 空运算符：empty，用于判断字符串、集合、数组对象是否为null或长度是否为0。
      - 语法：`${empty 对象名}`，返回值为布尔类型，返回为true时表示为空或长度为零。
      - `not empty`可判断是否不为null且长度不为0。
  - 获取值：
    - EL表达式只能从域对象中获取值。
    - 域名称：
      - `pageScope` -> pageContext 域。
      - `requestScope` -> request 域。
      - `sessionScope` -> session 域。
      - `applicationScope` -> ServletContext 域。
    - 语法：
      - `${域名称.键名}`，从指定域中获取指定键的值。
      - `${键名}`：从最小的域开始查找是否有对应的值，直到找到为止。
    - 获取不到对应的值也不会报错，返回空字符串。
  - 获取对象：
    - 直接获取到的是对象的地址值，可以通过对象（JavaBean）的属性获取对象的值。
    - 语法：`${域名称.键名.属性名}`，本质是调用 getXxx 方法。
    - 可以在对象中使用逻辑视图获取属性。
  - 获取List集合：
    - 语法：`${域名称.键名[索引值]}`。
    - 下标越界不会报错，返回空字符串。
  - 获取Map集合：
    - 语法：`${域名称.键名.key名称}`或`${域名称.键名["key名称"]}`。

- 隐式对象：

  - 在EL表达式中不需要创建，可直接使用，类似于JSP内置对象，共11个。

  - pageContext隐式对象：
    - 用属性调用 getXxx() 方法获取JSP其他8个内置对象。
    - jsp页面动态获取虚拟目录：`${pageContext.request.contextPath}`。

## 3、JSTL

> JavaServer Page Tag Library :JSP标准标签库。用于简化和替换jsp页面上的Java代码。

- 使用步骤：

  1. 导入JSTL相关jar包。
  2. taglib指令引入标签库。
  3. 使用标签。

- 常用标签：

  1. if 标签：相当于Java中的 if 语句。

     - 格式：`<c:if test="表达式"></c:if>`，前缀为 c 时。
     - 必须有一个 test 属性，接收布尔表达式。如果表达式为 true ，显示 if 标签体内容；如果为 false ，则不显示。
     - 一般结合EL表达式使用。

  2. choose 标签：相当于Java中的 switch 语句。

     - 格式：

       ```jsp
       <c:choose>
           <c:when test="表达式1"></c:when>
           <c:when test="表达式2"></c:when>
           <c:otherwise></c:otherwise>
       </c:choose>
       ```

     - when 相当于 case，otherwise 相当于 default。

  3. forEach 标签：相当于Java中的 for 语句。

     - 格式1：`<c:forEach begin="起始" end="结束" var="变量" step="步长"></c:forEach>`。用于进行重复操作。
     - begin 是开始值，end 是结束值，开始和结束值都包括。var 是定义的临时变量。step 是步长。
     - 相当于：`for(int var=begin; var<=end; var+=step)`。
     - 格式2：`<c:forEach items="容器对象" var="变量">`。用于遍历容器。
     - items 是容器对象，var 是容器中的元素。
     - 相当于：`for(Object var : items)`。
     - 还有 varStatus 属性，循环状态对象。有 .index 方法获取遍历容器中元素的所有，从0开始； .count 方法获取循环次数，从1开始。

## 4、MVC与三层架构

- MVC开发模式：
  - 将程序分为三个部分：Model，模型；View，视图；Controller，控制器。
  - 控制器（Servlet）：
    1. 获取客户端的输入。
    2. 调用模型，进行业务操作。
    3. 将数据交给视图展示。
  - 模型（JavaBean）：业务逻辑操作（查询数据库、封装对象等）。
  - 视图（JSP）：展示数据。
- MVC的优点：耦合性低，方便维护，重用性高，分工明确。
- MVC的缺点：复杂化架构，不适合小型项目。
- 三层架构：一种软件设计架构。
  1. 界面层（表示层 / web层）：用户通过界面上的组件和服务器进行交互。
     - Servlet作为控制器。从浏览器接收数据后，Servlet将其传给后续两层，在获取后续层返回的数据后传递给前端页面并返回浏览器。
     - 作用是，接收用户参数，封装数据，调用业务逻辑层完成处理，转发jsp页面完成显示。
     - 一般在 项目名.web 包下，可用 SpringMVC 框架简化。
  2. 业务逻辑层（service层）：处理业务逻辑。
     - 作用是，组合dao层中最简单的数据操作方法，形成复杂的功能。
     - 一般在 项目名.service 包下，可用 Spring 框架简化。
  3. 数据访问层（dao层）：操作数据存储文件。
     - 作用是，定义了对数据库最基础的增删改查操作。
     - 一般在 项目名.dao 包下，可用Mybatis 框架简化。





