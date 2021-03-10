学习自：

- [ThreadLocal就是这么简单](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247484118&idx=1&sn=da3e4c4cfd0642687c5d7bcef543fe5b&chksm=ebd743d7dca0cac19a82c7b29b5b22c4b902e9e53bd785d066b625b4272af2a6598a0cc0f38e&scene=21###wechat_redirect)
- [Lock锁子类了解一下](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247484206&idx=1&sn=9722748c0308b3e56220be1c9d939ad7&chksm=ebd7422fdca0cb39ac7825e565ac4e7ed7fd77638da1a931f916d3b6c06ef50beb5c085510bf&scene=21###wechat_redirect)
- [线程池你真不来了解一下吗？](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247484214&idx=1&sn=9b5c977e0f8329b2bf4c29d230c678fb&chksm=ebd74237dca0cb212f4505935f9905858b9166beddd4603c3d3b5386b5dd8cf240c460a8e7c4&scene=21###wechat_redirect)
- [线程池继承体系](https://www.yuque.com/books/share/2b434c74-ed3a-470e-b148-b4c94ba14535/ryn3lf)
- [多线程之死锁就是这么简单](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247484218&idx=1&sn=5e5d7859627ed2c30ee517cb64e0a930&chksm=ebd7423bdca0cb2d55528781e9d3d12cfb94bc566946069293d1fad3c788a7e617879ba66b9e&scene=21###wechat_redirect)
- [比AtomicLong还高效的LongAdder源码解析](https://blog.csdn.net/qq_32331073/article/details/80493243)
- [ThreadLocal的内存泄露？什么原因？如何避免？](https://zhuanlan.zhihu.com/p/102571059)



## 1、ThreadLocal

- 什么是ThreadLocal：

  - ThreadLocal提供了线程的局部变量，每个线程都可以通过set()和get()来对这个局部变量进行操作，但不会和其他线程的局部变量进行冲突，实现了线程的数据隔离。

- ThreadLocal可以让我们拥有当前线程的变量，那这个作用有什么用呢？

  - 管理Connection。
    - 最典型的是管理数据库的Connection。数据库连接池的连接交由ThreadLocal来进行管理。
    - ThreadLocal能够实现当前线程的操作都是用同一个Connection，保证了事务。
  - 避免一些参数传递。
    - 类似于Cookie和Session的作用。

- 实现原理：

  - ThreadLocal的set()方法：

    ```java
    public void set(T value) {
    
        // 得到当前线程对象
        Thread t = Thread.currentThread();
    
        // 这里获取ThreadLocalMap
        ThreadLocalMap map = getMap(t);
    
        // 如果map存在，则将当前线程对象t作为key，要存储的对象作为value存到map里面去
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
    }
    ```

  - ThreadLocalMap是ThreadLocal的一个内部类。用Entry类来进行存储。值都是存储到这个Map上的，key是当前ThreadLocal对象

    ![image-20210113210345028](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113210345028.png)

    - 如果该Map不存在，则初始化一个。如果该Map存在，则从Thread中获取。

    ![image-20210113210455553](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113210455553.png)

  - Thread类中维护了ThreadLocalMap变量。

    ![image-20210113210615523](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113210615523.png)

    - ThreadLocalMap是在ThreadLocal中使用内部类来编写的，但对象的引用是在Thread中。
    - Thread为每个线程维护了ThreadLocalMap这么一个Map，而ThreadLocalMap的key是LocalThread对象本身，value则是要存储的对象。

  - get()方法：

    ![image-20210113210815942](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113210815942.png)

- 原理总结：

  - 每个Thread维护着一个ThreadLocalMap的引用
  - ThreadLocalMap是ThreadLocal的内部类，用Entry来进行存储
  - 调用ThreadLocal的set()方法时，实际上就是往ThreadLocalMap设置值，key是ThreadLocal对象，值是传递进来的对象
  - 调用ThreadLocal的get()方法时，实际上就是往ThreadLocalMap获取值，key是ThreadLocal对象
  - ThreadLocal本身并不存储值，它只是作为一个key来让线程从ThreadLocalMap获取value。

- ThreadLocal的内存泄露：

  ![image-20210113211028068](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113211028068.png)

  - ThreadLocal内存泄漏的根源是：由于ThreadLocalMap的生命周期跟Thread一样长，如果没有手动删除对应key就会导致内存泄漏，而不是因为弱引用。
  - 想要避免内存泄露就要手动remove()掉。
  - 实际上弱引用是防止内存泄露的一道屏障，因为如果ThreadLocalMap持有的是ThreadLocal的强引用，那么ThreadLocal就不会被回收，这个Entry也就发生了内存泄漏。
  - 而如果是弱引用，ThreadLocal不会内存泄漏，对应的value在下一次ThreadLocalMap调用set(),get(),remove()的时候会被清除。



## 2、Lock

- Lock锁主要的两个子类：

  - ReentrantLock
  - ReentrantReadWriteLock

- ReentrantLock锁：

  - 与synchronized都是互斥锁，但是有更好的扩展性。
  - 可以通过带参构造方法设置为公平锁，默认是非公平锁。
  - 使用时最标准用法是在try之前调用lock方法，在finally代码块释放锁。

  ```java
  class X {
      private final ReentrantLock lock = new ReentrantLock();
      // ...
  
      public void m() { 
          lock.lock();  // block until condition holds
          try {
              // ... method body
          } finally {
              lock.unlock()
          }
      }
  }
  ```

- 有三个内部类：

  ![image-20210113211728556](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113211728556.png)

  - 这些内部类都是AQS的子类，这就印证了我们之前所说的：AQS是ReentrantLock的基础，AQS是构建锁、同步器的框架。
  - ReentrantLock锁是支持公平锁和非公平锁的。

  ![image-20210113211759777](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113211759777.png)

- 非公平的lock方法：

  - 尝试获取锁，获取失败的话就调用AQS的`acquire(1)`方法。

  ![image-20210113211924884](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113211924884.png)

  - `acquire(1)`方法我们在AQS时简单看过了。总之就是锁是空闲状态就获取，锁是已获取状态就重入。

- 公平的lock方法：

  - 公平的lock方法其实就多了一个状态。判断当前线程是否位于CLH同步队列中的第一个。如果是则返回flase，否则返回true。

  ![image-20210113212054796](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113212054796.png)

- ReentrantReadWriteLock锁：

  - ReentrantReadWriteLock是一个读写锁：

    - 在读取数据的时候，可以多个线程同时进入到到临界区(被锁定的区域)。
    - 在写数据的时候，无论是读线程还是写线程都是互斥的。

  - 读写锁有一个接口ReadWriteLock，定义的方法就两个：

    ![image-20210113212354004](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113212354004.png)

  - 默认是非公平锁，不会偏好于读锁或者写锁。

  - 等待时间最长的写进程将获得写锁。

  - 当尝试公平获取读锁时，写锁释放后读进程才能获取锁。

  - 当尝试公平获取写锁时，当前应该处于无锁状态。

  - 写锁能获取读锁，读锁不能获取写锁。

  - 写锁可以降级成读锁，读锁不能升级为写锁。

  - 读锁不支持条件对象，写锁支持条件对象。

- ReentrantReadWriteLock比ReentrantLock锁多了两个内部类(都是Lock实现)来维护读锁和写锁，但是主体还是使用Syn。

- 在ReentrantLock锁上使用的是state来表示同步状态(也可以表示重入的次数)，而在ReentrantReadWriteLock是这样代表读写状态的：

  ![image-20210113212804623](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113212804623.png)

- 写锁的获取：

  - 主要还是调用syn的`acquire(1)`。.

  ![image-20210113212903658](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113212903658.png)

- 读锁获取：

  - 读锁的获取调用的是`acquireShared(int arg)`方法。内部调用的是`doAcquireShared(arg)`方法(实现也是在Syn的)。

  ![image-20210113213138139](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113213138139.png)



## 3、线程池

- 线程池可以看做是线程的集合。在没有任务时线程处于空闲状态，当请求到来：线程池给这个请求分配一个空闲的线程，任务完成后回到线程池中等待下次任务(而不是销毁)。这样就实现了线程的重用。

- ThreadPoolExecutor的核心原理就一句话：**阻塞队列 + 创建线程的ThreadFactory**。

- 如果没有线程池，就需要为每个请求新开一个线程：

  - 线程生命周期的开销非常高。每个线程都有自己的生命周期，创建和销毁线程所花费的时间和资源可能比处理客户端的任务花费的时间和资源更多，并且还会有某些空闲线程也会占用资源。
  - 程序的稳定性和健壮性会下降，每个请求开一个线程。如果受到了恶意攻击或者请求过多(内存不足)，程序很容易就崩溃掉了。
  - 所以说：我们的线程最好是交由线程池来管理，这样可以减少对线程生命周期的管理，一定程度上提高性能。

- JDK给我们提供了Excutor框架来使用线程池，它是线程池的基础。

  - Executor提供了一种将“任务提交”与“任务执行”分离开来的机制(解耦)。

- JDK线程池的总体api架构：

  ![image-20210113213633274](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113213633274.png)

- Executor接口：

  ![image-20210113213654793](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113213654793.png)

  - 线程池的初衷是屏蔽线程的创建、使用及调度(复用)等细节，让使用者通过简单的提交即可执行任务。

    - 基于这个设计思想，JDK设计了一个顶级接口：Executor。
    - 它只定义了一个方法。正如方法注释所说的，execute()作为接口方法，并没有强制子类如何实现。只要子类最终实现的效果是下面这样即可：

    ```java
    // 搞一个Executor实例
    Executor executor = anExecutor;
    // 提交任务即可，其他细节它帮你搞定（屏蔽底层细节，分离线程类和任务类）
    executor.execute(new RunnableTask1());
    executor.execute(new RunnableTask2());
    ```

  - 你可以在方法内部直接使用new Thread().start()：

    ```java
    class ThreadPerTaskExecutor implements Executor {
        public void execute(Runnable r) {
            new Thread(r).start();
        }
    }
    ```

  - 甚至可以实现同步调用（接口方法并没有强制子类实现必须走异步调用）：

    ```java
    class DirectExecutor implements Executor {
        public void execute(Runnable r) {
            r.run();
        }
    }
    ```

- ExcutorService接口：

  ![image-20210113213721217](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113213721217.png)

  - 如果说线程池的顶级接口Executor是开放式的，允许子类随便选择同步实现还是异步实现，那么作为儿子，ExecutorService则已经下定决定要走异步了，也就是创建线程执行任务，而不是同步调用。
  - ExecutorService的贡献有两点：
    - 突破性地引入“线程池状态”的概念，要求子类必须实现诸如shutDown()、shutDownNow()、isTerminated()、isShutDown()等方法以便反馈线程池的状态：已关闭？任务已结束？
    - 另外还引入了submit()方法。它的灵感来自于Executor的execute()，但execute()是没有返回值的，而submit()会返回一个Future对象。
    - 通过submit()提交一个任务后会返回一个Future接口的实例，我们可以通过这个实例获取结果或者取消任务。

- ScheduledExecutorService接口：

  ![image-20210113213839293](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113213839293.png)

  - 如果说ExecutorService已经基本规定了一个线程池需要提供哪些功能，那么ScheduledExecutorService则在此基础上提出了定时任务线程池的概念，也就是说JDK认为定时任务线程池是个更细化的领域，单独抽取一个接口描述。
  - ScheduledExecutorService要求子类实现定时方法，能够周期性地执行任务。与submit()类似，当任务提交给这些定时方法后，会返回ScheduledFuture，本质也是用来掌控任务进度的。

- AbstractExecutorService类：

  ![image-20210113213825071](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113213825071.png)

  - AbstractExecutorService做了两件事：

    - 新增newTaskFor()方法，将Runnable和Callable两种任务**统一**为FutureTask类型。
    - 对ExecutorService接口的submit()等方法做了**初步**实现。

  - newTaskFor()：包装Runnable和Callable，统一任务类型。

    - 无论submit()接收的是Runnable还是Callable任务，它都会将其包装成一个RunnableFuture返回。

      ![image-20210114204834438](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114204834438.png)

    - 而RunnableFuture也只是接口，实际返回的是FutureTask。

      ![image-20210114204858042](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114204858042.png)

      ![image-20210114204910627](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114204910627.png)

    - 包装成功后，再调用execute()执行提交的任务。之前学习过，Executor#execute(Runnable r)接收Runnable任务，而FutureTask实现了Runnable，所以可以传进去。

      ![image-20210114205044549](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114205044549.png)

  - 为什么要包装Runnable和Callable？

    - Runnable是JDK1.0就设计的，当初设计run()是没有返回值的。

    - 而Callable和线程池都是JDK1.5提出来的，Callable#call()和ExecutorService#submit()都有返回值。

    - 于是只好再来一个FutureTask包装一下：

      ![image-20210114205145181](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114205145181.png)

    - 历史原因，需要包装Runnable和Callable，统一返回值问题

    - 简化线程池的任务类型，统一操作FutureTask，**submit()底层会调用execute(Runnable r)**，而包装后的FutureTask实现了Runnable

    ![image-20210114205312171](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114205312171.png)

- **常用的**Java的线程池如果粗分的话其实有三大类：

  - ForkJoinPool。
  - ThreadPoolExecutor。
  - ScheduledExecutorService。
  
- ForkJoinPool线程池：

  - JDK1.7中新增的一个线程池，与ThreadPoolExecutor一样，同样继承了AbstractExecutorService。ForkJoinPool是Fork/Join框架的两大核心类之一。
  - 与其它类型的ExecutorService相比，其主要的不同在于采用了工作窃取算法(work-stealing)：
    - 所有池中线程会尝试找到并执行已被提交到池中的或由其他线程创建的任务。
    - 这样很少有线程会处于空闲状态，非常高效。这使得能够有效地处理以下情景：大多数由任务产生大量子任务的情况；从外部客户端大量提交小任务到池中的情况。

- Callable和Future：

  - 可以简单认为：Callable就是Runnable的扩展。Runnable没有返回值，不能抛出受检查的异常，而Callable可以。
  - 也就是说：当我们的任务需要返回值的时，我们就可以使用Callable。

  ![image-20210113214126218](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113214126218.png)

  - Future一般我们认为是Callable的返回值，但他其实代表的是任务的生命周期(当然了，它是能获取得到Callable的返回值的)。

  ![image-20210113214205440](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113214205440.png)

  - 简单用法：

  ```java
  public class CallableDemo {
      public static void main(String[] args) throws InterruptedException, ExecutionException {
          // 创建线程池对象
          ExecutorService pool = Executors.newFixedThreadPool(2);
  
          // 可以执行Runnable对象或者Callable对象代表的线程
          Future<Integer> f1 = pool.submit(new MyCallable(100));
          Future<Integer> f2 = pool.submit(new MyCallable(200));
  
          // V get()
          Integer i1 = f1.get();
          Integer i2 = f2.get();
  
          System.out.println(i1);
          System.out.println(i2);
  
          // 结束
          pool.shutdown();
      }
  }
  
  public class MyCallable implements Callable<Integer> {
  
      private int number;
  
      public MyCallable(int number) {
          this.number = number;
      }
  
      @Override
      public Integer call() throws Exception {
          int sum = 0;
          for (int x = 1; x <= number; x++) {
              sum += x;
          }
          return sum;
      }
  
  }
  ```

- ThreadPoolExecutor的贡献有以下几点：

  - 首次引入corePoolSize、maximumPoolSize、keepAliveTime、ThreadFactory等参数配置，真正实现了线程的“池化”。线程池里的线程数会在这几个阈值的影响下动态调整，大大提高了执行效率和复用率。
  - 将任务包装成Worker，加入阻塞队列，缓冲执行。
  - 内置4种拒绝策略，释放执行压力。
  
  ![image-20210115190717071](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115190717071.png)
  
- ThreadPoolExecutor：

  - 使用线程池的线程来完成认为，一般使用Executors工厂方法来配置。
  - 有属性核心线程数量和最大线程数量。
    - 如果运行线程的数量少于核心线程数量，则创建新的线程处理请求。
    - 如果运行线程的数量大于核心线程数量，小于最大线程数量，则当队列满的时候才创建新的线程。
    - 如果核心线程数量等于最大线程数量，那么将创建固定大小的连接池。
    - 如果设置了最大线程数量为无穷，那么允许线程池适合任意的并发数量。
  - 按需初始化线程池：默认情况下，只有当任务请求过来，才会初始化核心线程。
  - 创建新线程：
    - 新创建的线程默认由线程工厂创建，都是基于同一个线程组，相同优先级，没有守护线程。
    - 如果线程创建失败，执行器会继续执行，但是不会执行任务。
  - 线程空闲时间：当前线程数大于核心线程数，如果空闲时间已经超过了，那该线程会销毁。
  - 阻塞队列：
    - 对于新任务，如果少于核心线程数的线程正在运行，会添加一个新的线程处理该任务，而不是放入阻塞队列中。
    - 如果队列满了，会先开一个线程处理。除非超过了最大线程数，这样的话会拒绝这个请求。
  - 三种排队策略：
    - 同步移交：
      - 该策略不会将任务放到队列中，而是会直接移交给执行它的线程。
      - 如果当前没有线程执行它，那很可能会新建一个线程
      - —般用于线程池是无界限的情况。
    - 无界限策略：
      - 如果所有的核心线程都在工作，那么新的线程会在队列中等待。
      - 因此,线程的创建不会多于核心线程的数量(其他的都在队列中等待了)。
    - 有界限策略：
      - 折衷线程数量和阻塞队列长度，避免资源耗尽的情况发生。
      - 如果线程池比较小，而队列比较大。一定程度上减少内存的使用量。但是代价是限制吞吐量!
  - 拒绝任务：
    - 线程池关闭。
    - 线程数量满了和队列饱和了。
  - 四种拒绝策略：
    - 默认直接抛出异常。
    - 用调用者所在的线程执行任务。
    - 直接丢掉这个任务。
    - 丢弃最久的一条任务。

- 内部状态：

  - 变量ctl定义为AtomicInteger，记录了“线程池中的任务数量”和“线程池的状态”两个信息。。

    ![image-20210113220142383](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113220142383.png)

  - RUNNING：线程池能够接受新任务，以及对新添加的任务进行处理。

  - SHUTDOWN：线程池不可以接受新任务，但是可以对已添加的任务进行处理。

  - STOP：线程池不接收新任务，不处理已添加的任务，并且会中断正在处理的任务。

  - TIDYING：当所有的任务已终止，ctl记录的"任务数量"为0，线程池会变为TIDYING状态。当线程池变为TIDYING状态时，会执行钩子函数terminated()。terminated()在ThreadPoolExecutor类中是空的，若用户想在线程池变为TIDYING时，进行相应的处理；可以通过重载terminated()函数来实现。

  - TERMINATED：线程池彻底终止的状态。

  ![image-20210113220244373](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113220244373.png)

- execute()实现：

  - 从AbstractExecutorService的submit()可以看出，子类的execute()需要接收一个FutureTask(实现了Runnable和Future)，并在任务执行结束后把结果设置到FutureTask。

    ![image-20210115190959004](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115190959004.png)

  - ThreadPoolExecutor对Executor#execute()的实现：

    ![image-20210115191022456](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115191022456.png)

    - 整体来说execute()就两个操作：

      - addWorker()成功，把任务提交到队列并执行。
      - addWorker()失败，触发拒绝策略。
      - Runnable command其实是FutureTask对象。

    - execute()隐藏了很多细节，具体的实现其实封装到了addWorker()中。addWorker()的大概流程是：

      - 把task封装成Worker，Worker = task + thread。
      - 经过一系列步骤，最终会执行Worker里封装的thread.start()。

      ![image-20210115191354342](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115191354342.png)

    - Worker是啥。其实就是worker = task(FutureTask) + thread。而Worker其实也是一个Runnable。

      ![image-20210115191525950](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115191525950.png)

    - 那就意味着线程启动时会执行Worker的run()方法。

      ![image-20210115191603012](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115191603012.png)

    - run()最终又调用runWorker()：

      ![image-20210115191643942](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115191643942.png)

  - 整个调用链是：

    - submit(Runnable/Callable) --> 包装成FutureTask --> execute(FutureTask) --> addWorker(FutureTask) --> **异步线程**执行worker.run() --> runWorker(Worker) --> worker.futureTask.run()。
    - 注意，Executor#execute()和FutureTask#run()之间不是同步调用，而是通过线程池的异步线程执行的。

  - FutureTask包装Runnable和Callable：

    - FutureTask内部维护Callable类型的成员变量，对于Callable任务，直接赋值即可。
    - 对于Runnable任务，需要先调用Executors#callable()把Runnable先包装成Callable。
    - 所以现在执行FutureTask#run()只需把内部的callable取出来，调用callable.call()即可：

    ![image-20210115192003773](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115192003773.png)

  - FutureTask.outcome就是用来存结果的。

    ![image-20210115192034045](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115192034045.png)

  - 对 FutureTask = 任务 + 最终结果 的理解，因为：

    **FutureTask = FutureTask.callable（任务）+ FutureTask.outcome（结果）**。

  - 而FutureTask#get()。get()的阻塞是通过 for循环 + LockSupport 完成的。

    ![image-20210115192111224](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115192111224.png)

  - 当一个Runnable或者Callable任务通过submit()传入线程池时，会先在内部把任务包装为FutureTask，然后调用execute()方法。也就是说：**submit() = execute() + FutureTask包装**。

- 线程池的三种默认实现：

  - newFixedThreadPool：一个固定线程数的线程池，它将返回一个corePoolSize和maximumPoolSize相等的线程池。
  - newCachedThreadPool：非常有弹性的线程池，对于新的任务，如果此时线程池里没有空闲线程，线程池会毫不犹豫的创建一条新的线程去处理这个任务。
  - SingleThreadExecutor：使用单个worker线程的Executor。

- 构造方法可以让我们自定义(扩展)线程池，包括：

  1. 指定核心线程数量
  2. 指定最大线程数量
  3. 允许线程空闲时间
  4. 时间对象
  5. 阻塞队列
  6. 线程工厂
  7. 任务拒绝策略

- execute执行方法：

  ```java
  public void execute(Runnable command) {
      if (command == null)
          throw new NullPointerException();
      int c = ctl.get();
      //如果线程池中运行的线程数量<corePoolSize，则创建新线程来处理请求，即使其他辅助线程是空闲的。
      if (workerCountOf(c) < corePoolSize) {
          if (addWorker(command, true))
              return;
          c = ctl.get();
      }
  
      //如果线程池中运行的线程数量>=corePoolSize，且线程池处于RUNNING状态，且把提交的任务成功放入阻塞队列中，就再次检查线程池的状态，
      // 1.如果线程池不是RUNNING状态，且成功从阻塞队列中删除任务，则该任务由当前 RejectedExecutionHandler 处理。
      // 2.否则如果线程池中运行的线程数量为0，则通过addWorker(null, false)尝试新建一个线程，新建线程对应的任务为null。
      if (isRunning(c) && workQueue.offer(command)) {
          int recheck = ctl.get();
          if (! isRunning(recheck) && remove(command))
              reject(command);
          else if (workerCountOf(recheck) == 0)
              addWorker(null, false);
      }
      // 如果以上两种case不成立，即没能将任务成功放入阻塞队列中，且addWoker新建线程失败，则该任务由当前 RejectedExecutionHandler 处理。
      else if (!addWorker(command, false))
          reject(command);
  }
  ```

- ThreadPoolExecutor提供了`shutdown()`和`shutdownNow()`两个方法来关闭线程池：

  - shutdown：

    ![image-20210113220925612](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113220925612.png)

  - shutdownNow：

    ![image-20210113220949597](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113220949597.png)

  - 区别：

    - 调用shutdown()后，线程池状态立刻变为SHUTDOWN，而调用shutdownNow()，线程池状态立刻变为STOP。
    - shutdown()等待任务执行完才中断线程，而shutdownNow()不等任务执行完就中断了线程。

## 5、死锁

- 造成死锁的原因可以概括成三句话：

  - 当前线程拥有其他线程需要的资源
  - 当前线程等待其他线程已拥有的资源
  - 都不放弃自己拥有的资源

- 锁顺序死锁：

  ```java
  public class LeftRightDeadlock {
      private final Object left = new Object();
      private final Object right = new Object();
  
      public void leftRight() {
          // 得到left锁
          synchronized (left) {
              // 得到right锁
              synchronized (right) {
                  doSomething();
              }
          }
      }
  
      public void rightLeft() {
          // 得到right锁
          synchronized (right) {
              // 得到left锁
              synchronized (left) {
                  doSomethingElse();
              }
          }
      }
  }
  ```

  - 线程A调用leftRight()方法，得到left锁。
  - 同时线程B调用rightLeft()方法，得到right锁。
  - 线程A和线程B都继续执行，此时线程A需要right锁才能继续往下执行。此时线程B需要left锁才能继续往下执行。
  - 但是：线程A的left锁并没有释放，线程B的right锁也没有释放。
  - 所以他们都只能等待，而这种等待是无期限的-->永久等待-->死锁。

- 动态锁顺序死锁：

  ```java
  // 转账
  public static void transferMoney(Account fromAccount,
                                   Account toAccount,
                                   DollarAmount amount)
      throws InsufficientFundsException {
  
      // 锁定汇账账户
      synchronized (fromAccount) {
          // 锁定来账账户
          synchronized (toAccount) {
  
              // 判余额是否大于0
              if (fromAccount.getBalance().compareTo(amount) < 0) {
                  throw new InsufficientFundsException();
              } else {
  
                  // 汇账账户减钱
                  fromAccount.debit(amount);
  
                  // 来账账户增钱
                  toAccount.credit(amount);
              }
          }
      }
  }
  ```

  - 如果两个线程同时调用`transferMoney()`。
  - 线程A从X账户向Y账户转账。
  - 线程B从账户Y向账户X转账。
  - 那么就会发生死锁。

- 协作对象之间发生死锁：

  ```java
  public class CooperatingDeadlock {
      // Warning: deadlock-prone!
      class Taxi {
          @GuardedBy("this") private Point location, destination;
          private final Dispatcher dispatcher;
  
          public Taxi(Dispatcher dispatcher) {
              this.dispatcher = dispatcher;
          }
  
          public synchronized Point getLocation() {
              return location;
          }
  
          // setLocation 需要Taxi内置锁
          public synchronized void setLocation(Point location) {
              this.location = location;
              if (location.equals(destination))
                  // 调用notifyAvailable()需要Dispatcher内置锁
                  dispatcher.notifyAvailable(this);
          }
  
          public synchronized Point getDestination() {
              return destination;
          }
  
          public synchronized void setDestination(Point destination) {
              this.destination = destination;
          }
      }
  
      class Dispatcher {
          @GuardedBy("this") private final Set<Taxi> taxis;
          @GuardedBy("this") private final Set<Taxi> availableTaxis;
  
          public Dispatcher() {
              taxis = new HashSet<Taxi>();
              availableTaxis = new HashSet<Taxi>();
          }
  
          public synchronized void notifyAvailable(Taxi taxi) {
              availableTaxis.add(taxi);
          }
  
          // 调用getImage()需要Dispatcher内置锁
          public synchronized Image getImage() {
              Image image = new Image();
              for (Taxi t : taxis)
                  // 调用getLocation()需要Taxi内置锁
                  image.drawMarker(t.getLocation());
              return image;
          }
      }
  
      class Image {
          public void drawMarker(Point p) {
          }
      }
  }
  ```

  - 上面的getImage()和setLocation(Point location)都需要获取两个锁的。
  - 并且在操作途中是没有释放锁的。
  - 这就是隐式获取两个锁(对象之间协作)，这种方式也很容易造成死锁。

- 避免死锁的方法：

  - 固定加锁的顺序(针对锁顺序死锁)。
    - 比如说，根据两个对象的hash值来确定加锁的顺序。
  - 开放调用(针对对象之间协作造成的死锁)，减小加锁的范围。
    - 如果在调用某个方法时不需要持有锁，那么这种调用被称为开放调用。
    - 同步代码块最好仅被用于保护那些涉及共享状态的操作。也就是说仅仅在使用到共享变量时才同步，使用完后就释放锁。
  - 使用定时锁-->tryLock()。如果等待获取锁时间超时，则抛出异常而不是一直等待！

- 死锁检测：

  - JconsoleJDK自带的图形化界面工具，使用JDK给我们的的工具JConsole
  - Jstack是JDK自带的命令行工具，主要用于线程Dump分析。

## 6、同步工具类

- CountDownLatch(闭锁)：

  - CountDownLatch是一个同步的辅助类，允许一个或多个线程一直等待，直到其它线程完成它们的操作。
  - 常用的API其实就两个:`await()`和`countDown()`。
  - 用一个count初始化CountDownLatch，然后需要等待的线程调用await方法。await方法会一直受阻塞直到count=0。而其它线程完成自己的操作后，调用countDown()使计数器count减1。当count减到0时，所有在等待的线程均会被释放。
  - 就是通过count变量来控制等待，如果count值为0了(其他线程的任务都完成了)，那就可以继续执行。

  ```java
  public class Test {
  
      public static void main(String[] args) {
  
          final CountDownLatch countDownLatch = new CountDownLatch(5);
  
          System.out.println("现在6点下班了.....");
  
          // 3y线程启动
          new Thread(new Runnable() {
              @Override
              public void run() {
  
                  try {
                      // 这里调用的是await()不是wait()
                      countDownLatch.await();
                  } catch (InterruptedException e) {
                      e.printStackTrace();
                  }
                  System.out.println("...其他的5个员工走光了，3y终于可以走了");
              }
          }).start();
  
          // 其他员工线程启动
          for (int i = 0; i < 5; i++) {
              new Thread(new Runnable() {
                  @Override
                  public void run() {
                      System.out.println("员工xxxx下班了");
                      countDownLatch.countDown();
                  }
              }).start();
          }
      }
  }
  ```

- CyclicBarrier(栅栏)：

  - CyclicBarrier允许一组线程在某个公共屏障点互相等待，直到规定数量的线程到达。叫做cyclic是因为当所有等待线程都被释放以后，CyclicBarrier可以被重用(对比于CountDownLatch是不能重用的)。
  - CountDownLatch注重的是等待其他线程完成，CyclicBarrier注重的是：当线程到达某个状态后，暂停下来等待其他线程，所有线程均到达以后，继续执行。
  - 同样是使用一个count初始化。在程序中使用`await()`，当等待的线程数量到达count是，则同时被释放。

  ```java
  public class Test {
  
      public static void main(String[] args) {
  
          final CyclicBarrier CyclicBarrier = new CyclicBarrier(2);
          for (int i = 0; i < 2; i++) {
  
              new Thread(() -> {
  
                  String name = Thread.currentThread().getName();
                  if (name.equals("Thread-0")) {
                      name = "3y";
                  } else {
                      name = "女朋友";
                  }
                  System.out.println(name + "到了体育西");
                  try {
  
                      // 两个人都要到体育西才能发朋友圈
                      CyclicBarrier.await();
                      // 他俩到达了体育西，看见了对方发了一条朋友圈：
                      System.out.println("跟" + name + "去夜上海吃东西~");
                  } catch (InterruptedException e) {
                      e.printStackTrace();
                  } catch (BrokenBarrierException e) {
                      e.printStackTrace();
                  }
              }).start();
          }
      }
  }
  ```

- Semaphore(信号量)：

  - Semaphore(信号量)实际上就是可以控制同时访问的线程个数，它维护了一组"许可证"。
  - 当调用acquire()方法时，会消费一个许可证。如果没有许可证了，会阻塞起来。
  - 当调用release()方法时，会添加一个许可证。
  - 这些"许可证"的个数其实也就是一个count变量。

  ```java
  public class Test {
  
      public static void main(String[] args) {
  
          // 假设有50个同时来到酸奶店门口
          int nums = 50;
  
          // 酸奶店只能容纳10个人同时挑选酸奶
          Semaphore semaphore = new Semaphore(10);
  
          for (int i = 0; i < nums; i++) {
              int finalI = i;
              new Thread(() -> {
                  try {
                      // 有"号"的才能进酸奶店挑选购买
                      semaphore.acquire();
  
                      System.out.println("顾客" + finalI + "在挑选商品，购买...");
  
                      // 假设挑选了xx长时间，购买了
                      Thread.sleep(1000);
  
                      // 归还一个许可，后边的就可以进来购买了
                      System.out.println("顾客" + finalI + "购买完毕了...");
                      semaphore.release();
  
  
  
                  } catch (InterruptedException e) {
                      e.printStackTrace();
                  }
              }).start();
  
          }
  
      }
  }
  ```

- 原子变量类：

  - 原子变量类在`java.util.concurrent.atomic`包下。

    ![image-20210114194327132](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114194327132.png)

  - 基本类型：

  - - AtomicBoolean：布尔型
    - AtomicInteger：整型
    - AtomicLong：长整型

  - 数组：

  - - AtomicIntegerArray：数组里的整型
    - AtomicLongArray：数组里的长整型
    - AtomicReferenceArray：数组里的引用类型

  - 引用类型：

  - - AtomicReference：引用类型
    - AtomicStampedReference：带有版本号的引用类型
    - AtomicMarkableReference：带有标记位的引用类型

  - 对象的属性：

  - - AtomicIntegerFieldUpdater：对象的属性是整型
    - AtomicLongFieldUpdater：对象的属性是长整型
    - AtomicReferenceFieldUpdater：对象的属性是引用类型

  - JDK8新增DoubleAccumulator、LongAccumulator、DoubleAdder、LongAdder

  - - 是对AtomicLong等类的改进。比如LongAccumulator与LongAdder在高并发环境下比AtomicLong更高效。

  - 从原理上概述就是：Atomic包的类的实现绝大部分调用Unsafe的方法，而Unsafe底层实际上是调用C代码，C代码调用汇编，最后生成出一条CPU指令cmpxchg，完成操作。这也就为啥CAS是原子性的，因为它是一条CPU指令，不会被打断。

- 使用原子变量类：

  ```java
  class Count{
  
      // 共享变量(使用AtomicInteger来替代Synchronized锁)
      private AtomicInteger count = new AtomicInteger(0);
  
      public Integer getCount() {
          return count.get();
      }
      public void increase() {
          count.incrementAndGet();
      }
  }
  ```

- 解决ABA问题：

  - 使用JDK给我们提供的AtomicStampedReference和AtomicMarkableReference类。简单来说就是在给为这个对象提供了一个版本，并且这个版本是自动更新的。
  - 原理大概就是：维护了一个Pair对象，Pair对象存储我们的对象引用和一个stamp值。每次CAS比较的是两个Pair对象。

  ```java
  // Pair对象
  private static class Pair<T> {
      final T reference;
      final int stamp;
      private Pair(T reference, int stamp) {
          this.reference = reference;
          this.stamp = stamp;
      }
      static <T> Pair<T> of(T reference, int stamp) {
          return new Pair<T>(reference, stamp);
      }
  }
  
  private volatile Pair<V> pair;
  
  // 比较的是Pari对象
  public boolean compareAndSet(V   expectedReference,
                               V   newReference,
                               int expectedStamp,
                               int newStamp) {
      Pair<V> current = pair;
      return
          expectedReference == current.reference &&
          expectedStamp == current.stamp &&
          ((newReference == current.reference &&
            newStamp == current.stamp) ||
           casPair(current, Pair.of(newReference, newStamp)));
  }
  ```

- 如果是 JDK8，推荐使用 LongAdder 对象，比 AtomicLong 性能更好(减少乐观锁的重试次数)：

  - 使用AtomicLong时，在高并发下大量线程会同时去竞争更新同一个原子变量，但是由于同时只有一个线程的CAS会成功，所以其他线程会不断尝试自旋尝试CAS操作，这会浪费不少的CPU资源。
  - 而LongAdder可以概括成这样：
    - 因为我们需要的是一个数字的值，而且每次对这个数字做的是加减操作。
    - 内部核心数据value分离成一个数组(Cell)，每个线程访问时,通过哈希等算法映射到其中一个数字进行计数。
    - 最终的计数结果，则为这个数组的求和累加。
  - 简单来说就是将一个值分散成多个值，在并发的时候就可以分散压力，性能有所提高。































