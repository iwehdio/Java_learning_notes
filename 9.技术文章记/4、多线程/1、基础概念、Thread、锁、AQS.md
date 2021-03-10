学习自：

- [Thread源码剖析](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247484190&idx=1&sn=ab7301e393aa7762be9ef80d30c5fb7a&chksm=ebd7421fdca0cb09f4a880064a8610416df414ea25284e6d5142ea659e4e7e669632cfed4050&scene=21###wechat_redirect)

- [多线程初级（上）](https://zhuanlan.zhihu.com/p/56518031)

- [多线程初级（中）](https://zhuanlan.zhihu.com/p/57482990)

- [多线程基础必要知识点！](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247484194&idx=1&sn=ed1241fcba5d3e85b6d900d8667f04f6&chksm=ebd74223dca0cb35fe16a267c88ac9e5159825b27c278fb165a8c50d681e1340b73cfd69ae0d&scene=21###wechat_redirect)

- [Java锁机制了解一下](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247484198&idx=1&sn=4d8e372165bb49987a6243f17153a9b4&chksm=ebd74227dca0cb31311886f835092c9360d08a9f0a249ece34d4b1e49a31c9ec773fa66c8acc&scene=21###wechat_redirect)

- [AQS简简单单过一遍](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247484202&idx=1&sn=dbf9e94d2486ee0baa43e043a2363231&chksm=ebd7422bdca0cb3dc0451e09d139b72558b1cfa3593a6bcc1716ae9d1bd443804d194a303985&scene=21###wechat_redirect)

- [Java并发编程札记-(四)JUC锁-03AQS](https://blog.csdn.net/panweiwei1994/article/details/78769703)



## 1、Thread

- 线程命名：

  - 如果不设置，是与线程初始化的数量有关的。

  ![image-20210112220402926](http://iwehdio.gitee.io/img/my-img/21-01/image-20210112220402926.png)

- 守护线程：

  - 守护线程是为其他线程服务的，比如垃圾回收线程就是守护线程。
  - 守护线程有一个特点：
    - 当别的用户线程执行完了，虚拟机就会退出，守护线程也就会被停止掉了。
    - 也就是说：守护线程作为一个服务线程，没有服务对象就没有必要继续运行了。
  - 使用线程的时候要注意的地方：

    - 在线程启动前设置为守护线程，方法是setDaemon(boolean on)。
    - 使用守护线程不要访问共享资源(数据库、文件等)，因为它可能会在任何时候就挂掉了。
    - 守护线程中产生的新线程也是守护线程。

- 优先级线程：

  - 线程优先级高仅仅表示线程获取的CPU时间片的几率高，但这不是一个确定的因素。

  - 程的优先级是高度依赖于操作系统的，Windows和Linux就有所区别(Linux下优先级可能就被忽略了)

  - Java提供的优先级默认是5，最低是1，最高是10：

    ![image-20210112220635661](http://iwehdio.gitee.io/img/my-img/21-01/image-20210112220635661.png)

  - 实现优先级是使用`setPriority0`，是一个本地(navite)的方法。

- 线程状态：

  ![image-20210112221931840](http://iwehdio.gitee.io/img/my-img/21-01/image-20210112221931840.png)

  - 调用sleep方法会进入计时等待状态，等时间到了，进入的是就绪状态而并非是运行状态。
  - 调用yield方法会尝试先让别的线程执行，但是不确保真正让出。
  - 调用join方法，会等待该线程执行完毕后才执行别的线程。（使用wait和notifyAll实现）

  ![image-20210112220901240](http://iwehdio.gitee.io/img/my-img/21-01/image-20210112220901240.png)

- 线程中断：

  - 线程中断在之前的版本有stop方法，但是被设置过时了。Stop方法太暴力了，不安全，现在已经没有强制线程终止的方法了。
  - 一般使用的是interrupt()来请求终止线程：
    - interrupt不会真正停止一个线程，它仅仅是给这个线程发了一个信号告诉它，它应该要结束了。
    - 也就是说：Java设计者实际上是想线程自己来终止，通过上面的信号，就可以判断处理什么业务了。
    - 具体到底中断还是继续运行，应该由被通知的线程自己处理。
  - 对线程对象调用`interrupt()`，线程内部可以通过`isInterrupted()`检测是否被通知终止。
  - 阻塞线程调用了interrupt()方法，那么会抛出异常，设置标志位为false，同时该线程会退出阻塞。

- 线程的创建方式：继承Thread和实现Runnable。

  - 继承Thread：本质是覆盖了Thread中的run方法。

    ![image-20210114202638780](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114202638780.png)

  - 实现Runnable：本质是Thread中的run方法调用了传入的Runnable实现类的run方法。

    ![image-20210114202717214](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114202717214.png)

  - Runnable里面仅定义了一个抽象方法run()![image-20210114202534659](http://iwehdio.gitee.io/img/my-img/21-01/image-20210114202534659.png)

  - 搞出Runnable接口，目的有两个：

    1. 限定Thread构造方法的形参类型。
    2. 将run()向上抽取，做成抽象方法，让实现类去重写。

  - 为什么要传Runnable实现类对象？因为对于方式2而言，要执行的代码并不在Thread线程本身，而是在Runnable的实现类中，所以**必须传入一个对象告诉线程去哪执行。**而Thread的有参构造方法的形参类型是Runnable：

    ```java
    // 构造方法
     public Thread(Runnable target) {
         init(null, target, "Thread-" + nextThreadNum(), 0);
     }
    ```

  - 一个线程执行，总是从start()开始，因为它才是开启线程的钥匙。线程开始后会自动调用Thread的run()。

  - run()的本质，只是为了“包裹”需要线程执行的代码块。

  - Thread类（及其子类）是线程运行的入口。所要运行的，也就是调用start对象中的run方法。

  - 实现Runnable的好处恰恰在于“执行者”与“被执行者”被分离了，实现了解耦。


## 2、多线程中遇到的问题

- 对象的发布和逸出：

  - 发布(publish) 使对象能够在当前作用域之外的代码中使用。
  - 逸出(escape) 当某个不应该发布的对象被发布了。

- 常见逸出的有下面几种方式：

  - 静态域逸出；
  - public修饰的get方法；
  - 方法参数传递;
  - 隐式的this。

  ![image-20210113192441081](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113192441081.png)

- 安全发布对象的常见方式：

  - 在静态域中直接初始化 ： public static Person = new Person();

    - 静态初始化由JVM在类的初始化阶段就执行了，JVM内部存在着同步机制，致使这种方式我们可以安全发布对象。
  - 对应的引用保存到volatile或者AtomicReferance引用中：保证了该对象的引用的可见性和原子性。
  - 由final修饰：该对象是不可变的，那么线程就一定是安全的，所以是安全发布。
  - 由锁来保护：发布和使用的时候都需要加锁，这样才保证能够该对象不会逸出。

- 解决线程安全问题的常见办法：

  - 无状态(没有共享变量)。
  - 使用final使该引用变量不可变(如果该对象引用也引用了其他的对象，那么无论是发布或者使用时都需要加锁)。
  - 加锁(内置锁，显示Lock锁)、
  - 使用JDK为我们提供的类来实现线程安全
    - 原子性（比如上面的count++操作，可以使用AtomicLong来实现原子性，那么在增加的时候就不会出错了！)
    - 容器(ConcurrentHashMap等等…)

- 原子性：

  - 原子性就是执行某一个操作是不可分割的。

    - count++，先读取，后自增，再赋值。如果该操作是原子性的，那么就可以说线程安全了。

       - JDK中有atomic包提供给我们实现原子性操作。

- 可见性：

  - volatile是一种轻量级的同步机制。volatile仅仅用来保证该变量对所有线程的可见性，但不保证原子性。
    - 保证该变量对所有线程的可见性。在多线程的环境下：当这个变量修改时，所有的线程都会知道该变量被修改了，也就是所谓的“可见性”
    - 不保证原子性。修改变量(赋值)实质上是在JVM中分了好几步，而在这几步内(从装载变量到修改)，它是不安全的。
  - 使用了volatile修饰的变量保证了三点：

    - 一旦完成写入，任何访问这个字段的线程将会得到最新的值。
    - 在写入前，会保证所有之前发生的事已经发生，并且任何更新过的数据值也是可见的，因为内存屏障会把之前的写入值都刷新到缓存。
    - volatile可以防止重排序(重排序指的就是：程序执行的时候，CPU、编译器可能会对执行顺序做一些调整，导致执行的顺序并不是从上往下的。从而出现了一些意想不到的效果)。而如果声明了volatile，那么CPU、编译器就会知道这个变量是共享的，不会被缓存在寄存器或者其他不可见的地方。
  - 一般来说，volatile大多用于标志位上(判断操作),满足下面的条件才应该使用volatile修饰变量：

    - 修改变量时不依赖变量的当前值(因为volatile是不保证原子性的)。
    - 该变量不会纳入到不变性条件中(该变量是可变的)。
    - 在访问变量的时候不需要加锁(加锁就没必要使用volatile这种轻量级同步机制了)。

- 线程封闭：

  - 在多线程的环境下，只要我们不使用成员变量(不共享数据),那么就不会出现线程安全的问题了。
  - 在方法上操作，只要我们保证不要在栈(方法)上发布对象(每个变量的作用域仅仅停留在当前的方法上)，那么我们的线程就是安全的。

- 不变性：

  - 不可变对象一定线程安全的。
  - final仅仅是不能修改该变量的引用，但是引用里边的数据是可以改的。不可变的对象引用在使用的时候还是需要加锁的。
  - 要想将对象设计成不可变对象，那么要满足下面三个条件：
    - 对象创建后状态就不能修改。
    - 对象所有的域都是final修饰的。
    - 对象是正确创建的(没有this引用逸出)。
  
- 线程安全问题：

  - 所谓线程安全就是多个线程去执行某类，这个类始终能表现出正确的行为，那么这个类就是线程安全的。
  - 比如有一个count变量，在service方法不断的累加这个count变量。如果多个线程累加这个变量，每次得到的值是不变的，就是线程安全，否则就是线程不安全。

- 如何解决线程安全问题：

  - 能不能保证操作的原子性，考虑atomic包下的类够不够我们使用。
  - 能不能保证操作的可见性，考虑`volatile`关键字够不够我们使用
  - 如果涉及到对线程的控制（比如一次能使用多少个线程，当前线程触发的条件是否依赖其他线程的结果），考虑CountDownLatch/Semaphore等等。
  - 如果是集合，考虑`java.util.concurrent`包下的集合类。
  - 如果synchronized无法满足，考虑lock包下的类。

## 3、锁

- 多线程对共享变量做++操作，会产生线程安全问题。出现问题的原因在于cnt++并不是原子性操作，实际上分三步：

  1. 各个线程从主存拷贝变量
  2. 在自己的工作内存进行+1操作
  3. 把结果回写到主存
  
- 如何解决？回想一下三个条件：

  - 多线程环境（这个是前提，无法改变，没有多线程当然没有安全问题）
  - 有共享数据（通常无法改变，特定情境下必须要操作共享数据）
  - 非原子性操作（可以改变！）

- 

- synchronized是Java的一个关键字，它能够将代码块(方法)锁起来：

  - synchronized是一种互斥锁：一次只能允许一个线程进入被锁住的代码块。
  - 用synchronized修饰方法后，就相当于将方法内的多个语句捆绑在一起，要么全部执行，要么尚未开始，不会出现“执行到一半被挂起”的情况，也就避免了线程安全问题的发生。
  - synchronized是一种内置锁/监视器锁。Java中每个对象都有一个内置锁(监视器,也可以理解成锁标记)，而synchronized就是使用对象的内置锁(监视器)来将代码块(方法)锁定的。
  - synchronized保证了线程的原子性。(被保护的代码块是一次被执行的，没有任何线程会同时访问)。
  - synchronized还保证了可见性。(当执行完synchronized之后，修改后的变量对其他的线程是可见的)。

- “锁”本身是个对象，且理论上可以是任意对象。synchronized这个关键字不是“锁”，硬要说的话，加synchronized仅仅是相当于“加锁”这个操作，真正的锁是“某一个对象”。

  - 由于理论上锁可以是任意对象，所以锁的wait/notify/notifyAll等方法就被定义在Object类中，让所有类去继承。
  - wait/notify/notifyAll这些方法都是锁对象的方法，线程之所以会产生等待、唤醒等一系列状态，都是去读取锁对象时被指定的。

  ![image-20210120190705808](G:\Part One+\Part9 code notes\9.技术文章记\4、多线程\img\1、基础概念、Thread、锁、AQS\image-20210120190705808.png)

  ![image-20210120190716041](G:\Part One+\Part9 code notes\9.技术文章记\4、多线程\img\1、基础概念、Thread、锁、AQS\image-20210120190716041.png)

- synchronized的原理（JVM层面）：

  - 同步代码块：monitorenter和monitorexit指令实现的。
  - 同步方法：方法修饰符上的ACC_SYNCHRONIZED实现。
  - synchronized底层是是通过monitor对象，对象有自己的对象头，存储了很多信息，其中一个信息标示是被哪个线程持有。

- 类锁和对象锁：

  - synchronized修饰静态方法获取的是类锁(类的字节码文件对象)，synchronized修饰普通方法或代码块获取的是对象锁。
  - 获取了类锁的线程和获取了对象锁的线程是不冲突的。

- 重入锁：

  ```java
  public class Widget {
  
      // 锁住了
      public synchronized void doSomething() {
          ...
      }
  }
  
  public class LoggingWidget extends Widget {
  
      // 锁住了
      public synchronized void doSomething() {
          System.out.println(toString() + ": calling doSomething");
          super.doSomething();
      }
  }
  ```

  - 当线程A进入到LoggingWidget的doSomething()方法时，此时拿到了LoggingWidget实例对象的锁。
  - 随后在方法上又调用了父类Widget的doSomething()方法，它又是被synchronized修饰。
  - 那现在我们LoggingWidget实例对象的锁还没有释放，进入父类Widget的doSomething()方法还需要一把锁吗？
  - 不需要的！因为锁的持有者是“线程”，而不是“调用”。线程A已经是有了LoggingWidget实例对象的锁了，当再需要的时候可以继续“开锁”进去的！
  - 这就是内置锁的可重入性。

- 释放锁的时机：

  - 当方法(代码块)执行完毕后会自动释放锁，不需要做任何的操作。
  - 当一个线程执行的代码出现异常时，其所持有的锁会自动释放。

- Lock锁：

  - Lock方式来获取锁支持中断、超时不获取、是非阻塞的。
  - 提高了语义化，哪里加锁，哪里解锁都得写出来。
  - Lock显式锁可以给我们带来很好的灵活性，但同时我们必须手动释放锁。
  - 支持Condition条件对象。
  - 允许多个读线程同时访问共享资源。

- synchronized锁和Lock锁使用哪个：

  - Lock锁在刚出来的时候很多性能方面都比Synchronized锁要好，但是从JDK1.6开始Synchronized锁就做了各种的优化。
  - 优化操作：适应自旋锁，锁消除，锁粗化，轻量级锁，偏向锁。
  - 到现在Lock锁和Synchronized锁的性能其实差别不是很大！而Synchronized锁用起来又特别简单。Lock锁还得顾忌到它的特性，要手动释放锁才行。
  - 绝大部分时候还是会使用Synchronized锁，用到了Lock锁提及的特性，带来的灵活性才会考虑使用Lock显式锁。

- 公平锁：

  - 公平锁：线程将按照它们发出请求的顺序来获取锁。
  - 非公平锁：线程发出请求的时可以“插队”获取锁。
  - Lock和synchronize都是默认使用非公平锁的。如果不是必要的情况下，不要使用公平锁。公平锁会来带一些性能的消耗的。

## 4、AQS

- AQS是什么：

  - java.util.concurrnt包中lock包下有一个抽象类：AbstractQueuedSynchronizer简称为AQS。
  - 为实现阻塞锁和相关同步器提供一个框架，他是依赖于先进先出的等待队列。
  - 依靠单个原子int值来表示状态，该状态用于表示锁是获取中还是释放。通过给定的方法改变状态的值。
  - 定义了内部类ConditionObject
  - 拥有两种线程模式
    - 独占模式
    - 共享模式
  - 在LOCK包中的相关锁(常用的有ReentrantLock、 ReadWriteLock)都是基于AQS来构建。
  - 一般我们叫AQS为同步器。

- 同步状态：

  - STATE是共享资源，描述当前有多少线程获取了锁，对于互斥锁来说state<=1。
  - 使用volatile修饰的int类型实现线程可见性。

  ![image-20210113194958643](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113194958643.png)

  - 通过使用getState()、setState(int) 和/或 compareAndSetState(int, int) 方法可以检查和/或修改同步状态。
  - 修改state状态值时使用CAS算法来实现（乐观锁）。

  ![image-20210113195332145](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113195332145.png)

- AQS支持两种资源访问方式，独占模式（Exclusive）和共享模式（Share）。

  - AQS的子类只支持其中一种模式，但也有例外，如ReadWriteLock支持两种模式。只支持独占模式或者只支持共享模式的子类不必定义支持未使用模式的方法。
  - 为了将此类用作同步器的基础，子类需要适当地重新定义以下方法：
    - tryAcquire(int)。在独占模式下获取对象状态。
    - tryRelease(int)。设置状态来反映独占模式下的一个释放。
    - tryAcquireShared(int)。在共享模式下获取对象状态。
    - tryReleaseShared(int)。设置状态来反映共享模式下的一个释放。
    - isHeldExclusively()。如果对于当前线程，同步是以独占方式进行的，则返回 true。
  - 在AQS中，这几个方法都抛出UnsupportedOperationException。
  - AQS的子类（锁或者同步器）需要进行重写这几个方法实现共享资源STATE的获取与释放方式。
    - 以ReentrantLock为例，ReentrantLock是独占锁，只有独占模式（Exclusive）一种资源访问方式，所以在NonfairSync和FairSync中只有tryAcquire(int)和tryRelease(int)方法。
    - state初始化为0，表示未锁定状态。线程thread调用reentrantLock.lock()时，会调用tryAcquire(1)独占该锁并将state+1。此后，其他线程再tryAcquire()时就会失败，直到线程thread调用unlock()时，会调用tryRelease将state-1，这时state=0，这时其它线程才有机会获取该锁。
    - 当然，ReentrantLock是可重入的锁，在释放锁之前，线程thread自己是可以多次调用lock()重复获取此锁的，state会累加。释放锁时要重复调用unlock()释放锁，获取多少次就要释放多么次，这样才能保证state是能回到零态的。

- 先进先出队列：

  - 这个队列被称为：CLH队列(三个名字组成)，是一个双向队列。
  - 通过自旋锁和CAS保证节点插入和移除的原子性。
  - 把队头head和队尾tail设置为了volatile,这两个节点的修改将会被其他线程看到,事实上,我们也主要是通过修改这两个节点来完成入队和出队

  ![image-20210113195509154](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113195509154.png)

  - 队列中节点的状态：
    - SIGNAL:     节点的继任节点是（或者将要成为）BLOCKED状态（例如通过LockSupport.park()操作），因此一个节点一旦被释放（解锁）或者取消就需要唤醒（LockSupport.unpack()）它的继任节点。
    -  CANCELLED:  节点操作因为超时或者对应的线程被interrupt。节点不应该留在此状态，一旦达到此状态将从CHL队列中踢出。
    -  CONDITION:  表明节点对应的线程因为不满足一个条件（Condition）而被阻塞。
    - PROPAGATE:  一个releaseShared 应该扩散到其他节点。设置在doReleaseShared方法（只是头结点）中为了确保扩散继续，即使其他操作已经开始执行。  
    - 0: 正常状态。
  - 状态为非负值意味着节点不需要被唤醒。初始化为0，可以使用CAS修改它。

  ![image-20210113201233718](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113201233718.png)

- acquire方法：以独占模式获取对象，忽略中断。

  - 该方法用到了模板设计模式，由子类实现的。
  - 通过至少调用一次 tryAcquire(int) 来实现此方法，并在成功时返回。 
  - 否则在成功之前，一直调用 tryAcquire(int) 将线程加入队列，线程可能重复被阻塞或不被阻塞。

  ![image-20210113195724469](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113195724469.png)

  - 过程：
    1. acquire(int)尝试获取资源，如果成功，则acquire的任务就完成了。
    2. 如果获取失败，将线程标记为独占模式，插入等待队列。通过acquireQueued()自旋尝试获取资源。
    3. 如果前置节点是头结点，继续尝试获取资源，，如果成功则返回中断位结束acquire(int)。
    4. 否则，调用shouldParkAfterFailedAcquire(Node, Node)检测当前节点是否应该park()。
       - 如果前置节点的状态是Node.SIGNAL，说明前置节点释放资源后会通知自己。此时当前节点就可以park()，返回true。
       - 如果前置节点的状态是Node.CANCELLED，说明前置节点已经放弃获取资源了，此时一直往前找，直到找到最近的一个处于正常等待状态的节点，并排在它的后边。返回false，acquireQueued()自旋，回到第三步。
       - 如果前置节点处于其他状态，利用CAS将前置节点的状态置为SIGNAL，让它释放资源后通知自己（有可能失败，如果前置节点刚刚释放资源，状态就不是SIGNAL了，这时就会失败）。返回false，acquireQueued()自旋，回到第三步。
    5. 如果shouldParkAfterFailedAcquire(Node, Node)返回true，调用parkAndCheckInterrupt()中断当前节点中的线程。acquireQueued(Node,int)返回true。
    6. 回到acquire(int)，acquireQueued(Node,int)如果返回true，调用selfInterrupt()，中断当前线程。

- tryAcquire(int)方法：

  - AQS的子类（锁或者同步器）需要进行重写这个方法实现共享资源STATE的获取。以公平的ReentrantLock为例。

  ![image-20210113202112706](http://iwehdio.gitee.io/img/my-img/21-01/image-20210113202112706.png)

  - 从代码中可以看出，如果共享资源state为0，说明该资源还没有被其他线程占用，此时tryAcquire(int)获取资源成功。如果state不为0，且资源的占用者就是当前线程，此时可以重复获取资源（印证了ReentrantLock是可重入的锁），tryAcquire(int)获取资源成功。

- addWaiter(Node)方法：此方法用于将当前线程加入到等待队列的队尾，并返回当前线程所在的节点。

  ```java
  private Node addWaiter(Node mode) {
      //将当前线程封装成节点，并指定资源访问模式，如独占模式（Exclusive）和共享模式（Share）
      Node node = new Node(Thread.currentThread(), mode);
      // 尝试快速方式入队
      Node pred = tail;
      if (pred != null) {
          node.prev = pred;
          //可以看出，设置尾节点是通过CAS进行的
          if (compareAndSetTail(pred, node)) {
              pred.next = node;
              return node;
          }
      }
      //如果快速方式入队失败，则通过enq(Node)入队
      enq(node);
      //返回当前线程所在的节点
      return node;
  }
  private Node enq(final Node node) {
          for (;;) {
              Node t = tail;
              //此时可能有其他线程插入,所以重新判断tail是否为空
              if (t == null) { // Must initialize
                  if (compareAndSetHead(new Node()))
                      tail = head;
              } else {
                  node.prev = t;
                  if (compareAndSetTail(t, node)) {
                      t.next = node;
                      return t;
                  }
              }
          }
      }
  }
  ```

- acquireQueued(Node, int)：

  - 在把node插入队列末尾后,它并不立即挂起该节点中线程,因为在插入它的过程中,前面的线程可能已经执行完成。
  - 所以它会先进行自旋操作acquireQueued(node, arg),尝试让该线程重新获取锁!当条件满足获取到了锁则可以从自旋过程中退出，否则继续。

  ```java
  final boolean acquireQueued(final Node node, int arg) {
      boolean failed = true;
      try {
          boolean interrupted = false;
          //自旋尝试获取资源
          for (;;) {
              final Node p = node.predecessor();
              //如果节点的前驱是队列的头结点，这时可以调用tryAcquire(arg)尝试获取资源，如果成功则返回中断位结束acquire(int)。
              if (p == head && tryAcquire(arg)) {
                  setHead(node);
                  p.next = null; // help GC
                  failed = false;
                  return interrupted;
              }
              //调用shouldParkAfterFailedAcquire(Node, Node)检测当前节点是否应该park()
              //如果shouldParkAfterFailedAcquire(Node, Node)返回true，调用parkAndCheckInterrupt()中断当前节点中的线程
              if (shouldParkAfterFailedAcquire(p, node) &&
                  parkAndCheckInterrupt())
                  interrupted = true;
          }
      } finally {
          if (failed)
              cancelAcquire(node);
      }
  }
  ```

- shouldParkAfterFailedAcquire(Node, Node)方法：如果没获取到锁,则判断是否应该挂起,而这个判断则得通过它的前驱节点的waitStatus来确定。

  ```java
  private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
      int ws = pred.waitStatus;
      if (ws == Node.SIGNAL)
          /*
           * 如果前置节点的状态是Node.SIGNAL，说明前置节点释放资源后会通知自己。
           * 此时当前节点就可以park()，返回true。
           */
          return true;
      if (ws > 0) {
          /*
           * 如果前置节点的状态是Node.CANCELLED，说明前置节点已经放弃获取资源了，
           * 此时一直往前找，直到找到最近的一个处于正常等待状态的节点，
           * 并排在它的后边，返回false。
           */
          do {
              node.prev = pred = pred.prev;
          } while (pred.waitStatus > 0);
          pred.next = node;
      } else {
          /*
           * 如果前置节点处于其他状态，利用CAS将前置节点的状态置为SIGNAL，
           * 让它释放资源后通知自己
           * （有可能失败，如果前置节点刚刚释放资源，状态就不是SIGNAL了，这时就会失败）。
           * 返回false
           */
          compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
      }
      return false;
  }
  ```

- parkAndCheckInterrupt()方法：如果shouldParkAfterFailedAcquire(Node, Node)返回true，调用parkAndCheckInterrupt()中断当前节点中的线程。

  ```java
  private final boolean parkAndCheckInterrupt() {
      LockSupport.park(this);
      return Thread.interrupted();
  }
  ```

- release(int)方法： 以独占模式释放对象.

  ```java
  /**
   * 以独占模式释放对象。
   * 如果 tryRelease(int) 返回 true，则通过消除一个或多个线程的阻塞来实现此方法。
   * 可以使用此方法来实现 Lock.unlock() 方法
   */
  public final boolean release(int arg) {
      if (tryRelease(arg)) {
          Node h = head;
          if (h != null && h.waitStatus != 0)
              unparkSuccessor(h);
          return true;
      }
      return false;
  }
  ```

  - release(int)会释放指定量的资源，如果资源被彻底释放了，即state=0，它会唤醒等待队列里的其他线程来获取资源。
  - release(int)的执行流程：
    1. tryRelease(int)尝试直接去释放资源.如果将资源完全释放了，调用unparkSuccessor(Node)唤醒头结点的后继节点。返回true。
    2. 如果tryRelease(int)没有将资源完全释放，返回false。

- tryRelease(int)方法：

  - AQS的子类（锁或者同步器）需要进行重写这个方法实现共享资源STATE的释放。以公平的ReentrantLock为例。

  ```java
  protected final boolean tryRelease(int releases) {
      //释放量为releases的资源
      int c = getState() - releases;
      //如果当前线程不是独占模式的，抛出异常
      if (Thread.currentThread() != getExclusiveOwnerThread())
          throw new IllegalMonitorStateException();
      boolean free = false;
      //如果当前线程占有的资源已经全部被释放了，返回true
      if (c == 0) {
          free = true;
          setExclusiveOwnerThread(null);
      }
      setState(c);
      return free;
  }
  ```

- unparkSuccessor(Node)方法，唤醒后续的节点。

  ```java
  private void unparkSuccessor(Node node) {
      /*
       * If status is negative (i.e., possibly needing signal) try
       * to clear in anticipation of signalling.  It is OK if this
       * fails or if status is changed by waiting thread.
       */
      int ws = node.waitStatus;
      //如果节点状态小于0，将节点状态置为0，即正常状态
      if (ws < 0)
          compareAndSetWaitStatus(node, ws, 0);
  
      //找到符合条件的需要唤醒的节点
      Node s = node.next;
      if (s == null || s.waitStatus > 0) {
          s = null;
          for (Node t = tail; t != null && t != node; t = t.prev)
              if (t.waitStatus <= 0)
                  s = t;
      }
      //唤醒节点
      if (s != null)
          LockSupport.unpark(s.thread);
  }
  ```

- acquireShared(int)：以共享模式获取对象，忽略中断。

  - acquireShared(int)的执行流程：
    1. tryAcquireShared(int)尝试直接去获取资源，如果成功，acquireShared(int)就结束了。
    2. 如果步骤1失败了，调用doAcquireShared(Node)将线程加入等待队列，直到获取到资源为止。

  ```java
  public final void acquireShared(int arg) {
      if (tryAcquireShared(arg) < 0)
          doAcquireShared(arg);
  }
  ```

- tryAcquireShared(int)：需要进行重写这个方法实现共享资源STATE的获取。以ReentrantReadWriteLock为例。

  ```java
  protected final int tryAcquireShared(int unused) {
      Thread current = Thread.currentThread();
      int c = getState();
      //如果独占锁（写锁）已经被其他线程获取，tryAcquireShared(int)失败
      if (exclusiveCount(c) != 0 &&
          getExclusiveOwnerThread() != current)
          return -1;
      int r = sharedCount(c);
      //如果线程不应该阻塞，且大小没有饱和，且CAS成功，获取资源成功，返回1
      if (!readerShouldBlock() &&
          r < MAX_COUNT &&
          compareAndSetState(c, c + SHARED_UNIT)) {
          if (r == 0) {
              firstReader = current;
              firstReaderHoldCount = 1;
          } else if (firstReader == current) {
              firstReaderHoldCount++;
          } else {
              HoldCounter rh = cachedHoldCounter;
              if (rh == null || rh.tid != getThreadId(current))
                  cachedHoldCounter = rh = readHolds.get();
              else if (rh.count == 0)
                  readHolds.set(rh);
              rh.count++;
          }
          return 1;
      }
      //如果线程应该阻塞，或大小饱和，或CAS失败，自旋获取资源，如果成功返回1，否则返回-1
      return fullTryAcquireShared(current);
  }
  ```

- doAcquireShared(arg)方法：在等待队列中继续尝试获取资源。

  ```java
  private void doAcquireShared(int arg) {
      //将线程加入等待队列，并指定资源访问方式为共享方式
      final Node node = addWaiter(Node.SHARED);
      boolean failed = true;
      try {
          boolean interrupted = false;
          //自旋尝试获取资源
          for (;;) {
              final Node p = node.predecessor();
              if (p == head) {
                  int r = tryAcquireShared(arg);
                  if (r >= 0) {
                      //设置头结点，且如果还有剩余资源，唤醒后继节点的线程去获取资源
                      setHeadAndPropagate(node, r);
                      p.next = null; // help GC
                      if (interrupted)
                          selfInterrupt();
                      failed = false;
                      return;
                  }
              }
              if (shouldParkAfterFailedAcquire(p, node) &&
                  parkAndCheckInterrupt())
                  interrupted = true;
          }
      } finally {
          if (failed)
              cancelAcquire(node);
      }
  }
  ```

- releaseShared(int)方法：以共享模式释放对象。releaseShared(int)与release(int)大体上比较像，区别在于releaseShared(int)在释放一部分资源后就可以通知其他线程获取资源，而release(int)要等资源释放完后才可以。

  ```java
  public final boolean releaseShared(int arg) {
      if (tryReleaseShared(arg)) {
          doReleaseShared();
          return true;
      }
      return false;
  }
  ```

- 在AQS的设计中，在父类AQS中实现了对等待队列的默认实现，子类中几乎不用修改该部分功能。而state在子类中根据需要被赋予了不同的意义，子类通过对state的不同操作来提供不同的同步器功能，进而对封装的工具类提供不同的功能。

- 流程总结：

  ![image-20210118185234404](http://iwehdio.gitee.io/img/my-img/21-01/image-20210118185234404.png)


