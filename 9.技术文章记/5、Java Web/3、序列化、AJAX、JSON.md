学习自：

- [又一个难题：序列化和反序列化为什么要实现 Serializable 接口](https://mp.weixin.qq.com/s/NM9aLvpCFcz9A-S80x5E1w)
- [AJAX与JSON](https://zhuanlan.zhihu.com/p/38773438)

## 1、序列化

- 序列化和反序列化
  - 序列化：把对象转换为字节序列的过程称为对象的序列化.
  - 反序列化：把字节序列恢复为对象的过程称为对象的反序列化.
- 什么时候需要用到序列化和反序列化呢?
  - 当我们只在本地 JVM 里运行下 Java 实例，这个时候是不需要什么序列化和反序列化的，但当我们需要将内存中的对象持久化到磁盘，数据库中时， 当我们需要与浏览器进行交互时，当我们需要实现 RPC 时， 这个时候就需要序列化和反序列化了。
  - **只要我们对内存中的对象进行持久化或网络传输， 这个时候都需要序列化和反序列化.**
  - 例如：服务器与浏览器使用JSON交互时，JSON 格式实际上就是将一个对象转化为字符串。String 类型实现了 Serializable 接口，并显示指定 serialVersionUID 的值。
- 为什么要实现 Serializable 接口?
  - 在 Java 中实现了 Serializable 接口后， JVM 会在底层帮我们实现序列化和反序列化， 如果我们不实现 Serializable 接口， 那自己去写一套序列化和反序列化代码也行。
- 为什么还要指定serialVersionUID的值?
  - 如果不显式指定 serialVersionUID， JVM 在序列化时会根据属性自动生成一个 serialVersionUID， 然后与属性一起序列化，再进行持久化或网络传输。
  - 在反序列化时，JVM 会再根据属性自动生成一个新版 serialVersionUID，然后将这个新版 serialVersionUID 与序列化时生成的旧版 serialVersionUID 进行比较，如果相同则反序列化成功， 否则报错.
  - 如果显示指定了 serialVersionUID， JVM 在序列化和反序列化时仍然都会生成一个 serialVersionUID， 但值为我们显示指定的值，这样在反序列化时新旧版本的 serialVersionUID 就一致了.
  - 在实际开发中， 不显示指定 serialVersionUID 的情况会导致什么问题？如果我们的类写完后不再修改，那当然不会有问题。
  - 但这在实际开发中是不可能的，我们的类会不断迭代，一旦类被修改了，那旧对象反序列化就会报错。所以在实际开发中， 我们都会显示指定一个 serialVersionUID，值是多少无所谓， 只要不变就行.
- Java 序列化的其他特性：
  - 被 transient 关键字修饰的属性不会被序列化， static 属性也不会被序列化.
  - 因为序列化是针对对象而言的，而 static 属性优先于对象存在， 随着类的加载而加载， 所以不会被序列化.
  - serialVersionUID 也被 static 修饰， 为什么 serialVersionUID 会被序列化？ 
    - 其实 serialVersionUID 属性并没有被序列化， JVM 在序列化对象时会自动生成一个 serialVersionUID， 然后将我们显式指定的 serialVersionUID 属性值赋给自动生成的 serialVersionUID。

## 2、AJAX和JSON

- AJAX不是一门编程语言，只是一种技术。它和JS有关，也就是说它的代码运行在客户端（因为JS运行在浏览器），而不是服务端。

- AJAX的全称是Asynchronous JavaScript And XML（异步的JS和XML），说明当初这门技术设想的最佳拍档是JS和XML。但现在大家更倾向于JS+JSON。

- 关于“同步”与“异步”，可以粗浅地理解为：

  - 同步：我填完所有表单后，点击“提交”按钮，所有内容才会一起发给服务器！
  - 异步：我刚填完“账号”，点击填写“密码”时，页面就让JS拿着“账号”去让服务器校验了。那么我们填写“密码”时就知道刚才填写的“账号”是不是重复/错误的。

- JS实现异步请求的原理：

  - 原先JS的请求由JS引擎负责，同步请求依赖JS引擎。当JS发起一个请求后，如果没有收到服务端响应，它是不能做任何事的，处于等待状态，效率很低。
  - 而异步请求是由JS委托AJAX引擎（XMLHttpRequest对象）发起的。请求交给AJAX引擎后，JS引擎就可以抽身做别的事，等到AJAX引擎拿到响应给它后，它再渲染数据显示即可！

  ![image-20210120192916564](http://iwehdio.gitee.io/img/my-img/21-01/image-20210120192916564.png)

- JSON作为一种轻量级的**数据交换格式**，采用完全独立于编程语言的**文本格式**来存储和表示数据，易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率。

- JSON的英文全称是JavaScript Object Notation，也就是**JS对象标记法**。

  - 后端传给前端的JSON串在前端解析后可能是数组或者JS对象。解析后得到的对象往往被人们称为JSON对象（如果是数组，也是JSON对象数组，即元素是JSON对象）。
  - 前端传给后端的JSON串则转化为对应后端语言的对象，比如Java的类对象。

- AJAX传送JSON：

  - 请求时有两种选择：key/value格式 or JSON串。请求时key/value格式比JSON串常用。

    ![image-20210120194133902](http://iwehdio.gitee.io/img/my-img/21-01/image-20210120194133902.png)

    ![image-20210120194148334](http://iwehdio.gitee.io/img/my-img/21-01/image-20210120194148334.png)

  - 响应时都返回JSON串。

- 后端的转化主要涉及

  - 接收前端请求：（JSON串/key-value格式）→Java对象
    - 接收key/value格式的请求，就是根据键取值，而且在SpringMVC中都是自动封装到形参里的。
    - 接收JSON串呢？在SpringMVC框架中，提供了@RequestBody注解来将JSON格式的请求参数自动封装成Java对象。
  - 响应：Java对象→JSON串
    - 在SpringMVC框架中，还提供了@ResponseBody注解来将Java对象转为JSON串返回给前端。
  - 特别注意下Controller里的方法，加不加@ResponseBody的区别（以return "index"为例）：
    - 不加注解，会把return的字符串解析为视图地址，跳转到对应页面
    - 加了注解，不跳转页面，会把return的字符串当做数据返回