## 1、事务管理

- 同一个事务应该由同一个连接（connection）完成，需要使用ThreadLocal对象把连接与当前线程绑定，从而使一个线程中只有一个连接可以操作。

- 事务控制应该在业务层。

- 连接的工具类 ConnectionUtils，从数据源获取一个连接并且和线程绑定：

  ```java
  public class ConnectionUtils {
  
      private ThreadLocal<Connection> tl = new ThreadLocal<Connection>();
  
      private DataSource dataSource;
  
      public void setDataSource(DataSource dataSource) {
          this.dataSource = dataSource;
      }
  
      public Connection getThreadConnection() {
          try {
              //1、从当前线程获取连接
              Connection connection = tl.get();
              //2、判断当前线程是否有连接
              if(connection == null) {
                  //3、没有则获取一个新连接
                  connection = dataSource.getConnection();
                  tl.set(connection);
              }
              //4、返回连接
              return connection;
          } catch (SQLException e) {
              throw new RuntimeException(e);
          }
      }
  
      public void removeConnection() {
          tl.remove();
      }
  }
  ```

- 事务管理的工具类 TransactionManager，包括开启、提交、回滚事务和提交连接：

  ```java
  public class TransactionManager {
  
      private ConnectionUtils connectionUtils;
  
      public void setConnectionUtils(ConnectionUtils connectionUtils) {
          this.connectionUtils = connectionUtils;
      }
  
      public void beginTransaction () {
          try {
              connectionUtils.getThreadConnection().setAutoCommit(false);
          } catch (SQLException e) {
              e.printStackTrace();
          }
      }
      public void commit () {
          try {
              connectionUtils.getThreadConnection().commit();
          } catch (SQLException e) {
              e.printStackTrace();
          }
      }
      public void rollback () {
          try {
              connectionUtils.getThreadConnection().rollback();
          } catch (SQLException e) {
              e.printStackTrace();
          }
      }
      public void release () {
          try {
              connectionUtils.getThreadConnection().close();
              connectionUtils.removeConnection();
          } catch (SQLException e) {
              e.printStackTrace();
          }
      }
  }
  ```

- 在 AccountDaoImpl 中，每次都使用与线程绑定的连接进行操作：

  ```java
  public class AccountDaoImpl implements AccountDao {
  
      private QueryRunner runner;
      private ConnectionUtils connectionUtils;
  
      public void setConnectionUtils(ConnectionUtils connectionUtils) {
          this.connectionUtils = connectionUtils;
      }
  
      public void setRunner(QueryRunner runner) {
          this.runner = runner;
      }
      
      public void updateAccount(Account account) {
          try {
              runner.update(connectionUtils.getThreadConnection(),"update account set name=?,money=? where id=?",account.getName(),account.getMoney(),account.getId());
          } catch (SQLException e) {
              e.printStackTrace();
          }
      }
  }
  ```

- 在 AccountServiceImpl 中，调用事务：

  ```java
  public class AccountServiceImpl implements AccountService {
  
      private AccountDao accountDao;
      private TransactionManager txManager;
  
      public void setAccountDao(AccountDao accountDao) {
          this.accountDao = accountDao;
      }
  
      public void setTxManager(TransactionManager txManager) {
          this.txManager = txManager;
      }
      
       public void transfer(String sourceName, String targetName, float money) {
          try {
              txManager.beginTransaction();
              Account source = accountDao.findAccountByName(sourceName);
              Account target = accountDao.findAccountByName(targetName);
              source.setMoney(source.getMoney()-money);
              target.setMoney(target.getMoney()+money);
              accountDao.updateAccount(source);
              accountDao.updateAccount(target);
              System.out.println("提交");
              txManager.commit();
  
          } catch (Exception e) {
              txManager.rollback();
              System.out.println("回滚");
              e.printStackTrace();
          } finally {
              txManager.release();
              System.out.println("释放");
          }
      }
  }
  ```

- 在 bean.xml 中设置注入：

  ```xml
  <beans>
  	<bean id="connectionUtils" class="cn.iwehdio.utils.ConnectionUtils">
          <property name="dataSource" ref="datasource"></property>
      </bean>
  
      <bean id="txManager" class="cn.iwehdio.utils.TransactionManager">
          <property name="connectionUtils" ref="connectionUtils"></property>
      </bean>
  
      <bean id="accountService" class="cn.iwehdio.service.Impl.AccountServiceImpl">
          <property name="accountDao" ref="accountDao"></property>
          <property name="txManager" ref="txManager"></property>
      </bean>
  
      <bean id="accountDao" class="cn.iwehdio.dao.Impl.AccountDaoImpl">
          <property name="runner" ref="runner"></property>
          <property name="connectionUtils" ref="connectionUtils"></property>
      </bean>
  </beans>
  ```

  

## 2、基于代理的事务

- 动态代理：

  - 特点：字节码随用随创建，随用随加载。
- 作用：不修改源码的基础上对方法进行增强。
  - 分类：

    - 基于接口的动态代理。
  - 基于子类的动态代理。
  - 基于接口的动态代理：
  
  - 涉及的类：Proxy。
    - 提供者：JDK官方。
- 如何创建基于接口的代理对象：
  
    - 使用Proxy类中的`newProxyInstance`方法。
  - 被代理类最少实现一个方法。
    - 参数：
    - `ClassLoader`：用于加载代理对象字节码，写的是被代理对象的类加载器。
        - `.getClass().getClassLoader()`。
      - `CLass[]`：字节码数组，让代理对象与被代理对象有相同的方法，代理谁就写谁的接口。
        - `.getClass().getInerfaces()`。
      - `new InvocationHandler()`：写的是如何代理，一般都是该接口的实现类。
        - `invoke()`方法：执行被代理对象的任何接口方法都会经过该方法。
        - 参数`proxy`：代理对象的引用。
        - 参数`method`：当前执行的方法。
        - 参数`args`：当前执行方法所需的参数。
        - 返回值：和被代理对象相同。
      - 如果被代理的类不实现任何接口，就无法使用基于接口的动态代理。
  - 基于子类的代理：
  
    - 涉及的类：EnHancer。
  - 提供者：第三方cglib库。
- 如何创建基于子类的代理对象：
  
  - 使用EnHancer类中的`create`方法。
  
  - 被代理类不能是最终类。
  
  - 参数：
  
    - `Class`：代表用于指定被代理对象的字节码。
  
      - `.getClass()`。
  
    - `Callback`：用于提供增强的方法。
  
      - 一般写的是该接口的子实现类`MethodInterceptor`的`intercept()`。
  
        - 参数：新增了methodProxy当前执行方法的代理对象。
- 执行被代理对象的所有方法都会执行此方法。
  

- 使用基于接口的动态代理实现事务管理：

  - 创建工厂类 BeanFactory，实现对 AccountService 基于接口的动态代理：

    ```java
    public class BeanFactory {
    
        private AccountService accountService;
        private TransactionManager txManager;
    
        public void setAccountService(AccountService accountService) {
            this.accountService = accountService;
        }
    
        public void setTxManager(TransactionManager txManager) {
            this.txManager = txManager;
        }
    
        public AccountService getAccountService() {
            return (AccountService) Proxy.newProxyInstance(accountService.getClass().getClassLoader(),
                    accountService.getClass().getInterfaces(), new InvocationHandler() {
                        @Override
                        public Object invoke(Object o, Method method, Object[] objects) throws Throwable {
                            System.out.println(method.getName());
                            if(!"transfer".equals(method.getName())) {
                                return method.invoke(accountService, objects);
                            }
                            Object value = null;
                            try {
                                txManager.beginTransaction();
                                value = method.invoke(accountService, objects);
                                System.out.println("提交");
                                txManager.commit();
                                return value;
                            } catch (Exception e) {
                                txManager.rollback();
                                System.out.println("回滚");
                                throw new RuntimeException(e);
                            } finally {
                                txManager.release();
                                System.out.println("释放");
                            }
                        }
                    });
        }
    }
    ```

  - 在 AcountServiceImpl 中恢复到无异常处理的状态：

    ```java
    public void transfer(String sourceName, String targetName, float money) {
        Account source = accountDao.findAccountByName(sourceName);
        Account target = accountDao.findAccountByName(targetName);
        source.setMoney(source.getMoney()-money);
        target.setMoney(target.getMoney()+money);
        accountDao.updateAccount(source);
        accountDao.updateAccount(target);
    }
    ```

  - 在 bean.xml 中注入：

    ```xml
    <beans>
    	<bean id="proxyAccountService" factory-bean="beanFactory" factory-method="getAccountService"></bean>
    
        <bean id="beanFactory" class="cn.iwehdio.factory.BeanFactory">
            <property name="txManager" ref="txManager"></property>
            <property name="accountService" ref="accountService"></property>
        </bean>
    </beans>
    ```

  - 与 test 测试类整合：

    ```java
    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration(locations = "classpath:bean.xml")
    public class test {
    
        @Autowired
        @Qualifier("proxyAccountService")
        private AccountService service;
        
        @Test
        public void trans() {
            service.transfer("aaa","bbb",100);
        }
    }
    ```

- 基于子类的动态代理：

  ```java
  public class BeanFactory00 {
  
      private AccountService accountService;
      private TransactionManager txManager;
  
      public void setAccountService(AccountService accountService) {
          this.accountService = accountService;
      }
      public void setTxManager(TransactionManager txManager) {
          this.txManager = txManager;
      }
  
      public AccountService getAccountService() {
          return (AccountService) Enhancer.create(accountService.getClass(), new MethodInterceptor() {
              @Override
              public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                  System.out.println(method.getName());
                  if(!"transfer".equals(method.getName())) {
                      return method.invoke(accountService, objects);
                  }
                  Object value = null;
                  try {
                      txManager.beginTransaction();
                      value = method.invoke(accountService, objects);
                      System.out.println("提交");
                      txManager.commit();
                      return value;
                  } catch (Exception e) {
                      txManager.rollback();
                      System.out.println("回滚");
                      throw new RuntimeException(e);
                  } finally {
                      txManager.release();
                      System.out.println("释放");
                  }
              }
          });
      }
  }
  ```

  

## 3、AOP

> 面向切片编程，通过预编译和运行期动态代理实现程序功能的统一维护。是函数式编程了一种衍生范型，可以对业务逻辑的各个部分进行隔离，使得业务逻辑各部分的耦合性降低，提高可重用性。

- 作用：在程序运行期间，不修改源码对已有方法进行增强。

- 实现方式：动态代理。

- Spring中的AOP：通过配置的方式进行动态代理。

- AOP相关术语：

  - Joinpoint 连接点：指那些被拦截到的点。在Spring中可以指业务层接口中的所有方法被拦截。
  - Pointcut 切入点：指要对哪些连接点进行拦截。在Spring中可以指业务层接口中被拦截并被实际增强的方法。
  - 比如业务层接口被代理，接口中的一个方法并没有被实际增强，这个方法是连接点而不是切入点。
  - Advice 通知/增强：指拦截到连接点后所要做的事情。
  - 在切入点方法调用之前的是前置通知，之后是后置通知，catch 中的是异常通知，final 中的是最终通知，整个 invoke 方法是环绕通知。
  - Introduction 引介：一种特殊的通知。
  - Target 目标对象：被代理的目标对象。
  - Weaving 织入：把增强应用到目标对象来创建新的代理对象的过程。
  - Proxy 代理：一个类被织入增强后，就产生一个结果代理类。
  - Aspect 切面：是切入点和通知的对应关系，即被增强的方法调用了那些通知。

- 通过XML进行Spring的AOP配置：

  - 导入坐标，用于解析切入点表达式：

    ```xml
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.8.7</version>
    </dependency>
    ```

  - 编写业务层的类和演示用工具类：

    ```java
    //AccountService.java
    public interface AccountService {
        //模拟保存账户
        void saveAccount();
        //模拟更新账户
        void updateAccount(int i);
        //模拟删除用户
        int deleteAccount();
    }
    
    //AccountServiceImpl.java
    public class AccountServiceImpl implements AccountService {
        @Override
        public void saveAccount() {
            System.out.println("保存");
        }
        @Override
        public void updateAccount(int i) {
            System.out.println("更新");
        }
        @Override
        public int deleteAccount() {
            System.out.println("删除");
            return 0;
        }
    }
    
    //Logger.java
    public class Logger {
        //打印日志，在切入点方法（业务层方法）之前执行
        public void printLog() {
            System.out.println("printlog记录日志");
        }
    }
    ```

  - 配置AOP约束和IOC配置：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:aop="http://www.springframework.org/schema/aop"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/aop
            http://www.springframework.org/schema/aop/spring-aop.xsd">
    
        <bean id="accountService" class="cn.iwehdio.service.Impl.AccountServiceImpl"></bean>
    
    </beans>
    ```

  - AOP配置步骤：

    1. 把通知的Bean也交给Spring管理。

    2. 使用`<aop:config>`标签表明开始AOP的配置。

    3. 使用`<aop:aspect>`标签表明配置切面。

       - id 属性：给切面提供一个唯一索引。
       - ref 属性：指定通知类bean的id。

    4. 在`<aop:aspect>`标签内部使用对应标签配置通知类型。

       - `<aop:before>`标签表示前置通知。

         - method 属性：用于指定通知类中那个方法是前置通知。
         - pointcut 属性：指定切入点表达式，含义是指的是对业务层中的那些方法进行增强。

       - 切入点表达式：`execution(表达式)`。

         - 表达式：`访问修饰符 返回值 全类名.方法名(参数列表)`。
         - 全通配写法：`* *..*.*(..)`。
         - 访问修饰符可以省略。
         - 返回值可以使用通配符，表示任意返回值。
         - 包名可以使用通配符，表示任意包，但是有几级包就需要写几个`*.`。
         - 包名可以使用`..`表示当前包及其子包。
         - 类名和方法名都可以使用`*`实现通配。
         - 参数列表可以直接写数据类型，基本类型写名称，引用类型写全类名。
         - 通配符表示任意个参数，但是必须有参数。
         - `..`表示任意个参数，没有参数也可以。
         - 一般而言，切入点且到业务层实现类下的所有方法。

       - 通用切入点表达式`<aop:pointcut>`：

         - 如果写在`<aop:aspect>`内部，只能当前切面使用。
         - 如果写在`<aop:aspect>`外部，所有切面都可以使用，但是必须在切面之前。

         ```xml
         <aop:pointcut id="pt" expression="execution(* cn.iwehdio.service.Impl.*.*(..))"/>
         <aop:before method="beforePrintLog" pointcut-ref="pt"></aop:before>
         ```

    5. 完整配置：

    ```xml
    <bean id="accountService" class="cn.iwehdio.service.Impl.AccountServiceImpl"></bean>
    <bean id="logger" class="cn.iwehdio.utils.Logger"></bean>
    <aop:config>
        <aop:aspect id="logAdvice" ref="logger">
            <!-- 配置通知类型，建立通知方法和切入点方法的关联 -->
            <aop:before method="printLog" pointcut="execution(public void cn.iwehdio.service.Impl.AccountServiceImpl.saveAccount())"></aop:before>
        </aop:aspect>
    </aop:config>
    ```

  - 测试类：

    ```java
    public class AOPTest {
        public static void main(String[] args) {
            ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
            AccountService accountService = (AccountService) ac.getBean("accountService");
            accountService.saveAccount();
        }
    }
    ```

- 常用的通知类型：

  - 在Logger 中添加四种通知：

    ```java
    public class Logger {
        public void beforePrintLog() {
            System.out.println("前置通知printlog记录日志");
        }
        public void afterReturnPrintLog() {
            System.out.println("后置通知printlog记录日志");
        }
        public void afterThrowingPrintLog() {
            System.out.println("异常通知printlog记录日志");
        }
        public void finalPrintLog() {
            System.out.println("最终通知printlog记录日志");
        }
    }
    ```

  - bean.xml中的通知配置：

    ```xml
    <aop:config>
        <aop:aspect id="logAdvice" ref="logger">
            <!-- 配置通知类型，建立通知方法和切入点方法的关联 -->
            <aop:before method="beforePrintLog" pointcut="execution(* cn.iwehdio.service.Impl.*.*(..))"></aop:before>
            <aop:after-returning method="afterReturnPrintLog" pointcut="execution(* cn.iwehdio.service.Impl.*.*(..))"></aop:after-returning>
            <aop:after-throwing method="afterThrowingPrintLog" pointcut="execution(* cn.iwehdio.service.Impl.*.*(..))"></aop:after-throwing>
            <aop:after method="afterPrintLog" pointcut="execution(* cn.iwehdio.service.Impl.*.*(..))"></aop:after>
        </aop:aspect>
    </aop:config>
    ```

  - 环绕通知：

    -  bean.xml配置：

      ```xml
      <aop:around method="aroundPrintLog" pointcut-ref="pt"></aop:around>
      ```

    - 配置环绕通知后，切入点方法没有执行，但是环绕通知执行了。

    - 在环绕通知中，需要显式调用切入点方法。

    - 使用`ProceedingJoinPoint`接口的方法`proceed()`方法调用切入点方法，使用`getArgs()`方法获取参数：

      ```java
      public Object aroundPrintLog(ProceedingJoinPoint pjp) {
          Object value = null;
          Object[] args = pjp.getArgs();
          try {
              System.out.println("环绕通知(前置)printlog记录日志");
              value = pjp.proceed(args);
              System.out.println("环绕通知(后置)printlog记录日志");
          } catch (Throwable throwable) {
              System.out.println("环绕通知(异常)printlog记录日志");
              throwable.printStackTrace();
          } finally {
              System.out.println("环绕通知(最终)printlog记录日志");
          }
          return value;
      }
      ```

- 基于注解的AOP配置：

  - 基于注解的约束：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:aop="http://www.springframework.org/schema/aop"
           xmlns:context="http://www.springframework.org/schema/context"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/aop
            http://www.springframework.org/schema/aop/spring-aop.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/aop/spring-context.xsd">
    ```

  - 配置Spring创建容器时扫描的包，并把业务层类和Logger类载入IOC容器。

  - 配置Spring开启AOP注解支持：

    ```xml
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
    ```

    - 也可以使用注解`@EnableAspectJAutoProxy`。

  - `@Aspect`表示当前类是一个切面类，在Logger类上。

  - `@Before(切入点表达式)`：前置通知标签。

  - `@AfterReturning(切入点表达式)`：后置通知标签。

  - `@AfterThrowing(切入点表达式)`：异常通知标签。

  - `@After(切入点表达式)`：最终通知标签。

  - `@Around(切入点表达式)`：环绕通知标签。

  - 通用切入点表达式：

    ```java
    @Pointcut("表达式")
    private void pt(){}
    @Before("pt()")
    public void before PrintLog(){}
    ```

  - Spring基于注解的配置，后置通知的执行顺序有问题。



