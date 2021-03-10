## 1、集合体系

 [Java核心（一）——集合/ArrayList/HashMap](https://www.cnblogs.com/iwehdio/p/14269208.html)

- Collection继承体系？
- ArrayList和LinkedList的选择？
- ArrayList是什么？
- ArrayList的初始化？
- ArrayList的扩容？
- ArrayList的默认数组大小为什么是10？
- ArrayList在增删的时候是怎么做的？
- ArrayList（int initialCapacity）会不会初始化数组大小size？
- ArrayList是线程安全的么？线程安全的List还有什么？
- ArrayList和LinkedList性能比较如何？适合做队列吗？
- Queue 和 Deque？
- Set 的常用实现类？
- HashMap的实现原理？
- HashMap 中是如何保证元素的唯一性？
- 为什么重写 equals() 方法，一定要重写 hashCode() 呢？
- 新的Entry节点在插入链表的时候，是怎么插入的？
- 在java7中为什么出现死循环？
- HashMap的扩容机制？
- 为什么HashMap的默认初始化长度是16？
- 为什么默认加载因子是0.75？
- HashMap指定容量初始化？
- HashMap中的`hash()`方法？
- 为什么java8中使用了红黑树？
- 为什么Hashmap中的链表大小超过八个时会自动转化为红黑树，当删除小于六时重新变为链表？
- 为什么 HashMap 中 String、Integer 这样的包装类适合作为 key 键？
- HashMap的k-v都允许为null？
- HashMap不保证有序？



## 2、核心语法

 [Java核心（二）——this/反射/注解](https://www.cnblogs.com/iwehdio/p/14285284.html)

 [Java核心（三）——泛型和枚举](https://www.cnblogs.com/iwehdio/p/14295525.html)
- 泛型设计原则？为什么需要泛型？
  - 编译期警告，运行时类型转换异常。
- 简单泛型为什么需要左右一致？
  - 允许父类泛型指向子类，添加其他类型后，重新被子类泛型指向，取出会出问题。
- 通配符上下限？
  - 上限extends，下限super，通配？
- 形式类型参数T和通配符？的区别？
  - 确定类型，泛型一致性，设置多个上限，设置下限
- 泛型的实现？泛型擦除？
  - 底层Object[]存储元素，编译不含泛型，虚拟机不知情
- 泛型的作用？
  - 抽取代码模板，类型校验，类型强转
- 泛型为什么不支持基本数据类型？
  - 本身就是为了解决引用类型强转易出错问题，自动拆装箱从功能上已经支持，本质是底层使用的是Object接收。
- 枚举的本质？
  - 继承自Enum的final类
  - 枚举DemoEnum中的每个成员都是一个DemoEnum对象
  - 枚举中维护了所有成员的数组，并且根据成员的变量值编号
- 枚举和常量类有什么区别呢？
  - 限制重复定义常量，包含更多维度信息
- 枚举策略？
  - 在枚举类中定义抽象方法，强制子类实现

 [Java核心（四）——动态代理](https://www.cnblogs.com/iwehdio/p/14314930.html)

- 动态代理的思路？
  - 通过获得被代理对象的接口的Class对象，获取被代理对象的方法信息。然后通过反射获取代理对象调用的方法和参数
- 引入InvocationHandler的好处是？
  - 把“方法”和“方法体”分离






























