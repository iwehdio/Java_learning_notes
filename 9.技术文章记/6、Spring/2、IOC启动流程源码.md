学习自：

- [Spring IOC知识点一网打尽！](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247484247&idx=1&sn=e228e29e344559e469ac3ecfa9715217&chksm=ebd74256dca0cb40059f3f627fc9450f916c1e1b39ba741842d91774f5bb7f518063e5acf5a0&scene=21###wechat_redirect)
- [Spring IOC 容器源码分析](https://www.javadoop.com/post/spring-ioc)



## 1、对IOC的理解

- IoC(Inversion of Control)控制反转，包含了两个方面：一、控制。二、反转
  - 控制指的是：**当前对象对内部成员的控制权**。是 bean 的创建、管理的权利，控制 bean 的整个生命周期。
  - 反转指的是：这种控制权**不由当前对象管理**了，由其他(类,第三方容器)来管理。由之前的自己主动创建对象，变成现在被动接收别人给我们的对象的过程。
- 使用IOC的好处：
  1. 不用自己组装，拿来就用。
  2. 享受单例的好处，效率高，不浪费空间。
  3. 便于单元测试，方便切换mock组件。
  4. 便于进行AOP操作，对于使用者是透明的。
  5. 统一配置，便于修改。

- IOC的原理：

  - 原理就是通过Java的**反射技术**来实现的！通过反射我们可以获取类的所有信息(成员变量、类名等等等)！
  - 再通过配置文件(xml)或者注解来**描述**类与类之间的关系
  - 我们就可以通过这些配置信息和反射技术来**构建**出对应的对象和依赖关系了

  ![image-20210130160431191](http://iwehdio.gitee.io/img/my-img/21-01/image-20210130160431191.png)

  1. 根据Bean配置信息在容器内部创建Bean定义注册表
  2. 根据注册表加载、实例化bean、建立Bean与Bean之间的依赖关系
  3. 将这些准备就绪的Bean放到Map缓存池中，等待应用程序调用

- Spring容器(Bean工厂)可简单分成两种：

  - BeanFactory：是最基础、面向Spring的，可以理解为 HashMap。
  - ApplicationContext：是在BeanFactory基础之上，面向使用Spring框架的开发者。提供了一系列的功能！

- Bean的初始化过程：

  ![](http://iwehdio.gitee.io/img/my-img/21-01/image-20210130160755639.png)

  - BeanDefinitionReader**读取Resource所指向的配置文件资源**，然后解析配置文件。配置文件中每一个`<bean>`解析成一个**BeanDefinition对象**，并**保存**到BeanDefinitionRegistry中；
  - 容器扫描BeanDefinitionRegistry中的BeanDefinition；调用InstantiationStrategy**进行Bean实例化的工作**；使用**BeanWrapper完成Bean属性的设置**工作；
  - 单例Bean缓存池：Spring 在DefaultSingletonBeanRegistry类中提供了一个用于缓存单实例 Bean 的**缓存器**，它是一个用HashMap实现的缓存器，单实例的Bean**以beanName为键保存在这个HashMap**中

## 2、bean容器的启动流程

![image-20210131175707246](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131175707246.png)

![image-20210131175721716](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131175721716.png)

![image-20210131175737688](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131175737688.png)

![image-20210131175751227](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131175751227.png)

![image-20210131175801624](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131175801624.png)

![image-20210131175814630](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131175814630.png)

![image-20210131175825837](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131175825837.png)

![image-20210131175834812](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131175834812.png)

![image-20210131175852591](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131175852591.png)

![image-20210131175909584](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131175909584.png)

![image-20210131175917713](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131175917713.png)

![image-20210131175934562](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131175934562.png)

![](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131175947135.png)

![image-20210131180001199](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131180001199.png)

![image-20210131180018620](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131180018620.png)

![image-20210131180028138](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131180028138.png)

![image-20210131180044291](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131180044291.png)

![image-20210131180052056](http://iwehdio.gitee.io/img/my-img/21-01/image-20210131180052056.png)



## 3、bean容器的启动

### 体系结构

- 引入Spring依赖：

  - spring-context 会自动将 spring-core、spring-beans、spring-aop、spring-expression 这几个基础 jar 包带进来。

  ```xml
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>4.3.11.RELEASE</version>
  </dependency>
  ```

- 最基本的启动 Spring 容器的例子：

  ```java
  ApplicationContext context = new ClassPathXmlApplicationContext("classpath:applicationfile.xml");
  ```

- 这一行，就是在 ClassPath 中寻找 xml 配置文件，根据 xml 文件内容来构建 ApplicationContext。当然，除了 ClassPathXmlApplicationContext 以外，也还有其他构建 ApplicationContext 的方案可供选择，先来看看大体的继承结构是怎么样的：

  ![image-20210130162315740](http://iwehdio.gitee.io/img/my-img/21-01/image-20210130162315740.png)

  **1、FileSystemXmlApplicationContext** 的构造函数需要一个 xml 配置文件在系统中的路径，其他和 ClassPathXmlApplicationContext 基本上一样。

  **2、AnnotationConfigApplicationContext** 是基于注解来使用的，它不需要配置文件，采用 java 配置类和各种注解来配置，是比较简单的方式，也是大势所趋吧。

- BeanFactory：

  - 从名字上也很好理解，生产 bean 的工厂，它负责生产和管理各个 bean 实例。

  ![image-20210130162709798](http://iwehdio.gitee.io/img/my-img/21-01/image-20210130162709798.png)

  - ApplicationContext 继承了 ListableBeanFactory，这个 Listable 的意思就是，通过这个接口，我们可以获取多个 Bean，大家看源码会发现，最顶层 BeanFactory 接口的方法都是获取单个 Bean 的。
  - ApplicationContext 继承了 HierarchicalBeanFactory，Hierarchical （层次结构）单词本身已经能说明问题了，也就是说我们可以在应用中起多个 BeanFactory，然后可以将各个 BeanFactory 设置为父子关系。
  - AutowireCapableBeanFactory 这个名字中的 Autowire 大家都非常熟悉，它就是用来自动装配 Bean 用的，ApplicationContext 并没有继承它，不过不用担心，不使用继承，不代表不可以使用组合，如果你看到 ApplicationContext 接口定义中的最后一个方法 getAutowireCapableBeanFactory() 就知道了。
  - ConfigurableListableBeanFactory 也是一个特殊的接口，特殊之处在于它继承了第二层所有的三个接口，而 ApplicationContext 没有。

### 准备工作

- 第一步：ClassPathXmlApplicationContext 的构造方法说起。

  - 调用的是第82行的构造方法。
  -  `refresh()`是核心方法。为什么是叫 refresh()，而不是 init() 这种名字的方法。因为 ApplicationContext 建立起来以后，其实我们是可以通过调用 refresh() 这个方法重建的，refresh() 会将原来的 ApplicationContext 销毁，然后再重新执行一次初始化操作。

  ```java
  public class ClassPathXmlApplicationContext extends AbstractXmlApplicationContext {
    private Resource[] configResources;
  
    // 如果已经有 ApplicationContext 并需要配置成父子关系，那么调用这个构造方法
    public ClassPathXmlApplicationContext(ApplicationContext parent) {
      super(parent);
    }
    ...
    //传入配置文件的位置，第一次启动容器，调用这个方法，读取配置文件，创建并刷新上下文
    public ClassPathXmlApplicationContext(String configLocation) throws BeansException {
  		this(new String[] {configLocation}, true, null);
    }
      
    public ClassPathXmlApplicationContext(String[] configLocations, boolean refresh, ApplicationContext parent)
        throws BeansException {
  
      super(parent);
      // 根据提供的路径，处理成配置文件数组(以分号、逗号、空格、tab、换行符分割)
      setConfigLocations(configLocations);
      if (refresh) {
        refresh(); // 核心方法
      }
    }
      ...
  }
  ```

- AbstractApplicationContext中，`refresh()`方法的大致流程：

  ```java
  @Override
  public void refresh() throws BeansException, IllegalStateException {
     // 来个锁，不然 refresh() 还没结束，你又来个启动或销毁容器的操作，那不就乱套了嘛
     synchronized (this.startupShutdownMonitor) {
  
        // 准备工作，记录下容器的启动时间、标记“已启动”状态、处理配置文件中的占位符
        prepareRefresh();
  
        // 这步比较关键，这步完成后，配置文件就会解析成一个个 Bean 定义，注册到 BeanFactory 中，
        // 当然，这里说的 Bean 还没有初始化，只是配置信息都提取出来了，
        // 注册也只是将这些信息都保存到了注册中心(说到底核心是一个 beanName-> beanDefinition 的 map)
        ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();
  
        // 设置 BeanFactory 的类加载器，添加几个 BeanPostProcessor，手动注册几个特殊的 bean
        // 这块待会会展开说
        prepareBeanFactory(beanFactory);
  
        try {
           // 【这里需要知道 BeanFa ctoryPostProcessor 这个知识点，Bean 如果实现了此接口，
           // 那么在容器初始化以后，Spring 会负责调用里面的 postProcessBeanFactory 方法。】
  
           // 这里是提供给子类的扩展点，到这里的时候，所有的 Bean 都加载、注册完成了，但是都还没有初始化
           // 具体的子类可以在这步的时候添加一些特殊的 BeanFactoryPostProcessor 的实现类或做点什么事
           postProcessBeanFactory(beanFactory);
           // 调用 BeanFactoryPostProcessor 各个实现类的 postProcessBeanFactory(factory) 方法
           invokeBeanFactoryPostProcessors(beanFactory);
  
           // 注册 BeanPostProcessor 的实现类，注意看和 BeanFactoryPostProcessor 的区别
           // 此接口两个方法: postProcessBeforeInitialization 和 postProcessAfterInitialization
           // 两个方法分别在 Bean 初始化之前和初始化之后得到执行。注意，到这里 Bean 还没初始化
           registerBeanPostProcessors(beanFactory);
  
           // 初始化当前 ApplicationContext 的 MessageSource，国际化这里就不展开说了，不然没完没了了
           initMessageSource();
  
           // 初始化当前 ApplicationContext 的事件广播器，这里也不展开了
           initApplicationEventMulticaster();
  
           // 从方法名就可以知道，典型的模板方法(钩子方法)，
           // 具体的子类可以在这里初始化一些特殊的 Bean（在初始化 singleton beans 之前）
           onRefresh();
  
           // 注册事件监听器，监听器需要实现 ApplicationListener 接口。这也不是我们的重点，过
           registerListeners();
  
           // 重点，重点，重点
           // 初始化所有的 singleton beans
           //（lazy-init 的除外）
           finishBeanFactoryInitialization(beanFactory);
  
           // 最后，广播事件，ApplicationContext 初始化完成
           finishRefresh();
        }
  
        catch (BeansException ex) {
           if (logger.isWarnEnabled()) {
              logger.warn("Exception encountered during context initialization - " +
                    "cancelling refresh attempt: " + ex);
           }
  
           // 销毁已经初始化的 singleton 的 Beans，以免有些 bean 会一直占用资源
           destroyBeans();
  
           // 重置活动标志ex
           cancelRefresh(ex);
  
           // 把异常往外抛
           throw ex;
        }
  
        finally {
           //重置Spring中的公共部分缓存，因为我们可能不再需要单例bean的单例数据了
           resetCommonCaches();
        }
     }
  }
  ```

  

- 创建Bean容器前的准备工作：AbstractApplicationContext中的`prepareRefresh()`。

  ```java
  protected void prepareRefresh() {
     // 记录启动时间，
     // 将 active 属性设置为 true，closed 属性设置为 false，它们都是 AtomicBoolean 类型
     this.startupDate = System.currentTimeMillis();
     this.closed.set(false);
     this.active.set(true);
  
     if (logger.isInfoEnabled()) {
        logger.info("Refreshing " + this);
     }
  
     // 初始化上下文环境中的占位符属性源
     initPropertySources();
  
     // 校验 xml 配置文件
     getEnvironment().validateRequiredProperties();
  
     this.earlyApplicationEvents = new LinkedHashSet<ApplicationEvent>();
  }
  ```

### 解析xml文件，把BeanDefinition注册到BeanFactory并返回

- 创建 Bean 容器，加载并注册 Bean：AbstractApplicationContext中的`obtainFreshBeanFactory()`。

  - **这个方法是全文最重要的部分之一**，这里将会初始化 BeanFactory、加载 Bean、注册 Bean 等等。
  - 当然，这步结束后，Bean 并没有完成初始化。这里指的是 Bean 实例并未在这一步生成。

  ```java
  protected ConfigurableListableBeanFactory obtainFreshBeanFactory() {
     // 关闭旧的 BeanFactory (如果有)，创建新的 BeanFactory，加载 Bean 定义、注册 Bean 等等
     refreshBeanFactory();
  
     // 返回刚刚创建的 BeanFactory
     ConfigurableListableBeanFactory beanFactory = getBeanFactory();
     if (logger.isDebugEnabled()) {
        logger.debug("Bean factory for " + getDisplayName() + ": " + beanFactory);
     }
     return beanFactory;
  }
  ```

  - `refreshBeanFactory()`方法在AbstractApplicationContext中是抽象的，其子类AbstractRefreshableApplicationContext对其进行了实现：

    ```java
    @Override
    protected final void refreshBeanFactory() throws BeansException {
       // 如果 ApplicationContext 中已经加载过 BeanFactory 了，销毁所有 Bean，关闭 BeanFactory
       // 注意，应用中 BeanFactory 本来就是可以多个的，这里可不是说应用全局是否有 BeanFactory，而是当前
       // ApplicationContext 是否有 BeanFactory
       if (hasBeanFactory()) {
          destroyBeans();
          closeBeanFactory();
       }
       try {
          // 初始化一个 DefaultListableBeanFactory，为什么用这个，我们马上说。
          DefaultListableBeanFactory beanFactory = createBeanFactory();
          // 用于 BeanFactory 的序列化，我想大部分人应该都用不到
          beanFactory.setSerializationId(getId());
    
          // 下面这两个方法很重要，别跟丢了，具体细节之后说
          // 设置 BeanFactory 的两个配置属性：是否允许 Bean 覆盖、是否允许循环引用
          customizeBeanFactory(beanFactory);
    
          // 加载 Bean 到 BeanFactory 中
          loadBeanDefinitions(beanFactory);
          synchronized (this.beanFactoryMonitor) {
             this.beanFactory = beanFactory;
          }
       }
       catch (IOException ex) {
          throw new ApplicationContextException("I/O error parsing bean definition source for " + getDisplayName(), ex);
       }
    }
    ```

  - ApplicationContext 继承自 BeanFactory，但是它不应该被理解为 BeanFactory 的实现类，而是说其内部持有一个实例化的 BeanFactory（DefaultListableBeanFactory）。以后所有的 BeanFactory 相关的操作其实是委托给这个实例来处理的。

  - 为什么选择实例化 **DefaultListableBeanFactory** ？

    - 因为其是继承体系中最底层的，功能最强大的。
    - 继承自DefaultSingletonBeanRegistry单例池注册器，可以获取池中的单例bean。
    - 实现了BeanFactory，可以对外提供getBean方法。

- BeanDefinition：

  - BeanFactory 是 Bean 容器，那么 Bean 又是什么呢？
    - BeanDefinition 就是我们所说的 Spring 的 Bean，我们自己定义的各个 Bean 其实会转换成一个个 BeanDefinition 存在于 Spring 的 BeanFactory 中。
    - BeanDefinition 中保存了我们的 Bean 信息，比如这个 Bean 指向的是哪个类、是否是单例的、是否懒加载、这个 Bean 依赖了哪些 Bean 等等。
  - BeanDefinition 的接口定义：

  ```java
  public interface BeanDefinition extends AttributeAccessor, BeanMetadataElement {
  
     // 我们可以看到，默认只提供 sington 和 prototype 两种，
     // 很多读者可能知道还有 request, session, globalSession, application, websocket 这几种，
     // 不过，它们属于基于 web 的扩展。
     String SCOPE_SINGLETON = ConfigurableBeanFactory.SCOPE_SINGLETON;
     String SCOPE_PROTOTYPE = ConfigurableBeanFactory.SCOPE_PROTOTYPE;
  
     // 比较不重要，直接跳过吧
     int ROLE_APPLICATION = 0;
     int ROLE_SUPPORT = 1;
     int ROLE_INFRASTRUCTURE = 2;
  
     // 设置父 Bean，这里涉及到 bean 继承，不是 java 继承。请参见附录的详细介绍
     // 一句话就是：继承父 Bean 的配置信息而已
     void setParentName(String parentName);
  
     // 获取父 Bean
     String getParentName();
  
     // 设置 Bean 的类名称，将来是要通过反射来生成实例的
     void setBeanClassName(String beanClassName);
  
     // 获取 Bean 的类名称
     String getBeanClassName();
  
  
     // 设置 bean 的 scope
     void setScope(String scope);
  
     String getScope();
  
     // 设置是否懒加载
     void setLazyInit(boolean lazyInit);
  
     boolean isLazyInit();
  
     // 设置该 Bean 依赖的所有的 Bean，注意，这里的依赖不是指属性依赖(如 @Autowire 标记的)，
     // 是 depends-on="" 属性设置的值。
     void setDependsOn(String... dependsOn);
  
     // 返回该 Bean 的所有依赖
     String[] getDependsOn();
  
     // 设置该 Bean 是否可以注入到其他 Bean 中，只对根据类型注入有效，
     // 如果根据名称注入，即使这边设置了 false，也是可以的
     void setAutowireCandidate(boolean autowireCandidate);
  
     // 该 Bean 是否可以注入到其他 Bean 中
     boolean isAutowireCandidate();
  
     // 主要的。同一接口的多个实现，如果不指定名字的话，Spring 会优先选择设置 primary 为 true 的 bean
     void setPrimary(boolean primary);
  
     // 是否是 primary 的
     boolean isPrimary();
  
     // 如果该 Bean 采用工厂方法生成，指定工厂名称。对工厂不熟悉的读者，请参加附录
     // 一句话就是：有些实例不是用反射生成的，而是用工厂模式生成的
     void setFactoryBeanName(String factoryBeanName);
     // 获取工厂名称
     String getFactoryBeanName();
     // 指定工厂类中的 工厂方法名称
     void setFactoryMethodName(String factoryMethodName);
     // 获取工厂类中的 工厂方法名称
     String getFactoryMethodName();
  
     // 构造器参数
     ConstructorArgumentValues getConstructorArgumentValues();
  
     // Bean 中的属性值，后面给 bean 注入属性值的时候会说到
     MutablePropertyValues getPropertyValues();
  
     // 是否 singleton
     boolean isSingleton();
  
     // 是否 prototype
     boolean isPrototype();
  
     // 如果这个 Bean 是被设置为 abstract，那么不能实例化，
     // 常用于作为 父bean 用于继承，其实也很少用......
     boolean isAbstract();
  
     int getRole();
     String getDescription();
     String getResourceDescription();
     BeanDefinition getOriginatingBeanDefinition();
  }
  ```

- 再往下看 refreshBeanFactory() 方法中的剩余部分：

  ```java
  customizeBeanFactory(beanFactory);
  loadBeanDefinitions(beanFactory);
  ```

- AbstractRefreshableApplicationContext中的`customizeBeanFactory(beanFactory)`：

  - 就是配置是否允许 BeanDefinition 覆盖、是否允许循环引用。

  ```java
  protected void customizeBeanFactory(DefaultListableBeanFactory beanFactory) {
     if (this.allowBeanDefinitionOverriding != null) {
        // 是否允许 Bean 定义覆盖
        beanFactory.setAllowBeanDefinitionOverriding(this.allowBeanDefinitionOverriding);
     }
     if (this.allowCircularReferences != null) {
        // 是否允许 Bean 间的循环依赖
        beanFactory.setAllowCircularReferences(this.allowCircularReferences);
     }
  }
  ```

  - BeanDefinition 的覆盖问题:就是在配置文件中定义 bean 时使用了相同的 id 或 name，默认情况下，allowBeanDefinitionOverriding 属性为 null，如果在同一配置文件中重复了，会抛错，但是如果不是同一配置文件中，会发生覆盖。
  - 循环引用就是：A 依赖 B，而 B 依赖 A。或 A 依赖 B，B 依赖 C，而 C 依赖 A。默认情况下，Spring 允许循环依赖，当然如果你在 A 的构造方法中依赖 B，在 B 的构造方法中依赖 A 是不行的。

- ` loadBeanDefinitions(beanFactory)`在AbstractRefreshableApplicationContext是抽象的，具体的实现在 AbstractXmlApplicationContext中：

  - 根据配置，加载各个 Bean，然后放到 BeanFactory 中。
  - 读取配置的操作在 XmlBeanDefinitionReader 中，其负责加载配置、解析。

  ```java
  /** 我们可以看到，此方法将通过一个 XmlBeanDefinitionReader 实例来加载各个 Bean。*/
  @Override
  protected void loadBeanDefinitions(DefaultListableBeanFactory beanFactory) throws BeansException, IOException {
     // 给这个 BeanFactory 实例化一个 XmlBeanDefinitionReader
     XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(beanFactory);
  
     // 在这个上下文的源加载环境中配置 bean definition reader
     beanDefinitionReader.setEnvironment(this.getEnvironment());
     beanDefinitionReader.setResourceLoader(this);
     beanDefinitionReader.setEntityResolver(new ResourceEntityResolver(this));
  
     // 初始化 BeanDefinitionReader，其实这个是提供给子类覆写的，
     // 我看了一下，没有类覆写这个方法，我们姑且当做不重要吧
     initBeanDefinitionReader(beanDefinitionReader);
     // 重点来了，继续往下
     loadBeanDefinitions(beanDefinitionReader);
  }
  ```

- AbstractXmlApplicationContext中的`loadBeanDefinitions(beanDefinitionReader)`：

  - 作用：用刚刚初始化的 Reader 开始来加载 xml 配置，将一个配置文件转换为一颗 DOM 树。
  - 在转换完毕后，调用`doRegisterBeanDefinitions(root)`从 xml 根节点开始解析文件。
  - 调用DefaultBeanDefinitionDocumentReader中的`parseBeanDefinitions(root, this.delegate)`方法解析xml文件中的标签。

- `parseBeanDefinitions(root, this.delegate) `：

  - 核心解析方法。

  ```java
  // default namespace 涉及到的就四个标签 <import />、<alias />、<bean /> 和 <beans />，
  // 其他的属于 custom 的
  protected void parseBeanDefinitions(Element root, BeanDefinitionParserDelegate delegate) {
     if (delegate.isDefaultNamespace(root)) {
        NodeList nl = root.getChildNodes();
        for (int i = 0; i < nl.getLength(); i++) {
           Node node = nl.item(i);
           if (node instanceof Element) {
              Element ele = (Element) node;
              if (delegate.isDefaultNamespace(ele)) {
                 // 解析 default namespace 下面的几个元素
                 parseDefaultElement(ele, delegate);
              }
              else {
                 // 解析其他 namespace 的元素
                 delegate.parseCustomElement(ele);
              }
           }
        }
     }
     else {
        delegate.parseCustomElement(root);
     }
  }
  ```

  - 对于每个配置来说，分别进入到 parseDefaultElement(ele, delegate); 和 delegate.parseCustomElement(ele); 这两个分支了。

  - parseDefaultElement(ele, delegate) 代表解析的节点是 `<import />`、`<alias />`、`<bean />`、`<beans />` 这几个。

  - 这里的四个标签之所以是 **default** 的，是因为它们是处于这个 namespace 下定义的：

    ```http
    http://www.springframework.org/schema/beans
    ```

  - 而对于其他的标签，将进入到 delegate.parseCustomElement(element) 这个分支。如我们经常会使用到的 `<mvc />`、`<task />`、`<context />`、`<aop />`等。这些属于扩展，如果需要使用上面这些 ”非 default“ 标签，那么上面的 xml 头部的地方也要引入相应的 namespace 和 .xsd 文件的路径。同时代码中需要提供相应的 parser 来解析，如 MvcNamespaceHandler、TaskNamespaceHandler、ContextNamespaceHandler、AopNamespaceHandler 等。

- DefaultBeanDefinitionDocumentReader中的`parseDefaultElement(ele, delegate)`方法：

  - 处理default标签。

  ```java
  private void parseDefaultElement(Element ele, BeanDefinitionParserDelegate delegate) {
     if (delegate.nodeNameEquals(ele, IMPORT_ELEMENT)) {
        // 处理 <import /> 标签
        importBeanDefinitionResource(ele);
     }
     else if (delegate.nodeNameEquals(ele, ALIAS_ELEMENT)) {
        // 处理 <alias /> 标签定义
        // <alias name="fromName" alias="toName"/>
        processAliasRegistration(ele);
     }
     else if (delegate.nodeNameEquals(ele, BEAN_ELEMENT)) {
        // 处理 <bean /> 标签定义，这也算是我们的重点吧
        processBeanDefinition(ele, delegate);
     }
     else if (delegate.nodeNameEquals(ele, NESTED_BEANS_ELEMENT)) {
        // 如果碰到的是嵌套的 <beans /> 标签，需要递归
        doRegisterBeanDefinitions(ele);
     }
  }
  ```

- DefaultBeanDefinitionDocumentReader中的`processBeanDefinition(ele, delegate)`：

  - 以解析 `<bean />` 标签为例。

  ```java
  protected void processBeanDefinition(Element ele, BeanDefinitionParserDelegate delegate) {
     // 将 <bean /> 节点中的信息提取出来，然后封装到一个 BeanDefinitionHolder 中，细节往下看
     BeanDefinitionHolder bdHolder = delegate.parseBeanDefinitionElement(ele);
  
     // 下面的几行先不要看，跳过先，跳过先，跳过先，后面会继续说的
  
     if (bdHolder != null) {
        bdHolder = delegate.decorateBeanDefinitionIfRequired(ele, bdHolder);
        try {
           // 注册bean
           BeanDefinitionReaderUtils.registerBeanDefinition(bdHolder, getReaderContext().getRegistry());
        }
        catch (BeanDefinitionStoreException ex) {
           getReaderContext().error("Failed to register bean definition with name '" +
                 bdHolder.getBeanName() + "'", ele, ex);
        }
        // 发送注册事件给监听器
        getReaderContext().fireComponentRegistered(new BeanComponentDefinition(bdHolder));
     }
  }
  ```

- BeanDefinitionParserDelegate中的`parseBeanDefinitionElement(Element ele)`解析`<bean>`标签：

  - 解析出`<bean>`标签中的各个属性，如类名，id、作用域、注入方式等。

  ```java
  public BeanDefinitionHolder parseBeanDefinitionElement(Element ele) {
      return parseBeanDefinitionElement(ele, null);
  }
  
  public BeanDefinitionHolder parseBeanDefinitionElement(Element ele, BeanDefinition containingBean) {
     String id = ele.getAttribute(ID_ATTRIBUTE);
     String nameAttr = ele.getAttribute(NAME_ATTRIBUTE);
  
     List<String> aliases = new ArrayList<String>();
  
     // 将 name 属性的定义按照 “逗号、分号、空格” 切分，形成一个 别名列表数组，
     // 当然，如果你不定义 name 属性的话，就是空的了
  
     if (StringUtils.hasLength(nameAttr)) {
        String[] nameArr = StringUtils.tokenizeToStringArray(nameAttr, MULTI_VALUE_ATTRIBUTE_DELIMITERS);
        aliases.addAll(Arrays.asList(nameArr));
     }
  
     String beanName = id;
     // 如果没有指定id, 那么用别名列表的第一个名字作为beanName
     if (!StringUtils.hasText(beanName) && !aliases.isEmpty()) {
        beanName = aliases.remove(0);
        if (logger.isDebugEnabled()) {
           logger.debug("No XML 'id' specified - using '" + beanName +
                 "' as bean name and " + aliases + " as aliases");
        }
     }
  
     if (containingBean == null) {
        checkNameUniqueness(beanName, aliases, ele);
     }
  
     // 根据 <bean ...>...</bean> 中的配置创建 BeanDefinition，然后把配置中的信息都设置到实例中,
     // 细节后面细说，先知道下面这行结束后，一个 BeanDefinition 实例就出来了。
     AbstractBeanDefinition beanDefinition = parseBeanDefinitionElement(ele, beanName, containingBean);
  
     // 到这里，整个 <bean /> 标签就算解析结束了，一个 BeanDefinition 就形成了。
     if (beanDefinition != null) {
        // 如果都没有设置 id 和 name，那么此时的 beanName 就会为 null，进入下面这块代码产生
        // 如果读者不感兴趣的话，我觉得不需要关心这块代码，对本文源码分析来说，这些东西不重要
        if (!StringUtils.hasText(beanName)) {
           try {
              if (containingBean != null) {// 按照我们的思路，这里 containingBean 是 null 的
                 beanName = BeanDefinitionReaderUtils.generateBeanName(
                       beanDefinition, this.readerContext.getRegistry(), true);
              }
              else {
                 // 如果我们不定义 id 和 name，那么我们引言里的那个例子：
                 //   1. beanName 为：com.javadoop.example.MessageServiceImpl#0
                 //   2. beanClassName 为：com.javadoop.example.MessageServiceImpl
  
                 beanName = this.readerContext.generateBeanName(beanDefinition);
  
                 String beanClassName = beanDefinition.getBeanClassName();
                 if (beanClassName != null &&
                       beanName.startsWith(beanClassName) && beanName.length() > beanClassName.length() &&
                       !this.readerContext.getRegistry().isBeanNameInUse(beanClassName)) {
                    // 把 beanClassName 设置为 Bean 的别名
                    aliases.add(beanClassName);
                 }
              }
              if (logger.isDebugEnabled()) {
                 logger.debug("Neither XML 'id' nor 'name' specified - " +
                       "using generated bean name [" + beanName + "]");
              }
           }
           catch (Exception ex) {
              error(ex.getMessage(), ele);
              return null;
           }
        }
        String[] aliasesArray = StringUtils.toStringArray(aliases);
        // 返回 BeanDefinitionHolder
        return new BeanDefinitionHolder(beanDefinition, beanName, aliasesArray);
     }
  
     return null;
  }
  
  public AbstractBeanDefinition parseBeanDefinitionElement(
        Element ele, String beanName, BeanDefinition containingBean) {
  
     this.parseState.push(new BeanEntry(beanName));
  
     String className = null;
     if (ele.hasAttribute(CLASS_ATTRIBUTE)) {
        className = ele.getAttribute(CLASS_ATTRIBUTE).trim();
     }
  
     try {
        String parent = null;
        if (ele.hasAttribute(PARENT_ATTRIBUTE)) {
           parent = ele.getAttribute(PARENT_ATTRIBUTE);
        }
        // 创建 BeanDefinition，然后设置类信息而已，很简单，就不贴代码了
        AbstractBeanDefinition bd = createBeanDefinition(className, parent);
  
        // 设置 BeanDefinition 的一堆属性，这些属性定义在 AbstractBeanDefinition 中
        parseBeanDefinitionAttributes(ele, beanName, containingBean, bd);
        bd.setDescription(DomUtils.getChildElementValueByTagName(ele, DESCRIPTION_ELEMENT));
  
        /**
         * 下面的一堆是解析 <bean>......</bean> 内部的子元素，
         * 解析出来以后的信息都放到 bd 的属性中
         */
  
        // 解析 <meta />
        parseMetaElements(ele, bd);
        // 解析 <lookup-method />
        parseLookupOverrideSubElements(ele, bd.getMethodOverrides());
        // 解析 <replaced-method />
        parseReplacedMethodSubElements(ele, bd.getMethodOverrides());
      // 解析 <constructor-arg />
        parseConstructorArgElements(ele, bd);
        // 解析 <property />
        parsePropertyElements(ele, bd);
        // 解析 <qualifier />
        parseQualifierElements(ele, bd);
  
        bd.setResource(this.readerContext.getResource());
        bd.setSource(extractSource(ele));
  
        return bd;
     }
     catch (ClassNotFoundException ex) {
        error("Bean class [" + className + "] not found", ele, ex);
     }
     catch (NoClassDefFoundError err) {
        error("Class that bean class [" + className + "] depends on not found", ele, err);
     }
     catch (Throwable ex) {
        error("Unexpected failure during bean definition parsing", ele, ex);
     }
     finally {
        this.parseState.pop();
     }
  
     return null;
  }
  ```

  - 到这里，我们已经完成了根据 `<bean />` 配置创建了一个 BeanDefinitionHolder 实例。注意，是一个。回到解析 `<bean />` 的入口方法：DefaultBeanDefinitionDocumentReader中的`processBeanDefinition(ele, delegate)`。

  ```java
  protected void processBeanDefinition(Element ele, BeanDefinitionParserDelegate delegate) {
     // 将 <bean /> 节点转换为 BeanDefinitionHolder，就是上面说的一堆
     BeanDefinitionHolder bdHolder = delegate.parseBeanDefinitionElement(ele);
     if (bdHolder != null) {
        // 如果有自定义属性的话，进行相应的解析，先忽略
        bdHolder = delegate.decorateBeanDefinitionIfRequired(ele, bdHolder);
        try {
           // 我们把这步叫做 注册Bean 吧
           BeanDefinitionReaderUtils.registerBeanDefinition(bdHolder, getReaderContext().getRegistry());
        }
        catch (BeanDefinitionStoreException ex) {
           getReaderContext().error("Failed to register bean definition with name '" +
                 bdHolder.getBeanName() + "'", ele, ex);
        }
        // 注册完成后，发送事件，本文不展开说这个
        getReaderContext().fireComponentRegistered(new BeanComponentDefinition(bdHolder));
     }
  }
  ```

  - 在第一行代码，根据一个 `<bean />` 标签产生了一个 BeanDefinitionHolder 的实例（也就是之前分析的解析流程）。

  ```java
  public class BeanDefinitionHolder implements BeanMetadataElement {
  
    private final BeanDefinition beanDefinition;
  
    private final String beanName;
  
    private final String[] aliases;
  ...
  ```

- BeanDefinitionReaderUtils中的`registerBeanDefinition`：

  - 尝试对bean进行注册，如果有别名的话把别名也都注册一遍。

  ```java
  public static void registerBeanDefinition(
        BeanDefinitionHolder definitionHolder, BeanDefinitionRegistry registry)
        throws BeanDefinitionStoreException {
  
     String beanName = definitionHolder.getBeanName();
     // 注册这个 Bean
     registry.registerBeanDefinition(beanName, definitionHolder.getBeanDefinition());
  
     // 如果还有别名的话，也要根据别名全部注册一遍，不然根据别名就会找不到 Bean 了
     String[] aliases = definitionHolder.getAliases();
     if (aliases != null) {
        for (String alias : aliases) {
           // alias -> beanName 保存它们的别名信息，这个很简单，用一个 map 保存一下就可以了，
           // 获取的时候，会先将 alias 转换为 beanName，然后再查找
           registry.registerAlias(beanName, alias);
        }
     }
  }
  ```

- DefaultListableBeanFactory中的`registerBeanDefinition`：

  - 注册 BeanDefinition 到注册中心。

  ```java
  @Override
  public void registerBeanDefinition(String beanName, BeanDefinition beanDefinition)
        throws BeanDefinitionStoreException {
  
     Assert.hasText(beanName, "Bean name must not be empty");
     Assert.notNull(beanDefinition, "BeanDefinition must not be null");
  
     if (beanDefinition instanceof AbstractBeanDefinition) {
        try {
           ((AbstractBeanDefinition) beanDefinition).validate();
        }
        catch (BeanDefinitionValidationException ex) {
           throw new BeanDefinitionStoreException(...);
        }
     }
  
     // old? 还记得 “允许 bean 覆盖” 这个配置吗？allowBeanDefinitionOverriding
     BeanDefinition oldBeanDefinition;
  
     // 之后会看到，所有的 Bean 注册后会放入这个 beanDefinitionMap 中
     oldBeanDefinition = this.beanDefinitionMap.get(beanName);
  
     // 处理重复名称的 Bean 定义的情况
     if (oldBeanDefinition != null) {
        if (!isAllowBeanDefinitionOverriding()) {
           // 如果不允许覆盖的话，抛异常
           throw new BeanDefinitionStoreException(beanDefinition.getResourceDescription()...
        }
        else if (oldBeanDefinition.getRole() < beanDefinition.getRole()) {
           // log...用框架定义的 Bean 覆盖用户自定义的 Bean 
        }
        else if (!beanDefinition.equals(oldBeanDefinition)) {
           // log...用新的 Bean 覆盖旧的 Bean
        }
        else {
           // log...用同等的 Bean 覆盖旧的 Bean，这里指的是 equals 方法返回 true 的 Bean
        }
        // 覆盖
        this.beanDefinitionMap.put(beanName, beanDefinition);
     }
     else {
        // 判断是否已经有其他的 Bean 开始初始化了.
        // 注意，"注册Bean" 这个动作结束，Bean 依然还没有初始化，我们后面会有大篇幅说初始化过程，
        // 在 Spring 容器启动的最后，会 预初始化 所有的 singleton beans
        if (hasBeanCreationStarted()) {
           // Cannot modify startup-time collection elements anymore (for stable iteration)
           synchronized (this.beanDefinitionMap) {
              this.beanDefinitionMap.put(beanName, beanDefinition);
              List<String> updatedDefinitions = new ArrayList<String>(this.beanDefinitionNames.size() + 1);
              updatedDefinitions.addAll(this.beanDefinitionNames);
              updatedDefinitions.add(beanName);
              this.beanDefinitionNames = updatedDefinitions;
              if (this.manualSingletonNames.contains(beanName)) {
                 Set<String> updatedSingletons = new LinkedHashSet<String>(this.manualSingletonNames);
                 updatedSingletons.remove(beanName);
                 this.manualSingletonNames = updatedSingletons;
              }
           }
        }
        else {
           // 最正常的应该是进到这个分支。
  
           // 将 BeanDefinition 放到这个 map 中，这个 map 保存了所有的 BeanDefinition
           this.beanDefinitionMap.put(beanName, beanDefinition);
           // 这是个 ArrayList，所以会按照 bean 配置的顺序保存每一个注册的 Bean 的名字
           this.beanDefinitionNames.add(beanName);
           // 这是个 LinkedHashSet，代表的是手动注册的 singleton bean，
           // 注意这里是 remove 方法，到这里的 Bean 当然不是手动注册的
           // 手动指的是通过调用以下方法注册的 bean ：
           //     registerSingleton(String beanName, Object singletonObject)
           // 这不是重点，解释只是为了不让大家疑惑。Spring 会在后面"手动"注册一些 Bean，
           // 如 "environment"、"systemProperties" 等 bean，我们自己也可以在运行时注册 Bean 到容器中的
           this.manualSingletonNames.remove(beanName);
        }
        // 这个不重要，在预初始化的时候会用到，不必管它。
        this.frozenBeanDefinitionNames = null;
     }
  
     if (oldBeanDefinition != null || containsSingleton(beanName)) {
        resetBeanDefinition(beanName);
     }
  }
  ```

- 这样`refresh()`方法中的`obtainFreshBeanFactory()`就完成了，返回了一个beanFactory。

- 接下来就是准备bean容器。AbstractApplicationContext中的`prepareBeanFactory(beanFactory)`：

  - 设置类加载器和后置处理器，“手动”注册一些特殊的bean。

  ```java
  protected void prepareBeanFactory(ConfigurableListableBeanFactory beanFactory) {
     // 设置 BeanFactory 的类加载器，我们知道 BeanFactory 需要加载类，也就需要类加载器，
     // 这里设置为加载当前 ApplicationContext 类的类加载器
     beanFactory.setBeanClassLoader(getClassLoader());
  
     // 设置 BeanExpressionResolver
     beanFactory.setBeanExpressionResolver(new StandardBeanExpressionResolver(beanFactory.getBeanClassLoader()));
     // 
     beanFactory.addPropertyEditorRegistrar(new ResourceEditorRegistrar(this, getEnvironment()));
  
     // 添加一个 BeanPostProcessor，这个 processor 比较简单：
     // 实现了 Aware 接口的 beans 在初始化的时候，这个 processor 负责回调，
     // 这个我们很常用，如我们会为了获取 ApplicationContext 而 implement ApplicationContextAware
     // 注意：它不仅仅回调 ApplicationContextAware，
     //   还会负责回调 EnvironmentAware、ResourceLoaderAware 等，看下源码就清楚了
     beanFactory.addBeanPostProcessor(new ApplicationContextAwareProcessor(this));
  
     // 下面几行的意思就是，如果某个 bean 依赖于以下几个接口的实现类，在自动装配的时候忽略它们，
     // Spring 会通过其他方式来处理这些依赖。
     beanFactory.ignoreDependencyInterface(EnvironmentAware.class);
     beanFactory.ignoreDependencyInterface(EmbeddedValueResolverAware.class);
     beanFactory.ignoreDependencyInterface(ResourceLoaderAware.class);
     beanFactory.ignoreDependencyInterface(ApplicationEventPublisherAware.class);
     beanFactory.ignoreDependencyInterface(MessageSourceAware.class);
     beanFactory.ignoreDependencyInterface(ApplicationContextAware.class);
  
     /**
      * 下面几行就是为特殊的几个 bean 赋值，如果有 bean 依赖了以下几个，会注入这边相应的值，
      * 之前我们说过，"当前 ApplicationContext 持有一个 BeanFactory"，这里解释了第一行。
      * ApplicationContext 还继承了 ResourceLoader、ApplicationEventPublisher、MessageSource
      * 所以对于这几个依赖，可以赋值为 this，注意 this 是一个 ApplicationContext
      * 那这里怎么没看到为 MessageSource 赋值呢？那是因为 MessageSource 被注册成为了一个普通的 bean
      */
     beanFactory.registerResolvableDependency(BeanFactory.class, beanFactory);
     beanFactory.registerResolvableDependency(ResourceLoader.class, this);
     beanFactory.registerResolvableDependency(ApplicationEventPublisher.class, this);
     beanFactory.registerResolvableDependency(ApplicationContext.class, this);
  
     // 这个 BeanPostProcessor 也很简单，在 bean 实例化后，如果是 ApplicationListener 的子类，
     // 那么将其添加到 listener 列表中，可以理解成：注册 事件监听器
     beanFactory.addBeanPostProcessor(new ApplicationListenerDetector(this));
  
     // 这里涉及到特殊的 bean，名为：loadTimeWeaver，这不是我们的重点，忽略它
     // tips: ltw 是 AspectJ 的概念，指的是在运行期进行织入，这个和 Spring AOP 不一样，
    
     if (beanFactory.containsBean(LOAD_TIME_WEAVER_BEAN_NAME)) {
        beanFactory.addBeanPostProcessor(new LoadTimeWeaverAwareProcessor(beanFactory));
        // Set a temporary ClassLoader for type matching.
        beanFactory.setTempClassLoader(new ContextTypeMatchClassLoader(beanFactory.getBeanClassLoader()));
     }
  
     /**
      * 从下面几行代码我们可以知道，Spring 往往很 "智能" 就是因为它会帮我们默认注册一些有用的 bean，
      * 我们也可以选择覆盖
      */
  
     // 如果没有定义 "environment" 这个 bean，那么 Spring 会 "手动" 注册一个
     if (!beanFactory.containsLocalBean(ENVIRONMENT_BEAN_NAME)) {
        beanFactory.registerSingleton(ENVIRONMENT_BEAN_NAME, getEnvironment());
     }
     // 如果没有定义 "systemProperties" 这个 bean，那么 Spring 会 "手动" 注册一个
     if (!beanFactory.containsLocalBean(SYSTEM_PROPERTIES_BEAN_NAME)) {
        beanFactory.registerSingleton(SYSTEM_PROPERTIES_BEAN_NAME, getEnvironment().getSystemProperties());
     }
     // 如果没有定义 "systemEnvironment" 这个 bean，那么 Spring 会 "手动" 注册一个
     if (!beanFactory.containsLocalBean(SYSTEM_ENVIRONMENT_BEAN_NAME)) {
        beanFactory.registerSingleton(SYSTEM_ENVIRONMENT_BEAN_NAME, getEnvironment().getSystemEnvironment());
     }
  }
  ```

### 初始化单例bean

- `refresh()`调用的AbstractApplicationContext的`finishBeanFactoryInitialization(beanFactory)`方法：

  - Spring 会在这个阶段完成所有的 singleton beans 的实例化。
  - 我们来总结一下，到目前为止，应该说 BeanFactory 已经创建完成，并且所有的实现了 BeanFactoryPostProcessor 接口的 Bean 都已经初始化并且其中的 postProcessBeanFactory(factory) 方法已经得到回调执行了。而且 Spring 已经“手动”注册了一些特殊的 Bean，如 `environment`、`systemProperties` 等。

  ```java
  // 初始化剩 singleton beans
  protected void finishBeanFactoryInitialization(ConfigurableListableBeanFactory beanFactory) {
  
     // 首先，初始化名字为 conversionService 的 Bean。
     // 什么，看代码这里没有初始化 Bean 啊！
     // 注意了，初始化的动作包装在 bean
      //Factory.getBean(...) 中，这里先不说细节，先往下看吧
     if (beanFactory.containsBean(CONVERSION_SERVICE_BEAN_NAME) &&
           beanFactory.isTypeMatch(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class)) {
        beanFactory.setConversionService(
              beanFactory.getBean(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class));
     }
  
     // Register a default embedded value resolver if no bean post-processor
     // (such as a PropertyPlaceholderConfigurer bean) registered any before:
     // at this point, primarily for resolution in annotation attribute values.
     if (!beanFactory.hasEmbeddedValueResolver()) {
        beanFactory.addEmbeddedValueResolver(new StringValueResolver() {
           @Override
           public String resolveStringValue(String strVal) {
              return getEnvironment().resolvePlaceholders(strVal);
           }
        });
     }
  
     // 先初始化 LoadTimeWeaverAware 类型的 Bean
     // 之前也说过，这是 AspectJ 相关的内容，放心跳过吧
     String[] weaverAwareNames = beanFactory.getBeanNamesForType(LoadTimeWeaverAware.class, false, false);
     for (String weaverAwareName : weaverAwareNames) {
        getBean(weaverAwareName);
     }
  
     // Stop using the temporary ClassLoader for type matching.
     beanFactory.setTempClassLoader(null);
  
     // 没什么别的目的，因为到这一步的时候，Spring 已经开始预初始化 singleton beans 了，
     // 肯定不希望这个时候还出现 bean 定义解析、加载、注册。
     beanFactory.freezeConfiguration();
  
     // 开始初始化
     beanFactory.preInstantiateSingletons();
  }
  ```

- DefaultListableBeanFactory中的`preInstantiateSingletons()`方法：

  ```java
  @Override
  public void preInstantiateSingletons() throws BeansException {
     if (this.logger.isDebugEnabled()) {
        this.logger.debug("Pre-instantiating singletons in " + this);
     }
     // this.beanDefinitionNames 保存了所有的 beanNames
     List<String> beanNames = new ArrayList<String>(this.beanDefinitionNames);
  
     // 下面这个循环，触发所有的非懒加载的 singleton beans 的初始化操作
     for (String beanName : beanNames) {
  
        // 合并父 Bean 中的配置，注意 <bean id="" class="" parent="" /> 中的 parent，用的不多吧，
  
        RootBeanDefinition bd = getMergedLocalBeanDefinition(beanName);
  
        // 非抽象、非懒加载的 singletons。如果配置了 'abstract = true'，那是不需要初始化的
        if (!bd.isAbstract() && bd.isSingleton() && !bd.isLazyInit()) {
           // 处理 FactoryBean
           if (isFactoryBean(beanName)) {
              // FactoryBean 的话，在 beanName 前面加上 ‘&’ 符号。再调用 getBean，getBean 方法别急
              final FactoryBean<?> factory = (FactoryBean<?>) getBean(FACTORY_BEAN_PREFIX + beanName);
              // 判断当前 FactoryBean 是否是 SmartFactoryBean 的实现，此处忽略，直接跳过
              boolean isEagerInit;
              if (System.getSecurityManager() != null && factory instanceof SmartFactoryBean) {
                 isEagerInit = AccessController.doPrivileged(new PrivilegedAction<Boolean>() {
                    @Override
                    public Boolean run() {
                       return ((SmartFactoryBean<?>) factory).isEagerInit();
                    }
                 }, getAccessControlContext());
              }
              else {
                 isEagerInit = (factory instanceof SmartFactoryBean &&
                       ((SmartFactoryBean<?>) factory).isEagerInit());
              }
              if (isEagerInit) {
  
                 getBean(beanName);
              }
           }
           else {
              // 对于普通的 Bean，只要调用 getBean(beanName) 这个方法就可以进行初始化了
              getBean(beanName);
           }
        }
     }
  
     // 到这里说明所有的非懒加载的 singleton beans 已经完成了初始化
     // 如果我们定义的 bean 是实现了 SmartInitializingSingleton 接口的，那么在这里得到回调，忽略
     for (String beanName : beanNames) {
        Object singletonInstance = getSingleton(beanName);
        if (singletonInstance instanceof SmartInitializingSingleton) {
           final SmartInitializingSingleton smartSingleton = (SmartInitializingSingleton) singletonInstance;
           if (System.getSecurityManager() != null) {
              AccessController.doPrivileged(new PrivilegedAction<Object>() {
                 @Override
                 public Object run() {
                    smartSingleton.afterSingletonsInstantiated();
                    return null;
                 }
              }, getAccessControlContext());
           }
           else {
              smartSingleton.afterSingletonsInstantiated();
           }
        }
     }
  }
  ```

- AbstractBeanFactory中的`getBean(name)`方法：

  - 这个方法我们经常用来从 BeanFactory 中获取一个 Bean，而初始化的过程也封装到了这个方法里。

  ```java
  @Override
  public Object getBean(String name) throws BeansException {
     return doGetBean(name, null, null, false);
  }
  
  // 我们在剖析初始化 Bean 的过程，但是 getBean 方法我们经常是用来从容器中获取 Bean 用的，注意切换思路，
  // 已经初始化过了就从容器中直接返回，否则就先初始化再返回
  @SuppressWarnings("unchecked")
  protected <T> T doGetBean(
        final String name, final Class<T> requiredType, final Object[] args, boolean typeCheckOnly)
        throws BeansException {
     // 获取一个 “正统的” beanName，处理两种情况，一个是前面说的 FactoryBean(前面带 ‘&’)，
     // 一个是别名问题，因为这个方法是 getBean，获取 Bean 用的，你要是传一个别名进来，是完全可以的
     final String beanName = transformedBeanName(name);
  
     // 注意跟着这个，这个是返回值
     Object bean; 
  
     // 检查下是不是已经创建过了
     Object sharedInstance = getSingleton(beanName);
  
     // 这里说下 args 呗，虽然看上去一点不重要。前面我们一路进来的时候都是 getBean(beanName)，
     // 所以 args 传参其实是 null 的，但是如果 args 不为空的时候，那么意味着调用方不是希望获取 Bean，而是创建 Bean
     if (sharedInstance != null && args == null) {
        if (logger.isDebugEnabled()) {
           if (isSingletonCurrentlyInCreation(beanName)) {
              logger.debug("...");
           }
           else {
              logger.debug("Returning cached instance of singleton bean '" + beanName + "'");
           }
        }
        // 下面这个方法：如果是普通 Bean 的话，直接返回 sharedInstance，
        // 如果是 FactoryBean 的话，返回它创建的那个实例对象
        bean = getObjectForBeanInstance(sharedInstance, name, beanName, null);
     }
  
     else {
        if (isPrototypeCurrentlyInCreation(beanName)) {
           // 创建过了此 beanName 的 prototype 类型的 bean，那么抛异常，
           // 往往是因为陷入了循环引用
           throw new BeanCurrentlyInCreationException(beanName);
        }
  
        // 检查一下这个 BeanDefinition 在容器中是否存在
        BeanFactory parentBeanFactory = getParentBeanFactory();
        if (parentBeanFactory != null && !containsBeanDefinition(beanName)) {
           // 如果当前容器不存在这个 BeanDefinition，试试父容器中有没有
           String nameToLookup = originalBeanName(name);
           if (args != null) {
              // 返回父容器的查询结果
              return (T) parentBeanFactory.getBean(nameToLookup, args);
           }
           else {
              // No args -> delegate to standard getBean method.
              return parentBeanFactory.getBean(nameToLookup, requiredType);
           }
        }
  
        if (!typeCheckOnly) {
           // typeCheckOnly 为 false，将当前 beanName 放入一个 alreadyCreated 的 Set 集合中。
           markBeanAsCreated(beanName);
        }
  
        /*
         * 稍稍总结一下：
         * 到这里的话，要准备创建 Bean 了，对于 singleton 的 Bean 来说，容器中还没创建过此 Bean；
         * 对于 prototype 的 Bean 来说，本来就是要创建一个新的 Bean。
         */
        try {
           final RootBeanDefinition mbd = getMergedLocalBeanDefinition(beanName);
           checkMergedBeanDefinition(mbd, beanName, args);
  
           // 先初始化依赖的所有 Bean，这个很好理解。
           // 注意，这里的依赖指的是 depends-on 中定义的依赖
           String[] dependsOn = mbd.getDependsOn();
           if (dependsOn != null) {
              for (String dep : dependsOn) {
                 // 检查是不是有循环依赖，这里的循环依赖和我们前面说的循环依赖又不一样，这里肯定是不允许出现的，不然要乱套了，读者想一下就知道了
                 if (isDependent(beanName, dep)) {
                    throw new BeanCreationException(mbd.getResourceDescription(), beanName,
                          "Circular depends-on relationship between '" + beanName + "' and '" + dep + "'");
                 }
                 // 注册一下依赖关系
                 registerDependentBean(dep, beanName);
                 // 先初始化被依赖项
                 getBean(dep);
              }
           }
  
           // 如果是 singleton scope 的，创建 singleton 的实例
           if (mbd.isSingleton()) {
              sharedInstance = getSingleton(beanName, new ObjectFactory<Object>() {
                 @Override
                 public Object getObject() throws BeansException {
                    try {
                       // 执行创建 Bean，详情后面再说
                       return createBean(beanName, mbd, args);
                    }
                    catch (BeansException ex) {
                       destroySingleton(beanName);
                       throw ex;
                    }
                 }
              });
              bean = getObjectForBeanInstance(sharedInstance, name, beanName, mbd);
           }
  
           // 如果是 prototype scope 的，创建 prototype 的实例
           else if (mbd.isPrototype()) {
              // It's a prototype -> create a new instance.
              Object prototypeInstance = null;
              try {
                 beforePrototypeCreation(beanName);
                 // 执行创建 Bean
                 prototypeInstance = createBean(beanName, mbd, args);
              }
              finally {
                 afterPrototypeCreation(beanName);
              }
              bean = getObjectForBeanInstance(prototypeInstance, name, beanName, mbd);
           }
  
           // 如果不是 singleton 和 prototype 的话，需要委托给相应的实现类来处理
           else {
              String scopeName = mbd.getScope();
              final Scope scope = this.scopes.get(scopeName);
              if (scope == null) {
                 throw new IllegalStateException("No Scope registered for scope name '" + scopeName + "'");
              }
              try {
                 Object scopedInstance = scope.get(beanName, new ObjectFactory<Object>() {
                    @Override
                    public Object getObject() throws BeansException {
                       beforePrototypeCreation(beanName);
                       try {
                          // 执行创建 Bean
                          return createBean(beanName, mbd, args);
                       }
                       finally {
                          afterPrototypeCreation(beanName);
                       }
                    }
                 });
                 bean = getObjectForBeanInstance(scopedInstance, name, beanName, mbd);
              }
              catch (IllegalStateException ex) {
                 throw new BeanCreationException(beanName,
                       "Scope '" + scopeName + "' is not active for the current thread; consider " +
                       "defining a scoped proxy for this bean if you intend to refer to it from a singleton",
                       ex);
              }
           }
        }
        catch (BeansException ex) {
           cleanupAfterBeanCreationFailure(beanName);
           throw ex;
        }
     }
  
     // 最后，检查一下类型对不对，不对的话就抛异常，对的话就返回了
     if (requiredType != null && bean != null && !requiredType.isInstance(bean)) {
        try {
           return getTypeConverter().convertIfNecessary(bean, requiredType);
        }
        catch (TypeMismatchException ex) {
           if (logger.isDebugEnabled()) {
              logger.debug("Failed to convert bean '" + name + "' to required type '" +
                    ClassUtils.getQualifiedName(requiredType) + "'", ex);
           }
           throw new BeanNotOfRequiredTypeException(name, requiredType, bean.getClass());
        }
     }
     return (T) bean;
  }
  ```

- AbstractAutowireCapableBeanFactory中的`createBean(beanName, mbd, args)`：

  - 第三个参数 args 数组代表创建实例需要的参数，不就是给构造方法用的参数，或者是工厂 Bean 的参数嘛，不过要注意，在我们的初始化阶段，args 是 null。
  - 这个类主要作用是实现了采用 @Autowired 注解注入属性值。这个方法则是创建了bean实例。

  ```java
  @Override
  protected Object createBean(String beanName, RootBeanDefinition mbd, Object[] args) throws BeanCreationException {
     if (logger.isDebugEnabled()) {
        logger.debug("Creating instance of bean '" + beanName + "'");
     }
     RootBeanDefinition mbdToUse = mbd;
  
     // 确保 BeanDefinition 中的 Class 被加载
     Class<?> resolvedClass = resolveBeanClass(mbd, beanName);
     if (resolvedClass != null && !mbd.hasBeanClass() && mbd.getBeanClassName() != null) {
        mbdToUse = new RootBeanDefinition(mbd);
        mbdToUse.setBeanClass(resolvedClass);
     }
  
     // 准备方法覆写，这里又涉及到一个概念：MethodOverrides，它来自于 bean 定义中的 <lookup-method /> 
     // 和 <replaced-method />
     try {
        mbdToUse.prepareMethodOverrides();
     }
     catch (BeanDefinitionValidationException ex) {
        throw new BeanDefinitionStoreException(mbdToUse.getResourceDescription(),
              beanName, "Validation of method overrides failed", ex);
     }
  
     try {
        // 让 InstantiationAwareBeanPostProcessor 在这一步有机会返回代理，
        Object bean = resolveBeforeInstantiation(beanName, mbdToUse);
        if (bean != null) {
           return bean; 
        }
     }
     catch (Throwable ex) {
        throw new BeanCreationException(mbdToUse.getResourceDescription(), beanName,
              "BeanPostProcessor before instantiation of bean failed", ex);
     }
     // 重头戏，创建 bean
     Object beanInstance = doCreateBean(beanName, mbdToUse, args);
     if (logger.isDebugEnabled()) {
        logger.debug("Finished creating instance of bean '" + beanName + "'");
     }
     return beanInstance;
  }
  ```

- AbstractAutowireCapableBeanFactory中的`doCreateBean(beanName, mbdToUse, args)`：

  - 真正的创建一个bean，此时所有的准备工作都已经完成。

  ```java
  protected Object doCreateBean(final String beanName, final RootBeanDefinition mbd, final Object[] args)
        throws BeanCreationException {
  
     // Instantiate the bean.
     BeanWrapper instanceWrapper = null;
     if (mbd.isSingleton()) {
        instanceWrapper = this.factoryBeanInstanceCache.remove(beanName);
     }
     if (instanceWrapper == null) {
        // 说明不是 FactoryBean，这里实例化 Bean，这里非常关键，细节之后再说
        instanceWrapper = createBeanInstance(beanName, mbd, args);
     }
     // 这个就是 Bean 里面的 我们定义的类 的实例，很多地方我直接描述成 "bean 实例"
     final Object bean = (instanceWrapper != null ? instanceWrapper.getWrappedInstance() : null);
     // 类型
     Class<?> beanType = (instanceWrapper != null ? instanceWrapper.getWrappedClass() : null);
     mbd.resolvedTargetType = beanType;
  
     // 建议跳过吧，涉及接口：MergedBeanDefinitionPostProcessor
     synchronized (mbd.postProcessingLock) {
        if (!mbd.postProcessed) {
           try {
              // MergedBeanDefinitionPostProcessor，这个我真不展开说了，直接跳过吧，很少用的
              applyMergedBeanDefinitionPostProcessors(mbd, beanType, beanName);
           }
           catch (Throwable ex) {
              throw new BeanCreationException(mbd.getResourceDescription(), beanName,
                    "Post-processing of merged bean definition failed", ex);
           }
           mbd.postProcessed = true;
        }
     }
  
     // Eagerly cache singletons to be able to resolve circular references
     // even when triggered by lifecycle interfaces like BeanFactoryAware.
     // 下面这块代码是为了解决循环依赖的问题，以后有时间，我再对循环依赖这个问题进行解析吧
     boolean earlySingletonExposure = (mbd.isSingleton() && this.allowCircularReferences &&
           isSingletonCurrentlyInCreation(beanName));
     if (earlySingletonExposure) {
        if (logger.isDebugEnabled()) {
           logger.debug("Eagerly caching bean '" + beanName +
                 "' to allow for resolving potential circular references");
        }
        addSingletonFactory(beanName, new ObjectFactory<Object>() {
           @Override
           public Object getObject() throws BeansException {
              return getEarlyBeanReference(beanName, mbd, bean);
           }
        });
     }
  
     // Initialize the bean instance.
     Object exposedObject = bean;
     try {
        // 这一步也是非常关键的，这一步负责属性装配，因为前面的实例只是实例化了，并没有设值，这里就是设值
        populateBean(beanName, mbd, instanceWrapper);
        if (exposedObject != null) {
           // 还记得 init-method 吗？还有 InitializingBean 接口？还有 BeanPostProcessor 接口？
           // 这里就是处理 bean 初始化完成后的各种回调
           exposedObject = initializeBean(beanName, exposedObject, mbd);
        }
     }
     catch (Throwable ex) {
        if (ex instanceof BeanCreationException && beanName.equals(((BeanCreationException) ex).getBeanName())) {
           throw (BeanCreationException) ex;
        }
        else {
           throw new BeanCreationException(
                 mbd.getResourceDescription(), beanName, "Initialization of bean failed", ex);
        }
     }
  
     if (earlySingletonExposure) {
        // 
        Object earlySingletonReference = getSingleton(beanName, false);
        if (earlySingletonReference != null) {
           if (exposedObject == bean) {
              exposedObject = earlySingletonReference;
           }
           else if (!this.allowRawInjectionDespiteWrapping && hasDependentBean(beanName)) {
              String[] dependentBeans = getDependentBeans(beanName);
              Set<String> actualDependentBeans = new LinkedHashSet<String>(dependentBeans.length);
              for (String dependentBean : dependentBeans) {
                 if (!removeSingletonIfCreatedForTypeCheckOnly(dependentBean)) {
                    actualDependentBeans.add(dependentBean);
                 }
              }
              if (!actualDependentBeans.isEmpty()) {
                 throw new BeanCurrentlyInCreationException(beanName,
                       "Bean with name '" + beanName + "' has been injected into other beans [" +
                       StringUtils.collectionToCommaDelimitedString(actualDependentBeans) +
                       "] in its raw version as part of a circular reference, but has eventually been " +
                       "wrapped. This means that said other beans do not use the final version of the " +
                       "bean. This is often the result of over-eager type matching - consider using " +
                       "'getBeanNamesOfType' with the 'allowEagerInit' flag turned off, for example.");
              }
           }
        }
     }
  
     // Register bean as disposable.
     try {
        registerDisposableBeanIfNecessary(beanName, bean, mbd);
     }
     catch (BeanDefinitionValidationException ex) {
        throw new BeanCreationException(
              mbd.getResourceDescription(), beanName, "Invalid destruction signature", ex);
     }
  
     return exposedObject;
  }
  ```

- AbstractAutowireCapableBeanFactory中的`createBeanInstance(beanName, mbd, args)`:

  - 实例化我们指定的类。

  ```java
  protected BeanWrapper createBeanInstance(String beanName, RootBeanDefinition mbd, Object[] args) {
     // 确保已经加载了此 class
     Class<?> beanClass = resolveBeanClass(mbd, beanName);
  
     // 校验一下这个类的访问权限
     if (beanClass != null && !Modifier.isPublic(beanClass.getModifiers()) && !mbd.isNonPublicAccessAllowed()) {
        throw new BeanCreationException(mbd.getResourceDescription(), beanName,
              "Bean class isn't public, and non-public access not allowed: " + beanClass.getName());
     }
  
     if (mbd.getFactoryMethodName() != null)  {
        // 采用工厂方法实例化，注意，不是 FactoryBean
        return instantiateUsingFactoryMethod(beanName, mbd, args);
     }
  
     // 如果不是第一次创建，比如第二次创建 prototype bean。
     // 这种情况下，我们可以从第一次创建知道，采用无参构造函数，还是构造函数依赖注入 来完成实例化
     boolean resolved = false;
     boolean autowireNecessary = false;
     if (args == null) {
        synchronized (mbd.constructorArgumentLock) {
           if (mbd.resolvedConstructorOrFactoryMethod != null) {
              resolved = true;
              autowireNecessary = mbd.constructorArgumentsResolved;
           }
        }
     }
     if (resolved) {
        if (autowireNecessary) {
           // 构造函数依赖注入
           return autowireConstructor(beanName, mbd, null, null);
        }
        else {
           // 无参构造函数
           return instantiateBean(beanName, mbd);
        }
     }
  
     // 判断是否采用有参构造函数
     Constructor<?>[] ctors = determineConstructorsFromBeanPostProcessors(beanClass, beanName);
     if (ctors != null ||
           mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_CONSTRUCTOR ||
           mbd.hasConstructorArgumentValues() || !ObjectUtils.isEmpty(args))  {
        // 构造函数依赖注入
        return autowireConstructor(beanName, mbd, ctors, args);
     }
  
     // 调用无参构造函数
     return instantiateBean(beanName, mbd);
  }
  ```

  - 调用无参构造函数`instantiateBean(beanName, mbd)`：

    ```java
    protected BeanWrapper instantiateBean(final String beanName, final RootBeanDefinition mbd) {
       try {
          Object beanInstance;
          final BeanFactory parent = this;
          if (System.getSecurityManager() != null) {
             beanInstance = AccessController.doPrivileged(new PrivilegedAction<Object>() {
                @Override
                public Object run() {
    
                   return getInstantiationStrategy().instantiate(mbd, beanName, parent);
                }
             }, getAccessControlContext());
          }
          else {
             // 实例化
             beanInstance = getInstantiationStrategy().instantiate(mbd, beanName, parent);
          }
          // 包装一下，返回
          BeanWrapper bw = new BeanWrapperImpl(beanInstance);
          initBeanWrapper(bw);
          return bw;
       }
       catch (Throwable ex) {
          throw new BeanCreationException(
                mbd.getResourceDescription(), beanName, "Instantiation of bean failed", ex);
       }
    }
    ```

  - 实际的实例化过程在SimpleInstantiationStrategy中的`getInstantiationStrategy().instantiate(mbd, beanName, parent)`：

    ```java
    @Override
    public Object instantiate(RootBeanDefinition bd, String beanName, BeanFactory owner) {
    
       // 如果不存在方法覆写，那就使用 java 反射进行实例化，否则使用 CGLIB,
       
       if (bd.getMethodOverrides().isEmpty()) {
          Constructor<?> constructorToUse;
          synchronized (bd.constructorArgumentLock) {
             constructorToUse = (Constructor<?>) bd.resolvedConstructorOrFactoryMethod;
             if (constructorToUse == null) {
                final Class<?> clazz = bd.getBeanClass();
                if (clazz.isInterface()) {
                   throw new BeanInstantiationException(clazz, "Specified class is an interface");
                }
                try {
                   if (System.getSecurityManager() != null) {
                      constructorToUse = AccessController.doPrivileged(new PrivilegedExceptionAction<Constructor<?>>() {
                         @Override
                         public Constructor<?> run() throws Exception {
                            return clazz.getDeclaredConstructor((Class[]) null);
                         }
                      });
                   }
                   else {
                      constructorToUse = clazz.getDeclaredConstructor((Class[]) null);
                   }
                   bd.resolvedConstructorOrFactoryMethod = constructorToUse;
                }
                catch (Throwable ex) {
                   throw new BeanInstantiationException(clazz, "No default constructor found", ex);
                }
             }
          }
          // 利用构造方法进行实例化
          return BeanUtils.instantiateClass(constructorToUse);
       }
       else {
          // 存在方法覆写，利用 CGLIB 来完成实例化，需要依赖于 CGLIB 生成子类，这里就不展开了。
          // tips: 因为如果不使用 CGLIB 的话，存在 override 的情况 JDK 并没有提供相应的实例化支持
          return instantiateWithMethodInjection(bd, beanName, owner);
       }
    }
    ```

    

- AbstractAutowireCapableBeanFactory中的`populateBean(beanName, mbd, instanceWrapper)`:

  - 该方法负责进行属性设值，处理依赖。

  ```java
  protected void populateBean(String beanName, RootBeanDefinition mbd, BeanWrapper bw) {
     // bean 实例的所有属性都在这里了
     PropertyValues pvs = mbd.getPropertyValues();
  
     if (bw == null) {
        if (!pvs.isEmpty()) {
           throw new BeanCreationException(
                 mbd.getResourceDescription(), beanName, "Cannot apply property values to null instance");
        }
        else {
           // Skip property population phase for null instance.
           return;
        }
     }
  
     // 到这步的时候，bean 实例化完成（通过工厂方法或构造方法），但是还没开始属性设值，
     // InstantiationAwareBeanPostProcessor 的实现类可以在这里对 bean 进行状态修改，
     // 我也没找到有实际的使用，所以我们暂且忽略这块吧
     boolean continueWithPropertyPopulation = true;
     if (!mbd.isSynthetic() && hasInstantiationAwareBeanPostProcessors()) {
        for (BeanPostProcessor bp : getBeanPostProcessors()) {
           if (bp instanceof InstantiationAwareBeanPostProcessor) {
              InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;
              // 如果返回 false，代表不需要进行后续的属性设值，也不需要再经过其他的 BeanPostProcessor 的处理
              if (!ibp.postProcessAfterInstantiation(bw.getWrappedInstance(), beanName)) {
                 continueWithPropertyPopulation = false;
                 break;
              }
           }
        }
     }
  
     if (!continueWithPropertyPopulation) {
        return;
     }
  
     if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_NAME ||
           mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_TYPE) {
        MutablePropertyValues newPvs = new MutablePropertyValues(pvs);
  
        // 通过名字找到所有属性值，如果是 bean 依赖，先初始化依赖的 bean。记录依赖关系
        if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_NAME) {
           autowireByName(beanName, mbd, bw, newPvs);
        }
  
        // 通过类型装配。复杂一些
        if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_TYPE) {
           autowireByType(beanName, mbd, bw, newPvs);
        }
  
        pvs = newPvs;
     }
  
     boolean hasInstAwareBpps = hasInstantiationAwareBeanPostProcessors();
     boolean needsDepCheck = (mbd.getDependencyCheck() != RootBeanDefinition.DEPENDENCY_CHECK_NONE);
  
     if (hasInstAwareBpps || needsDepCheck) {
        PropertyDescriptor[] filteredPds = filterPropertyDescriptorsForDependencyCheck(bw, mbd.allowCaching);
        if (hasInstAwareBpps) {
           for (BeanPostProcessor bp : getBeanPostProcessors()) {
              if (bp instanceof InstantiationAwareBeanPostProcessor) {
                 InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;
                 // 这里有个非常有用的 BeanPostProcessor 进到这里: AutowiredAnnotationBeanPostProcessor
                 // 对采用 @Autowired、@Value 注解的依赖进行设值
                 pvs = ibp.postProcessPropertyValues(pvs, filteredPds, bw.getWrappedInstance(), beanName);
                 if (pvs == null) {
                    return;
                 }
              }
           }
        }
        if (needsDepCheck) {
           checkDependencies(beanName, mbd, filteredPds, pvs);
        }
     }
     // 设置 bean 实例的属性值
     applyPropertyValues(beanName, mbd, bw, pvs);
  }
  ```

- AbstractAutowireCapableBeanFactory中的`initializeBean(beanName, bean, mbd)`：

  - 属性注入完成后，这一步其实就是处理各种回调了。

  ```java
  protected Object initializeBean(final String beanName, final Object bean, RootBeanDefinition mbd) {
     if (System.getSecurityManager() != null) {
        AccessController.doPrivileged(new PrivilegedAction<Object>() {
           @Override
           public Object run() {
              invokeAwareMethods(beanName, bean);
              return null;
           }
        }, getAccessControlContext());
     }
     else {
        // 如果 bean 实现了 BeanNameAware、BeanClassLoaderAware 或 BeanFactoryAware 接口，回调
        invokeAwareMethods(beanName, bean);
     }
  
     Object wrappedBean = bean;
     if (mbd == null || !mbd.isSynthetic()) {
        // BeanPostProcessor 的 postProcessBeforeInitialization 回调
        wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);
     }
  
     try {
        // 处理 bean 中定义的 init-method，
        // 或者如果 bean 实现了 InitializingBean 接口，调用 afterPropertiesSet() 方法
        invokeInitMethods(beanName, wrappedBean, mbd);
     }
     catch (Throwable ex) {
        throw new BeanCreationException(
              (mbd != null ? mbd.getResourceDescription() : null),
              beanName, "Invocation of init method failed", ex);
     }
  
     if (mbd == null || !mbd.isSynthetic()) {
        // BeanPostProcessor 的 postProcessAfterInitialization 回调
        wrappedBean = applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);
     }
     return wrappedBean;
  }
  ```
