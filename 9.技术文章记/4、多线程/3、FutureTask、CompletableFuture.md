学习自：

- [多线程基础](https://www.yuque.com/books/share/2b434c74-ed3a-470e-b148-b4c94ba14535/vq2ti4)
- [浅谈CompletableFuture](https://www.yuque.com/books/share/2b434c74-ed3a-470e-b148-b4c94ba14535/xzaqte)

## 1、FutureTask

- 无论是Runnable还是Callable，它们其实和线程没半毛钱关系，它们是任务类，只有Thread是线程类。

- JDK那么多类，有且仅有Thread类能通过start0()方法向操作系统申请线程资源（本地方法）。

  ![image-20210114210703219](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114210703219.png)

- 并且，在JVM的设定中Java的线程和操作系统的线程是**一一对应**的：

  ![image-20210114210741531](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114210741531.png)

- 而Runnable和Callable如果没有线程或线程池去执行它们，就什么也不是，只是一坨普通的代码。

  ```java
  public class AsyncAndWaitTest {
  
      public static void main(String[] args) throws ExecutionException, InterruptedException {
          // 方式1：重写Thread#run()
          Thread thread = new Thread() {
              @Override
              public void run() {
                  System.out.println(Thread.currentThread().getName() + "========>正在执行");
              }
          };
          thread.start();
  
          // 方式2：构造方法传入Runnable实例
          new Thread(() -> {
              System.out.println(Thread.currentThread().getName() + "========>正在执行");
          }).start();
  
          // 方式3：线程池 + Callable
          ExecutorService executorService = Executors.newSingleThreadExecutor();
          Future<String> submit = executorService.submit(() -> {
              System.out.println(Thread.currentThread().getName() + "========>正在执行");
              Thread.sleep(3 * 1000L);
              return "success";
          });
          String result = submit.get();
          System.out.println("result=======>" + result);
          // 关闭线程池
          executorService.shutdown();
      }
  }
  ```

- FutureTask = 任务 + 结果。

  - 第四种方法：通过Thread的构造器传入Runnable实例（FutureTask，内部包装了Runnable/Callable）。
  - 基本使用：

  ```java
  public class AsyncAndWaitTest {
  
      public static void main(String[] args) throws ExecutionException, InterruptedException {
  
          // FutureTask实现了Runnable，可以看做是一个任务
          FutureTask<String> futureTask = new FutureTask<>(new Callable<String>() {
              @Override
              public String call() throws Exception {
                  System.out.println(Thread.currentThread().getName() + "========>正在执行");
                  try {
                      Thread.sleep(3 * 1000L);
                  } catch (InterruptedException e) {
                      e.printStackTrace();
                  }
                  return "success";
              }
          });
          
          System.out.println(Thread.currentThread().getName() + "========>启动任务");
  
          // 传入futureTask，启动线程执行任务
          new Thread(futureTask).start();
  
          // 但它同时又实现了Future，可以获取异步结果（会阻塞3秒）
          String result = futureTask.get();
          System.out.println("任务执行结束，result====>" + result);
      }
  
  }
  ```

- FutureTask这个名字！它既是一个任务，又能存储任务执行的结果。反映在程序上就是既能传入Thread执行，又能通过futureTask.get()获取任务执行结果。

![image-20210114211605350](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114211605350.png)

- FutureTask有以下2个特征：

  - 能包装Runnable和Callable（构造器传入），但本身却又实现了Runnable接口，即本质是Runnable。
  - 既然是Runnable，所以FutureTask能作为任务被Thread执行，但诡异的是FutureTask#get()可以获取结果。

- FutureTask如何包装Runnable/Callable：

  - 使用：

    ![image-20210114211841491](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114211841491.png)

  - 通过FutureTask构造器传入Runnable/Callable的，所以我们去看看FutureTask的构造器：

    ![image-20210114211941786](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114211941786.png)

  - FutureTask内部维护Callable类型的成员变量，对于Callable任务，直接赋值即可：

    ![image-20210114212020753](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114212020753.png)

  - 而对于Runnable任务，需要先调用Executors#callable()把Runnable先包装成Callable：

    ![image-20210114212109758](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114212109758.png)

  - Executors#callable()用到了适配器模式：

    ![image-20210114212158174](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114212158174.png)

  - 而RunnableAdapter实现了Callable接口，所以包装后的RunnableAdapter可以赋值给FutureTask.callable。

    ![image-20210114212212182](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114212212182.png)

  - 也就是说：

    - Runnable --> Executors.callable() --> RunnableAdapter implements Callable --> FutureTask.callable
    - Callable --> FutureTask.callable

- Runnable和Callable的返回值问题：

  - Callable#call()是有返回值的，而Runnable#run()没有。它们都包装成FutureTask后，一个有返回值，一个没返回值，怎么处理呢。

  - 设计成有返回值的，毕竟Callable.call()明明有返回值，你总不能硬生生丢掉吧。至于Runnable.run()确实没返回值，但也好办，搞个假的返回即可。

    ![image-20210114212450677](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114212450677.png)

  - 等到Thread执行FutureTask时，会先取出FutureTask.callable，然后调用callable.call()：

    - 如果是真的Callable，调用Callable.call()会返回真实的result
    - 如果是Runnable包装的RunnableAdapter，会返回事先传入的result
    - 这也是上面的程序中，为什么Runnable要多传一个参数的原因

- FutureTask是如何被Thread执行的：

  - thread执行自己的run方法。这里的target是FutureTask，所以target.run()就是FutureTask#run()。

    ![image-20210114212942670](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114212942670.png)

  - 结果最终存哪呢？

    ![image-20210114213058867](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114213058867.png)

  - 也是FutureTask的一个成员变量：

    ![image-20210114213115890](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114213115890.png)

  - 进一步印证了说 FutureTask = 任务 + 结果。

- 为什么get()是阻塞的？

  - 在FutureTask中定义了很多任务状态：

    ![image-20210114213239662](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114213239662.png)

    - 刚创建
    - 即将完成
    - 完成
    - 抛异常
    - 任务取消
    - 任务即将被打断
    - 任务被打断

  - 这些状态的设置意义在哪？

    - 一个任务，有时可能非常耗时。而当用户使用futureTask.get()时，必然是希望获取最终结果的。如果FutureTask不帮我们阻塞，就有可能获取空结果。此时为了获取最终结果，用户不得不在外部自己写阻塞程序。
    - 所以，get()内部会判断当前任务的状态，只有当任务完成才返回。

  - 线程从阻塞到获取结果，中间必然经历类似唤醒的操作，怎么做到的？

    - 秘密就在awaitDone()：核心的就是 for循环 + LockSupport。
    - `LockSupport`是一个线程阻塞工具类，所有的方法都是静态方法，可以让线程在任意位置阻塞，当然也有唤醒的方法。
    - `LockSupport`主要有两类方法：`park`和`unpark`。即让线程停下和启动。

    ![image-20210114213403621](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114213403621.png)

    - 类似于：

      ```java
      public class ParkTest {
      
          @Test
          public void testPark() throws InterruptedException {
              // 存储线程
              List<Thread> threadList = new ArrayList<>();
      
              // 创建5个线程
              for (int i = 0; i < 5; i++) {
                  Thread thread = new Thread(() -> {
                      System.out.println("我是" + Thread.currentThread().getName() + ", 我开始工作了~");
                      LockSupport.park(this);
                      System.out.println("我是" + Thread.currentThread().getName() + ", 我又活过来了~");
                  });
                  thread.start();
                  threadList.add(thread);
              }
      
              Thread.sleep(3 * 1000L);
              System.out.println("====== 所有线程都阻塞了，3秒后全部恢复了 ======");
      
              // unPark()所有线程
              for (Thread thread : threadList) {
                  LockSupport.unpark(thread);
              }
      
              // 等所有线程执行完毕
              Thread.sleep(3 * 1000L);
          }
      
      }
      ```

  - 也就是说，调用get()后，如果当前没有结果，就会被park()，等有了结果再unpark()并往下走：

    ![image-20210114213807726](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114213807726.png)

  - 取出outcome返回：

    ![image-20210114213822669](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114213822669.png)

- FutureTask如何异步返回结果：

  ![image-20210114214552476](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114214552476.png)

  - 往线程池submit了一个Callable，结果马上返回了result（FutureTask）：

    ![image-20210114214746646](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114214746646.png)

  - 观察：

    - 返回的FutureTask里包含刚才丢进去的Callable
    - result.outcome目前还是null

  - 实际上，返回的futureTask并不是真正的结果，它内部持有outcome引用，它才指向真正的结果。而在任务完成之前，outcome引用指向的是null。

    ![image-20210114214843951](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114214843951.png)

- 何时调用futureTask.get()？

  - 用户调用get()必然是想到得到最终结果的，所以为了保证一定能得到结果，JDK把FutureTask#get()设计成阻塞的。

  - 建议不要立即调用get()，否则程序完全没有发挥异步优势，由异步阻塞变成同步阻塞。

    ![image-20210114215039393](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114215039393.png)

  - 开启多线程，当然应该发挥多线程的优势：

    ![image-20210114215102247](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114215102247.png)

    ![image-20210114215154400](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114215154400.png)

- isDone() + get()：

  - 但是实际开发时，异步线程具体会耗时多久有时很难预估，受网络、数据库等各方面影响。所以很难做到在合适的地方get()然后一击即中。
  - FutureTask提供了isDone()方法：

  ![image-20210114215310620](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114215310620.png)

  - 当然，这种做法也不是很优雅。JDK1.8提供了CompletableFuture解决这个问题。



## 2、CompletableFuture

- FutureTask#get()本身是阻塞的，假设当前有三个下载任务在执行：

  - task1（预计耗时5秒）
  - task2（预计耗时1秒）
  - task3（预计耗时1秒）

- 如果阻塞获取时不凑巧把task1.get()排在最前面，那么会造成一定的资源浪费，因为task2和task3早就已经准备好了，可以先拿出来处理，以获得最佳的用户体验。

  ![image-20210115192952604](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115192952604.png)

- 虽然可以结合轮询+isDone()的方式改进，但仍存在以下问题：

  - 轮询间隔多少合适？
  - 为了避免while(true)阻塞主线程逻辑，可能需要开启单独的线程轮询，浪费一个线程。
  - 仍然无法处理复杂的任务依赖关系。

- CompletableFuture的简单使用：

  ```java
  @Test
  public void testCallBack() throws InterruptedException, ExecutionException {
      // 提交一个任务，返回CompletableFuture
      CompletableFuture<String> completableFuture = CompletableFuture.supplyAsync(new Supplier<String>() {
          @Override
          public String get() {
              System.out.println("=============>异步线程开始...");
              System.out.println("=============>异步线程为：" + Thread.currentThread().getName());
              try {
                  TimeUnit.SECONDS.sleep(10);
              } catch (InterruptedException e) {
                  e.printStackTrace();
              }
              System.out.println("=============>异步线程结束...");
              return "supplierResult";
          }
      });
      
  	// 阻塞获取结果
      System.out.println("异步结果是:" + completableFuture.get());
      System.out.println("main结束");
  }
  ```

  - 整个过程看起来和同步没啥区别，因为我们在main线程中使用了CompletableFuture#get()，直接阻塞了。

  ![image-20210115193510981](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115193510981.png)

  - CompletableFuture和FutureTask的异同点：

    - 相同：都实现了Future接口，所以都可以使用诸如Future#get()、Future#isDone()、Future#cancel()等方法
    - 不同：

    - - FutureTask实现了Runnable，所以它可以作为任务被执行，且内部维护outcome，可以存储结果
      - CompletableFuture没有实现Runnable，**无法作为任务被执行，**所以你无法把它直接丢给线程池执行，相反地，你可以把Supplier#get()这样的函数式接口实现类丢给它执行
      - **CompletableFuture实现了CompletionStage，支持异步回调**。

  - FutureTask和CompletableFuture最大的区别在于，FutureTask需要我们主动阻塞获取，而CompletableFuture支持异步回调。

  - CompletableFuture好像承担的其实是线程池的角色，而Supplier#get()则对应Runnable#run()、Callable#call()。

- CompletionStage的基本使用：

  ```java
  @Test
  public void testCallBack() throws InterruptedException, ExecutionException {
      // 提交一个任务，返回CompletableFuture（注意，并不是把CompletableFuture提交到线程池，它没有实现Runnable）
      CompletableFuture<String> completableFuture = CompletableFuture.supplyAsync(new Supplier<String>() {
          @Override
          public String get() {
              System.out.println("=============>异步线程开始...");
              System.out.println("=============>异步线程为：" + Thread.currentThread().getName());
              try {
                  TimeUnit.SECONDS.sleep(10);
              } catch (InterruptedException e) {
                  e.printStackTrace();
              }
              System.out.println("=============>异步线程结束...");
              return "supplierResult";
          }
      });
  
      // 异步回调：上面的Supplier#get()返回结果后，异步线程会回调BiConsumer#accept()
      completableFuture.whenComplete(new BiConsumer<String, Throwable>() {
          @Override
          public void accept(String s, Throwable throwable) {
              System.out.println("=============>异步任务结束回调...");
              System.out.println("=============>回调线程为：" + Thread.currentThread().getName());
          }
      });
  
      // CompletableFuture的异步线程是守护线程，一旦main结束就没了，为了看到打印结果，需要让main休眠一会儿
      System.out.println("main结束");
      TimeUnit.SECONDS.sleep(15);
  }
  ```

  - 结果：

    ```html
    =============>异步线程开始...
    =============>异步线程为：ForkJoinPool.commonPool-worker-9
    main结束
    =============>异步线程结束...
    =============>异步任务结束回调...
    =============>回调线程为：ForkJoinPool.commonPool-worker-9
    ```

- 主线程调用了CompletableFuture#whenComplete()：

  - 这个方法定义在CompletionStage接口中：

    ```java
    public interface CompletionStage<T> {
        public CompletionStage<T> whenComplete
            (BiConsumer<? super T, ? super Throwable> action);
        
        // 省略其他方法...
    }
    ```

  - 而CompletableFuture实现了whenComplete()：

    ```java
    public class CompletableFuture<T> implements Future<T>, CompletionStage<T> {
        // 省略其他方法...
        
        public CompletableFuture<T> whenComplete(
            BiConsumer<? super T, ? super Throwable> action) {
            return uniWhenCompleteStage(null, action);
        }
        
        private CompletableFuture<T> uniWhenCompleteStage(Executor e, BiConsumer<? super T, ? super Throwable> f) {
            if (f == null) throw new NullPointerException();
            CompletableFuture<T> d = new CompletableFuture<T>();
            if (e != null || !d.uniWhenComplete(this, f, null)) {
                UniWhenComplete<T> c = new UniWhenComplete<T>(e, d, this, f);
                push(c);
                c.tryFire(SYNC);
            }
            return d;
        }
        
        // 省略其他方法...
    }
    ```

  - CompletionStage是什么呢？

    - 是一个“很简单”的接口。完全独立，没有继承任何其他接口，所有方法都是它自己定义的。

    ```java
    public interface CompletionStage<T> {
        // 定义了超级多类似whenComplete()的方法
    }
    ```

    - 是个不简单的接口。因为CompletableFuture实现Future的同时，还实现了它。Future方法就6、7个，而CompletionStage的方法超级多，所以如果你打开CompletableFuture的源码，目之所及几乎都是它对CompletionStage的实现。

    ```java
    public class CompletableFuture<T> implements Future<T>, CompletionStage<T> {
        // 一些字段
        // 实现Future的方法
        
        // 实现CompletionStage的方法
        // 一些私有方法，配合CompletionStage
        // 一些内部类，配合CompletionStage
    }
    ```

    - 异步回调其实和CompletionStage有着很大的关系。

  - 总而言之，CompletionStage是一个接口，定义了一些方法，CompletableFuture实现了这些方法并设计出了异步回调的机制

- 异步线程会回调BiConsumer#accept()，而CompletableFuture#whenComplete()是主线程调用的。即CompletionStage中定义的诸如whenComplete()等方法虽然和异步回调有关系，但并不是最终被回调的方法，最终被回调的其实是whenComplete(BiConsumer)传进去的BiConsumer#accept()。

  ![image-20210115195319241](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115195319241.png)

- 异步线程哪来的，Supplier如何被执行？

  - 跟随主线程进入CompletableFuture#supplyAsync():

    ![image-20210115195959491](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115195959491.png)

  - 注释：返回一个新的CompletableFuture，该future是由运行在{@link ForkJoinPool#commonPool()}中的任务异步完成的，其值是通过调用给定的Supplier获得的。

    - 即异步线程来自ForkJoinPool线程池。
    - 通过CompletableFuture#supplyAsync(supplier)传入Supplier，返回CompletableFuture对象，它包含一个未来的value，且这个value会在稍后由异步线程执行Supplier#get()产生。

  - CompletableFuture#supplyAsync(supplier)内部调用了asyncSupplyStage(asyncPool, supplier)，此时传入了一个线程池asyncPool，它是CompletableFuture的成员变量：

    ![image-20210115200409657](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115200409657.png)

    ![image-20210115200335107](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115200335107.png)

  - useCommonPool为true时会使用ForkJoinPool，而useCommonPool取决于运行当前程序的硬件是否支持多核CPU。

  - 主线程传进来的Supplier压根没有实现Runnable/Callable接口，怎么被异步线程执行呢？

    ![image-20210115200651552](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115200651552.png)

  - 和ExecutorService#submit()一样的套路：包装成Task再执行。只不过这次被包装成了AsyncSupply，而不是FutureTask：

    ![image-20210115200746525](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115200746525.png)

  - AsyncSupply和当初的FutureTask颇为相似，都实现了Future和Runnable，具备 任务+结果 双重属性：

    ![image-20210115200809769](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115200809769.png)

  - 最终就是把Supplier包装好，传入线程池的execute()中运行。等线程池分配出线程，最终会执行AsyncSupply#run()。

  - AsyncSupply#run()在方法内调用f.get()，也就是Supplier#get()，阻塞获取结果并通过d.completeValue(v)把值设置到CompletableFuture中，而CompletableFuture d已经在上一步asyncSupplyStage()中被返回。最终效果和线程池+FutureTask是一样的，先返回Future实例，再通过引用把值放进去。

  ![image-20210115201646593](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115201646593.png)

  - 从这个层面上来看，**CompletableFuture相当于一个自带线程池的Future**，而CompletableFuture#supplyAsync(Supplier)倒像是ExecutorService#submit(Runnable/Callable)，内部也会包装任务，最终丢给Executor#execute(Task)。
  - 只不过ExecutorService是把Runnable#run()/Callable#call()包装成FutureTask，而CompletableFuture则把乱七八糟的Supplier#get()等函数式接口的方法包装成ForkJoinTask。

- 回调机制的原理：

  - CompletableFuture的回调机制，其实本质上是对多个CompletableFuture内部函数的顺序执行，只不过发起者是异步线程而不是主线程

  - CompletableFuture#thenApply()，与CompletableFuture#whenComplete()本质是一样的（也是CompletableFuture对CompletionStage的实现）：

    ```java
    @RunWith(SpringRunner.class)
    @SpringBootTest
    public class CompletableFutureTest {
    
        @Test
        public void testCallBack() throws InterruptedException {
            // 任务一：把第一个任务推进去，顺便开启异步线程
            CompletableFuture<String> completableFuture1 = CompletableFuture.supplyAsync(new Supplier<String>() {
                @Override
                public String get() {
                    System.out.println("=============>异步线程开始...");
                    try {
                        TimeUnit.SECONDS.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println("=============>completableFuture1任务结束...");
                    System.out.println("=============>执行completableFuture1的线程为：" + Thread.currentThread().getName());
                    return "supplierResult";
                }
            });
            System.out.println("completableFuture1:" + completableFuture1);
    
            // 任务二：把第二个任务推进去，等待异步回调
            CompletableFuture<String> completableFuture2 = completableFuture1.thenApply(new Function<String, String>() {
                @Override
                public String apply(String s) {
                    try {
                        TimeUnit.SECONDS.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println("=============>completableFuture2任务结束 result=" + s);
                    System.out.println("=============>执行completableFuture2的线程为：" + Thread.currentThread().getName());
                    return s;
                }
            });
            System.out.println("completableFuture2:" + completableFuture2);
    
            // 任务三：把第三个任务推进去，等待异步回调
            CompletableFuture<String> completableFuture3 = completableFuture2.thenApply(new Function<String, String>() {
                @Override
                public String apply(String s) {
                    try {
                        TimeUnit.SECONDS.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println("=============>completableFuture3任务结束 result=" + s);
                    System.out.println("=============>执行completableFuture3的线程为：" + Thread.currentThread().getName());
                    return s;
                }
            });
            System.out.println("completableFuture3:" + completableFuture3);
    
            System.out.println("主线程结束");
            TimeUnit.SECONDS.sleep(40);
        }
    }
    ```

  - 分析主线程的主干：

    - CompletableFuture#supplyAsync(Supplier)：包装Supplier为AsyncSupply，调用executor.execute()，等待异步线程回调Supplier#get()
    - CompletableFuture#thenApply(Function)
    - CompletableFuture#thenApply(Function)

  - 主线程在执行“任务一”的CompletableFuture#supplyAsync(Supplier)时，将Supplier包装成AsyncSupply任务，并开启了异步线程，此后**异步线程**会阻塞在Supplier#get()：

    ![image-20210115212049077](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115212049077.png)

    ![image-20210115212056600](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115212056600.png)

  - Supplier#get()是异步线程开启后执行的第一站！

  - 与此同时，主线程继续执行后面的“任务二”、“任务三”，并且都会到达uniApply()，且都返回false，因为a.result==null。

  - 当主线程从任务二进来，调用thenApply()。最终会到达uniApply()，通过控制台的日志，我们发现a其实就是completableFuture1。因为uniApply()的上一步传入的this：

    ![image-20210115212144996](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115212144996.png)

  - 也就是说：

    - 主线程 ---> completableFuture1.thenApply(Function#apply) ---> !d.uniApply(this, f#apply, null) 
    - a.result就是completableFuture1.result，而completableFuture1的值来自Supplier#get()，此时确实还是null（异步线程阻塞设定的秒数秒后才会）。

  - 所以此时d.uniApply(this, f, null) 为false，那么!d.uniApply(this, f, null) 为true，就会进入if语句：

    ![image-20210115212301773](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115212301773.png)

  - 主要做了3件事：

    - 传入Executor e、新建的CompletableFuture d、当前completableFuture1、Function f，**构建UniApply**
    - **push**(uniApply)
    - uniApply.**tryFire**(SYNC)

  - 任务一做了两件事：

    - 开启异步线程
    - 等待回调

  - 由于要开启线程，自己也要作为任务被执行，所以Supplier#get()被包装成AsyncSupply，是一个Task。而后续的几个任务其实只做了一件事：等待回调。只要能通过实例执行方法即可，和任务一有所不同，所以只是被包装成UniApply对象。

  - push(uniApply)姑且认为是把任务二的Function#apply()包装后塞到**任务栈**中。

  - 但uniApply.tryFire(SYNC)是干嘛的呢？里面又调了一次uniApply()：

    ![image-20210115212533531](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115212533531.png)

  - SYNC=0，所以最终判断!d.uniApply(this, f, this) ==true，tryFire(SYNC)返回null，后面的d.postFire(a, mode)**此时并不会执行**，等后面异步线程复苏后，带着任务一的结果再次调用时，效果就截然不同了。

  - 总结一下，“任务二”、“任务三”操作都是一样的，都做了3件事：

    - 主线程调用CompletableFuture#thenApply(Function f)传入f，构建UniApply对象，包装Function#apply()
    - 把构建好的UniApply对象push到栈中
    - 返回CompletableFuture d

    ![image-20210115212633923](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115212633923.png)

    ![image-20210115212734838](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115212734838.png)

  - 等过了100秒，supplyAsync(Supplier)中的Supplier#get()返回结果后，异步线程继续往下走：

    - postComplete()也会走uniApply()，但这次已经有了异步结果result，所以流程不会被截断，最终会调用Function#apply(s)，而这个s是上一个函数的执行结果
    - 也就是说，新的CompletableFuture对象调用Function#apply()处理了上一个CompletableFuture产生的结果。

    ![image-20210115212939769](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115212939769.png)

- CompletableFuture与FutureTask线程数对比：

  - CompletableFuture和FutureTask耗费的线程数是一致的，但对于FutureTask来说，无论是轮询还是阻塞get，都会导致主线程无法继续其他任务，又或者主线程可以继续其他任务，但要时不时check FutureTask是否已经完成任务，比较糟心。而CompletableFuture则会根据我们编排的顺序逐个回调，是按照既定路线执行的。

    ![image-20210115213205392](http://iwehdio.gitee.io/img/my-img/21-01/image-20210115213205392.png)

  - 其实无论是哪种方式，异步线程其实都需要阻塞等待结果，期间不能处理其他任务。但对于FutureTask而言，在异步线程注定无法复用的前提下，如果想要获取最终结果，需要**主线程主动**查询或者额外开启一个线程查询，并且可能造成阻塞，而CompletableFuture的异步任务执行、任务结果获取都是**异步线程独立**完成。

  - 即，1个异步线程阻塞执行任务 + 回调异步结果 > 1个异步线程阻塞执行任务 + 1个线程阻塞查询任务。


