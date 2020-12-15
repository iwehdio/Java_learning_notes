1. 为什么数据库表中不用外键。
2. 存储或者更新方法需要检测重复吗。

5-4

1. 具体的数据操作为什么不放在service层。
2. 有没有优化每次都遍历所有产品的方法。
3. resultvo静态方法使用泛型



6-1

1. 枚举增加可读性。



6-3

1. 为什么不用@OneToMany而是重新创建一个对象。
2. DTO、VO和Entity能不能进行有效复用。
3. 传入了不存在的商品号怎么办。



6-6

1. 把创建订单返回OrderDTO的意义？如果对其设置查询的新值，那其中的DetailList是CartDTO也可以。
2. 每次OrderDTO和OrderMaster转换时，其中为null的数据是不是都不同？



6-8

1. CollecUtils。
2. stream流，map()、collect(Collectors.toList)
3. 判断和修改订单状态可以抽取为方法。



6-10~6-13

1. 表单校验`@Empty`和`@Valid`。bindingResult
2. 异常
3. StringUtils
4. page对象
5. @JsonSerialiazer。json中嵌套对象怎么办
6. @JsonInclude，返回前端的数据尽量不要为null，而是不返回，或返回""和[]。对于String和List（给字段返回初始值）。
7. VO的设计
8. gson



[总结springboot微信点餐项目_两颗西柚233的博客-CSDN博客](https://blog.csdn.net/qq_37128162/article/details/101036296)

1、善用dto、vo、form这几个对象的传递和转化
2、随时记录日志的思想
3、枚举类存储状态和返回值
4、redis分布式锁
5、结合redis的缓存的实现
6、AOP验证微信是否登录
7、全局异常的处理@ControllerAdvice





7-8

1. 直接调用带有@Bean注释的方法。改用依赖注入。@configuration

2. 支付宝签名算法

3. Springboot中的静态资源路径访问，

   ```yaml
   #  mvc:
   #    static-path-pattern: /static/**
   #  resources:
   #    static-locations: classpath:/static/
   ```

4. 项目的前缀

5. 异常的设计

6. 支付的事务，具体的dao操作怎么组织

7. 支付宝demo提供的截取字符串函数只能截取&

8. 数字显示精度转换

9. 退款后需要查询是否成功

10. 调起浏览器支付宝时显示加载中和安全证书





9

1. 枚举实现接口
2. 卖家端代码的抽取



12

1. 微信登录的token是什么

2. 什么是openid

3. 对于工具类，单例和静态的效率

4. 重定向尽量用绝对路径？

5. @ControllerAdvice

6. ```java
   ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
   HttpServletRequest request = attributes.getRequest();
   ```

7. request.getCookies()返回null

8. WebSocket原理

