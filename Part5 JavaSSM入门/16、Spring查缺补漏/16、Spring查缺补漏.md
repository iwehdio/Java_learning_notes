## 1、IOC

- 框架：高度抽取可重用代码，高度的通用性。多个可重用模块的集合，形成整体解决方案。
- Spring：容器框架，简化企业级开发。
- IOC容器可以用来整合其他框架。
- 控制反转：
  - 控制：资源的获取方式，分为主动式（要什么资源就new一个）和被动式（资源的获取不是自己创建而是交给容器）。
  - 反转：从主动的new资源变为被动的接收资源。
- WEB项目类路径的开始：WEB-INF/classes。
- 普通Java项目的类路径的开始：src。
- 对象是什么时候创建的：
  - 在创建容器完成时，容器中的对象也就被创建好了。
  - 同一个组件在IOC容器中只有一份。是单例的。
  - 获取容器中不存在的bean时，报错这个bean没有被定义。
  - xml中`<property>`标签对bean的赋值使用了setter方法。
- 可以根据bean的类型从IOC中获取bean实例：
  - `.getBean(Person.class)`。
  - 如果IOC中有多个这种类型的，该方法会报错。
  - 也可以同时传入id和类型。
- 构造器赋值时：
  - 传入的参数必须有类型和数量相对应的构造方法。
  - 如果不指定name只写value，顺序与构造方法必须一致。或者指定index。
  - 对于参数数量相同的重载构造器，需要制定type属性为参数的类型。
- 通过p名称空间为bean赋值：
  - 名称空间：为了防止在xml中标签重复。需要额外导包。
  - 格式：`<bean p:age="18">`。使用的也是setter方法。
- 为引用类型赋值：
  - 赋值为null：在`<property>`标签内加入标签`<null/>`。
  - 正常赋值：在`<property>`标签内写一个`<bean>`，内部bean可以没有id（写了也只能在内部使用）。或者写一个`<ref bean=""/>`。
  - 赋值列表：在`<property>`标签内写一个`<list>`，在其中写内容。
- 一个beanA引用IOC容器中的其他beanB，从beanA获取的beanB和从IOC中获取的beanB是同一个实例。而且改变其中一个对象，另一个对象也会受影响。
- util名称空间创建集合类型的bean：
  - 格式：`<util:map id="">map的内容<util:map>`。id是必须的。
  - 引用该map时使用ref属性指向id。
  - 可以直接从IOC获取这个map。
  - map也可以是list等。
- 级联属性（属性的属性）赋值：
  - 使用成员运算点 . 来指定。
  - 格式：`<property name="car.price" value="">`。
  - 这样也会更改被引用的bean的值、
- 通过继承实现bean配置信息的重用：
  - 使用parent属性指定当前bean的配置继承自那个bean的配置。
  - 格式：`<bean parent="">`，然后再指定变化了的值。可以省略class属性。
  - 这样除了指定更改的值，其他的都是相同的。
- 通过abstract属性创建一个模板bean：
  - 格式：`<bean abstract=true>`。
  - 这样配置的bean是抽象的，只能被继承，不能被获取实例化。
- bean之间的依赖：
  - bean创建的顺序：在配置文件中写在前边的先创建。
  - 使用属性depends-on属性指定那几个bean先于这个bean创建。
  - 格式：`<bean depends-on="">`。
- 单例与多例：
  - 单例的对象，在容器启动完成前就被创建并存入容器中了。
  - 多例的对象，在容器启动时默认不会被创建。在获取时才创建。
- 工厂模式：
  - 工厂帮我们创建对象，有一个专门为我们创建工厂的类。
  - 静态工厂：工厂本身不用创建对象，通过静态方法调用。class指定静态方法全类名，factory-method指定工厂方法，构造方法传值。
  - 实例工厂：工厂本身需要创建对象。实例工厂创建对象时用factory-bean指定使用那个工厂，factory-method指定工厂方法。
  - `FactoryBean`是Spring定义的一个接口。
    - 这个接口的实现类都被认为是一个工厂。Spring会自动调用工厂方法创建实例。
    - 在配置文件中注册，获取的是工厂创建的对象。
    - 不管是单例多例，都只在调用的时候才创建。
- bean的后置处理器BeanPostProcess：
  - Spring的一个接口，可以在bean的初始化前后调用。默认bean有初始化方法。
  - 实现postProcessBeforeInitialization和postProcessAfterInitialization方法，返回bean对象到容器中。
  - 配置文件中注册后置处理器。
- 基于xml的自动装配：
  - bean标签下的autowire属性。
  - 默认default不自动赋值，byName表示按变量名赋值，byType表示按类型赋值，constructor表示按构造器赋值。
- SpEL表达式指定value：
  - 引用其他bean：`#{其他bean的id}`。
  - 调用静态方法：`#{T(全类名).静态方法名()}`。
  - 调用非静态方法：`#{对象名.方法名()}`。
- `@Autowired`原理：
  - 先看所要注入的类型是什么，按照类型从容器中找对应的组件。
  - 如果找到一个就直接赋值。如果没找到就报错。
  - 如果找到多个，再按照变量名作为id继续匹配。
  - 如果按照id还没有找到，需要使用`@Qualifier`注解。
  - 如果使用`@Qualifier`注解。还没有找到，使用required=false，找不到就注入null。
- 方法上有`@Autowired`，这个方法上的每个参数都会自动注入值。
- `@Autowired`和`@Resource`的区别：
  - 和`@Inject`三个都是自动注入。
  - 前者最强大，是Spring自己的，离开Spring就无法使用。
  - 后者是java的标准，扩展性更强。
- 泛型依赖注入：
  - 子类在继承时确定了泛型的类型，父类是带泛型的父类类型。
  - 带泛型的组件进行注入时，泛型也是组件类型的一部分。
- IOC的代码实现：
  - IOC是一个容器，启动时创建所有单实例对象，可以直接从容器获取到这些对象。
  - IOC容器的启动过程：
    - 调用ClassPathXMLApplicationContext的构造器。
    - 该构造方法中调用refresh()方法。
    - 该方法中创建beanFactory，解析XML文件中的配置。
    - 调用方法finishBeanFactoryInitialization()创建所有非延迟加载的单实例的bean。
    - 该方法中perInstantiateSingletons()创建单实例bean。
    - 该方法中获取所有bean的名称，按顺序循环创建bean。
    - 判断bean是工厂bean还是普通bean。getbean()方法创建bean对象。
    - 该方法中调用doGetBean()方法。
    - 该方法中调用getSingleton()方法。
    - 该方法中调用singletonFactory.getObject()通过反射创建对象。
    - 创建对象把对象放入一个map中。
  - BeanFactory与ApplicationContext的区别：
    - ApplicationContext是BeanFactory的子接口。
    - BeanFactory是bean工厂接口，负责创建bean实例，容器里的单例bean实际上是一个map。是Spring最底层的接口。
    - ApplicationContext是容器接口，更多的负责容器功能的实现，容器可以从map中获取bean。AOP和依赖注入都在这个接口下实现。是Spring提供给用户的接口。



## 2、AOP

- 面向切面编程：在程序运行期间，将某段代码动态的切入到指定方法的指定位置进行运行的编程方式。
- AOP初级配置：
  - 将目标类和切面类加入IOC容器。
  - 切面类加入`@Aspect`注解。
  - 在切面类的方法上加`@Befored`等注解，并加入切入点表达式。
- AOP的实现：动态代理的嵌套。
- AOP细节：
  1. IOC容器中保存的是组件的代理对象。
  2. 切入点表达式的通配写法。
  3. 注解配置下通知方法的执行顺序。前置-后置-返回/异常。
  4. 通知方法运行时，可以拿到目标方法的详细信息。在通知方法的参数列表上写`JointPoint joinPoint`。使用`.getArgs()`获取方法参数。`.getSignature()`获取方法签名。
  5. 在`@Befored`等注解中用returning和throwing属性来告诉Spring参数列表中的那个参数用来接收返回值和异常信息。
  6. Spring对通知方法的唯一要求是参数列表一定要正确。
  7. 注解配置时，环绕通知的各个通知都在普通通知之前运行。环绕通知捕获异常则普通通知感受不到异常。环绕通知抛出异常则普通通知可以感受到异常。
  8. 多切面运行顺序：切面1前置-切面2前置-方法运行-切面2返回-切面2后置-切面1返回-切面1后置。注解配置时，那个切面在前是按切面类首字母顺序，也可以用`@Order`指定顺序。
  9. 在xml配置时，通知顺序与文件中配置的先后有关。也可以在`<aop:aspect>`中指定order属性。
- `${}`取出配置文件中的值（`<context:property-placeholder>`标签导入），`#{}`是SpEL表达式。
- jdbcTemplate的批量插入：`.batchUpdate(sql,List<Object[]>)`，第二个参数中列表中的每个元素就是一组输入的参数。
- SQL中的具名参数：参数不是占位符？而是一个变量名。
  - 格式：在SQL语句中`:参数名`。
  - 需要使用类NamedParameterJdbcTemolate的update方法。
  - 传入的第二个参数是一个参数map，键为参数名。
  - 也可以传入一个bean对象，bean的属性值与参数名对应。
- 声明式事务：
  - 与之对应的是编程式事务，通过编写过滤器实现。
  - 声明式是指只需要告诉Spring那个方法是事务方法即可。不需要编写代码。
  - 事务管理的模式式固定的，可以通过AOP的横切关注点实现。这个已经编写好的切面就是事务管理器。
  - 配置声明式事务：
    1. 在配置文件中配置事务管理器。
    2. 开启基于注解的事务控制。
    3. 加上`@Transactional`注解。
- 声明式事务细节：
  1. `@Transactional`注解的属性。隔离级别、传播行为、只读、超时、那些异常不回滚、那些异常回滚。
  2. 运行时异常，默认都回滚。编译时异常，默认不回滚。
  3. 事务的隔离级别isolation：读未提交、读已提交、可重复读和串行化。
  4. 并发修改同一个数据会排队，等第一个事务提交后第二个才会修改。
  5. 传播行为：如果有多个事务嵌套运行，子事务是否要和大事务共用一个事务。即事务方法被另一个事务方法调用时，必须指定事务应该如何传播。
  6. 传播行为主要包括在当前事务内运行（REQUIRED）或重新创建一个事务（REQUIRES_NEW，如果已经有事务就挂起）等。
  7. REQUIRED的子事务，事务属性来源于大事务。
  8. 本类事务方法之间的调用只会产生一个事务。因为只创建了一个代理对象。
  9. IOC容器中保存的是有事务的业务逻辑组件的代理对象。
  10. 加在类上的`@Transactional`注解就表示下边的所有方法都被事务控制，但是方法上再写`@Transactional`配置会覆盖类上的设置。

