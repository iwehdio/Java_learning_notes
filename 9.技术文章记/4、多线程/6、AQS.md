学习自：

- [1.5w字，30图带你彻底掌握 AQS！](https://mp.weixin.qq.com/s/iNz6sTen2CSOdLE0j7qu9A)
- [我画了35张图就是为了让你深入 AQS](https://mp.weixin.qq.com/s/trsjgUFRrz40Simq2VKxTA)



## 1、AQS前置知识

- AQS（ AbstractQueuedSynchronizer ）是一个用来构建锁和同步器（所谓同步，是指线程之间的通信、协作）的框架，Lock 包中的各种锁（如常见的 ReentrantLock, ReadWriteLock）, concurrent 包中的各种同步器（如 CountDownLatch, Semaphore, CyclicBarrier）都是基于 AQS 来构建，所以理解 AQS 的实现原理至关重要。

- 信号量：

  - 信号量（Semaphore）是操作系统提供的一种进程间常见的通信方式，主要用来协调并发程序对共享资源的访问，操作系统可以保证对信号量操作的**原子性**。它是怎么实现的呢。
    - 信号量由一个共享整型变量 S 和两个原子操作 PV 组成，S 只能通过 P 和 V 操作来改变
    - P 操作：即请求资源，意味着 S 要减 1，如果 S <  0, 则表示没有资源了，此时线程要进入等待队列（同步队列）等待
    - V 操作:  即释放资源，意味着 S 要加 1， 如果 S 小于等于 0，说明等待队列里有线程，此时就需要唤醒线程。

  ![image-20210226192250238](http://iwehdio.gitee.io/img/my-img/21-02/image-20210226192250238.png)

  - 信号量机制的引入解决了进程同步和互斥问题，但信号量的大量同步操作分散在各个进程中不便于管理，还有可能导致系统死锁。
  - 基于信号量给编程带来的隐患，于是有了提出了对开发者更加友好的并发编程模型-**管程**。

- 管程：

  - 管程是一种在信号量机制上进行改进的并发编程模型，解决了信号量在临界区的 PV 操作上配对的麻烦，把配对的 PV 操作集中在一起而形成的并发编程方法理论，极大降低了使用和理解成本。
  - 管程由四部分组成：
    1. 管程内部的共享变量。
    2. 管程内部的条件变量。
    3. 管程内部并行执行的进程。
    4. 对于局部与管程内部的共享数据设置初始值的语句。
  - 管程就是一个对象监视器。任何线程想要访问该资源（共享变量），就要排队进入监控范围。进入之后，接受检查，不符合条件，则要继续等待，直到被通知，然后继续进入监视器。
  - 信号量和管程两者是等价的，信号量可以实现管程，管程也可以实现信号量，只是两者的表现形式不同而已，管程对开发者更加友好。

  ![image-20210226192911031](http://iwehdio.gitee.io/img/my-img/21-02/image-20210226192911031.png)

  - 管程为了解决信号量在临界区的 PV 操作上的配对的麻烦，把配对的 PV 操作集中在一起，并且加入了条件变量的概念，使得在多条件下线程间的同步实现变得更加简单。

  - 互斥问题：

    - 任何线程如果想要访问临界区，必须首先获取共享资源，入口一次只允许一个线程经过，在共享资源被占有的情况下，如果再有线程想占有共享资源，就需要到等待队列去等候，等到获取共享资源的线程释放资源后，等待队列中的线程就可以去竞争共享资源了，这样就解决了互斥问题。
    - 所以本质上管程是通过将共享资源及其对共享资源的操作（线程安全地获取和释放）封装起来来保证互斥性的。

  - 同步问题：

    - 同步是通过条件变量及其等待队列实现的。

  - 在 Java 里，锁大多是依赖于管程来实现的。synchronized 锁也是基于管程实现的，只不过它只有且只有一个条件变量（就是锁对象本身）。

    ![image-20210226195438140](http://iwehdio.gitee.io/img/my-img/21-02/image-20210226195438140.png)



## 2、AQS实现原理

- AQS 全称是 AbstractQueuedSynchronizer，是一个用来构建**锁**和**同步器**的框架，它维护了一个共享资源 state 和一个 FIFO 的等待队列（即管程的入口等待队列），底层利用了 CAS 机制来保证操作的原子性。

  ![image-20210226195720359](http://iwehdio.gitee.io/img/my-img/21-02/image-20210226195720359.png)

- 以实现独占锁为例（即当前资源只能被一个线程占有），其实现原理如下：

  - state 初始化 0，在多线程条件下，线程要执行临界区的代码，必须首先获取 state，某个线程获取成功之后， state 加 1，其他线程再获取的话由于共享资源已被占用，所以会到 FIFO 等待队列去等待。
  - 等占有 state 的线程执行完临界区的代码释放资源( state 减 1)后，会唤醒 FIFO 中的下一个等待线程（head 中的下一个结点）去获取 state。
  - state 由于是多线程共享变量，所以必须定义成 volatile，以保证 state 的可见性, 同时虽然 volatile 能保证可见性，但不能保证原子性，所以 AQS 提供了对 state 的原子操作方法，保证了线程安全。

- 以ReentrantLock锁为例，其默认为非公平锁。

  - FairSync 和 NonfairSync 是 ReentrantLock 实现的内部类，分别指公平和非公平模式，ReentrantLock 的加锁（lock），解锁（unlock）在内部具体都是调用的 FairSync，NonfairSync 的加锁和解锁方法。

- `Lock()`方法：

  ```java
  static final class NonfairSync extends Sync {
      
      final void lock() {
          if (compareAndSetState(0, 1))
              setExclusiveOwnerThread(Thread.currentThread());
          else
              acquire(1);
      }
  }
  ```

  1. 使用 CAS 来获取 state 资源，如果成功设置 1，代表 state 资源获取锁成功，此时记录下当前占用 state 的线程 setExclusiveOwnerThread(Thread.currentThread());
  2. 如果 CAS 设置 state 为 1 失败（代表获取锁失败），则执行 acquire(1) 方法.

- `acquire()`方法：

  ```java
  public final void acquire(int arg) {
      if (!tryAcquire(arg) &&
          acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
          selfInterrupt();
  }
  ```

  1. 首先 调用 tryAcquire 尝试着获取 state，如果成功，则跳过后面的步骤。
  2. 如果失败，则执行 acquireQueued 将线程加入 CLH 等待队列中。

- `tryAcquire()`方法：

  - 这个方法是 AQS 提供的一个模板方法，最终由其 AQS 具体的实现类（Sync）实现，由于执行的是非公平锁逻辑，执行的代码如下:

  ```java
  final boolean nonfairTryAcquire(int acquires) {
      final Thread current = Thread.currentThread();
      int c = getState();
  
      if (c == 0) {
          // 如果 c 等于0，表示此时资源是空闲的（即锁是释放的），再用 CAS 获取锁
          if (compareAndSetState(0, acquires)) {
              setExclusiveOwnerThread(current);
              return true;
          }
      }
      else if (current == getExclusiveOwnerThread()) {
          // 此条件表示之前已有线程获得锁，且此线程再一次获得了锁，获取资源次数再加 1，这也映证了 ReentrantLock 为可重入锁
          int nextc = c + acquires;
          if (nextc < 0) // overflow
              throw new Error("Maximum lock count exceeded");
          setState(nextc);
          return true;
      }
      return false;
  }
  ```

  1. state 为 0 时，代表锁已经被释放，可以去获取，于是使用 CAS 去重新获取锁资源，如果获取成功，则代表竞争锁成功，使用 setExclusiveOwnerThread(current) 记录下此时占有锁的线程，看到这里的 CAS，应该不难理解为啥当前实现是非公平锁了，因为队列中的线程与新线程都可以 CAS 获取锁啊，新来的线程不需要排队.
  2. 如果 state 不为 0，代表之前已有线程占有了锁，如果此时的线程依然是之前占有锁的线程（current == getExclusiveOwnerThread() 为 true），代表此线程再一次占有了锁（可重入锁），此时更新 state，记录下锁被占有的次数（锁的重入次数）,这里的 setState 方法不需要使用 CAS 更新，因为此时的锁就是当前线程占有的，其他线程没有机会进入这段代码执行。所以此时更新 state 是线程安全的。

  ![image-20210226202311746](http://iwehdio.gitee.io/img/my-img/21-02/image-20210226202311746.png)

  - 如果获得锁的线程再次申请锁，也就是重入，state会加一。

- `addWaiter()`方法：

  - 如果 tryAcquire(arg) 执行失败，代表获取锁失败，则执行 acquireQueued 方法，将线程加入 FIFO 等待队列。

  - 首先会调用 addWaiter(Node.EXCLUSIVE) 将包含有当前线程的 Node 节点入队, Node.EXCLUSIVE 代表此结点为独占模式。

    ```java
    private Node addWaiter(Node mode) {
        Node node = new Node(Thread.currentThread(), mode);
        Node pred = tail;
        // 如果尾结点不为空，则用 CAS 将获取锁失败的线程入队
        if (pred != null) {
            node.prev = pred;
            if (compareAndSetTail(pred, node)) {
                pred.next = node;
                return node;
            }
        }
        // 如果结点为空，执行 enq 方法
        enq(node);
        return node;
    }
    ```

  - 首先是获取 FIFO 队列的尾结点，如果尾结点存在，则采用 CAS 的方式将等待线程入队，如果尾结点为空则执行 enq 方法。

    ```java
    private Node enq(final Node node) {
        for (;;) {
            Node t = tail;
            if (t == null) {
                // 尾结点为空，说明 FIFO 队列未初始化，所以先初始化其头结点
                if (compareAndSetHead(new Node()))
                    tail = head;
            } else {
                // 尾结点不为空，则将等待线程入队
                node.prev = t;
                if (compareAndSetTail(t, node)) {
                    t.next = node;
                    return t;
                }
            }
        }
    }
    ```

    - 使用 CAS 创建 head 节点的时候只是简单调用了 new Node() 方法，并不像其他节点那样记录 thread，这是为啥?
      - 因为 head 结点为**虚结点**，它只代表当前有线程占用了 state，至于占用 state 的是哪个线程，其实是调用了上文的 setExclusiveOwnerThread(current) ，即记录在 exclusiveOwnerThread 属性里。

  - 执行完 addWaiter 后，线程入队成功，现在就要看最后一个最关键的方法 acquireQueued 了。

- 如果T1获取锁成功，T2、T3竞争失败入队后会怎么处理：

  ![image-20210226202450644](http://iwehdio.gitee.io/img/my-img/21-02/image-20210226202450644.png)

  -  T2，T3 入队后怎么处理，是马上阻塞吗，马上阻塞意味着线程从运行态转为阻塞态 ，涉及到用户态向内核态的切换，而且唤醒后也要从内核态转为用户态，开销相对比较大，所以 AQS 对这种入队的线程采用的方式是让它们自旋来竞争锁。
  - 如果当前锁是独占锁，如果锁一直被被 T1 占有， T2，T3 一直自旋没太大意义，反而会占用 CPU，影响性能，所以更合适的方式是它们自旋一两次竞争不到锁后识趣地阻塞以等待前置节点释放锁后再来唤醒它。
  - 另外如果锁在自旋过程中被中断了，或者自旋超时了，应该处于「取消」状态。

- 基于每个 Node 可能所处的状态，AQS 为其定义了一个变量 waitStatus，根据这个变量值对相应节点进行相关的操作：

  ```java
  static final class Node {
      static final Node SHARED = new Node();//标识等待节点处于共享模式
      static final Node EXCLUSIVE = null;//标识等待节点处于独占模式
  
      static final int CANCELLED = 1; //由于超时或中断，节点已被取消
      static final int SIGNAL = -1;  // 节点阻塞（park）必须在其前驱结点为 SIGNAL 的状态下才能进行，如果结点为 SIGNAL,则其释放锁或取消后，可以通过 unpark 唤醒下一个节点，
      static final int CONDITION = -2;//表示线程在等待条件变量（先获取锁，加入到条件等待队列，然后释放锁，等待条件变量满足条件；只有重新获取锁之后才能返回）
      static final int PROPAGATE = -3;//表示后续结点会传播唤醒的操作，共享模式下起作用
  
      //等待状态：对于condition节点，初始化为CONDITION；其它情况，默认为0，通过CAS操作原子更新
      volatile int waitStatus;
  }
  ```

- 如果当前节点的上一个节点不为 head，且它的状态为 SIGNAL，则结点进入阻塞状态。

  ```java
  final boolean acquireQueued(final Node node, int arg) {
      boolean failed = true;
      try {
          boolean interrupted = false;
          for (;;) {
              final Node p = node.predecessor();
              // 如果前一个节点是 head，则尝试自旋获取锁
              if (p == head && tryAcquire(arg)) {
                  //  将 head 结点指向当前节点，原 head 结点出队
                  setHead(node);
                  p.next = null; // help GC
                  failed = false;
                  return interrupted;
              }
              // 如果前一个节点不是 head 或者竞争锁失败，则进入阻塞状态
              if (shouldParkAfterFailedAcquire(p, node) &&
                  parkAndCheckInterrupt())
                  interrupted = true;
          }
      } finally {
          if (failed)
              // 如果线程自旋中因为异常等原因获取锁最终失败，则调用此方法
              cancelAcquire(node);
      }
  }
  ```

- 如果当前结点的前一个节点是 head 结点，且获取锁（tryAcquire）成功。

  ![image-20210226203555042](http://iwehdio.gitee.io/img/my-img/21-02/image-20210226203555042.png)

  - 主要的处理就是把 head 指向当前节点，并且让原 head 结点出队，这样由于原 head 不可达， 会被垃圾回收。

  -  setHead 的处理：

    ```java
    private void setHead(Node node) {
        head = node;
        node.thread = null;
        node.prev = null;
    }
    ```

    - 将 head 设置成当前结点后，要把节点的 thread, pre 设置成 null，因为head 是虚节点，不保存除 waitStatus（结点状态）的其他信息，所以这里把 thread ,pre 置为空，因为占有锁的线程由 exclusiveThread 记录了，如果 head 再记录 thread 不仅多此一举，反而在释放锁的时候要多操作一遍 head 的 thread 释放。

- 如果前一个节点不是 head 或者竞争锁失败，则首先调用  shouldParkAfterFailedAcquire 方法判断锁是否应该停止自旋进入阻塞状态：

  ```java
  private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
      int ws = pred.waitStatus;
          
      if (ws == Node.SIGNAL)
         // 1. 如果前置顶点的状态为 SIGNAL，表示当前节点可以阻塞了
          return true;
      if (ws > 0) {
          // 2. 移除取消状态的结点
          do {
              node.prev = pred = pred.prev;
          } while (pred.waitStatus > 0);
          pred.next = node;
      } else {
          // 3. 如果前置节点的 ws 不为 0，则其设置为 SIGNAL，
          compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
      }
      return false;
  }
  ```

  1. 首先要明白，根据之前 Node 类的注释，如果前驱节点为 SIGNAL，则当前节点可以进入阻塞状态。

     ![image-20210226204254183](http://iwehdio.gitee.io/img/my-img/21-02/image-20210226204254183.png)

  2. 如果前驱节点为取消状态，则前驱节点需要移除，并且是把所有当前节点之前所有 waitStatus 为取消状态的节点全部移除。

     ![image-20210226204308310](http://iwehdio.gitee.io/img/my-img/21-02/image-20210226204308310.png)

  3. 如果前驱节点小于等于 0，则需要首先将其前驱节点置为 SIGNAL,因为前文分析过，当前节点进入阻塞的一个条件是前驱节点必须为 SIGNAL，这样下一次自旋后发现前驱节点为 SIGNAL，就会返回 true（即步骤 1）

- shouldParkAfterFailedAcquire 返回 true 代表线程可以进入阻塞中断，那么下一步 parkAndCheckInterrupt 就该让线程阻塞了

  ```java
  private final boolean parkAndCheckInterrupt() {
      // 阻塞线程
      LockSupport.park(this);
      // 返回线程是否中断过，并且清除中断状态（在获得锁后会补一次中断）
      return Thread.interrupted();
  }
  ```

  这里的阻塞线程很容易理解，但为啥要判断线程是否中断过呢，因为如果线程在阻塞期间收到了中断，唤醒（转为运行态）获取锁后（acquireQueued 为 true）需要补一个中断：

  ```java
  public final void acquire(int arg) {
      if (!tryAcquire(arg) &&
          acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
          // 如果是因为中断唤醒的线程，获取锁后需要补一下中断
          selfInterrupt();
  }
  ```

-  Node 什么时候会被设置为取消状态：

  - acquireQueued中的cancelAcquire 方法，如果线程自旋中因为异常等原因获取锁最终失败，则会调用此方法。

  ```java
  private void cancelAcquire(Node node) {
      // 如果节点为空，直接返回
      if (node == null)
          return;
      // 由于线程要被取消了，所以将 thread 线程清掉
      node.thread = null;
  
      // 下面这步表示将 node 的 pre 指向之前第一个非取消状态的结点（即跳过所有取消状态的结点）,waitStatus > 0 表示当前结点状态为取消状态
      Node pred = node.prev;
      while (pred.waitStatus > 0)
          node.prev = pred = pred.prev;
  
      // 获取经过过滤后的 pre 的 next 结点，这一步主要用在后面的 CAS 设置 pre 的 next 节点上
      Node predNext = pred.next;
      // 将当前结点设置为取消状态
      node.waitStatus = Node.CANCELLED;
  
      // 如果当前取消结点为尾结点，使用 CAS 则将尾结点设置为其前驱节点，如果设置成功，则尾结点的 next 指针设置为空
      if (node == tail && compareAndSetTail(node, pred)) {
          compareAndSetNext(pred, predNext, null);
      } else {
      // 这一步看得有点绕，我们想想，如果当前节点取消了，那是不是要把当前节点的前驱节点指向当前节点的后继节点，但是我们之前也说了，要唤醒或阻塞结点，须在其前驱节点的状态为 SIGNAL 的条件才能操作，所以在设置 pre 的 next 节点时要保证 pre 结点的状态为 SIGNAL，想通了这一点相信你不难理解以下代码。
          int ws;
          if (pred != head &&
              ((ws = pred.waitStatus) == Node.SIGNAL ||
               (ws <= 0 && compareAndSetWaitStatus(pred, ws, Node.SIGNAL))) &&
              pred.thread != null) {
              Node next = node.next;
              if (next != null && next.waitStatus <= 0)
                  compareAndSetNext(pred, predNext, next);
          } else {
          // 如果 pre 为 head，或者  pre 的状态设置 SIGNAL 失败，则直接唤醒后继结点去竞争锁，之前我们说过， SIGNAL 的结点取消（或释放锁）后可以唤醒后继结点
              unparkSuccessor(node);
          }
          node.next = node; // help GC
      }
  }
  ```

- 不管是公平锁还是非公平锁，最终都是调的 AQS 的如下模板方法来释放锁

  ```java
  public final boolean release(int arg) {
      // 锁释放是否成功
      if (tryRelease(arg)) {
          Node h = head;
          if (h != null && h.waitStatus != 0)
              unparkSuccessor(h);
          return true;
      }
      return false;
  }
  ```

- tryRelease 方法定义在了 AQS 的子类 Sync 方法里

  ```java
  protected final boolean tryRelease(int releases) {
      int c = getState() - releases;
      // 只有持有锁的线程才能释放锁，所以如果当前锁不是持有锁的线程，则抛异常
      if (Thread.currentThread() != getExclusiveOwnerThread())
          throw new IllegalMonitorStateException();
      boolean free = false;
      // 说明线程持有的锁全部释放了，需要释放 exclusiveOwnerThread 的持有线程
      if (c == 0) {
          free = true;
          setExclusiveOwnerThread(null);
      }
      setState(c);
      return free;
  }
  ```

- 锁释放成功后该干嘛，显然是唤醒之后 head 之后节点，让它来竞争锁

  ```java
  public final boolean release(int arg) {
      // 锁释放是否成功
      if (tryRelease(arg)) {
          Node h = head;
          if (h != null && h.waitStatus != 0)
              // 锁释放成功后，唤醒 head 之后的节点，让它来竞争锁
              unparkSuccessor(h);
          return true;
      }
      return false;
  }
  ```

  这里释放锁的条件为啥是 h != null && h.waitStatus != 0 呢。

  1. 如果 h == null, 这有两种可能，一种是一个线程在竞争锁，现在它释放了，当然没有所谓的唤醒后继节点，一种是其他线程正在运行竞争锁，只是还未初始化头节点，既然其他线程正在运行，也就无需执行唤醒操作
  2. 如果 h != null 且 h.waitStatus == 0，说明 head 的后继节点正在自旋竞争锁，也就是说线程是运行状态的，无需唤醒。
  3. 如果 h != null 且 h.waitStatus < 0, 此时 waitStatus 值可能为 SIGNAL，或 PROPAGATE，这两种情况说明后继结点阻塞需要被唤醒

- 唤醒方法 unparkSuccessor：

  ```java
  private void unparkSuccessor(Node node) {
      // 获取 head 的 waitStatus（假设其为 SIGNAL）,并用 CAS 将其置为 0，为啥要做这一步呢，之前我们分析过多次，其实 waitStatus = SIGNAL（< -1）或 PROPAGATE（-·3） 只是一个标志，代表在此状态下，后继节点可以唤醒，既然正在唤醒后继节点，自然可以将其重置为 0，当然如果失败了也不影响其唤醒后继结点
      int ws = node.waitStatus;
      if (ws < 0)
          compareAndSetWaitStatus(node, ws, 0);
  
      // 以下操作为获取队列第一个非取消状态的结点，并将其唤醒
      Node s = node.next;
      // s 状态为非空，或者其为取消状态，说明 s 是无效节点，此时需要执行 if 里的逻辑
      if (s == null || s.waitStatus > 0) {
          s = null;
          // 以下操作为从尾向前获取最后一个非取消状态的结点
          for (Node t = tail; t != null && t != node; t = t.prev)
              if (t.waitStatus <= 0)
                  s = t;
      }
      if (s != null)
          LockSupport.unpark(s.thread);
  }
  ```

  - 这里的寻找队列的第一个非取消状态的节点为啥要从后往前找呢，因为节点入队并不是原子操作.

  ![image-20210226212116693](http://iwehdio.gitee.io/img/my-img/21-02/image-20210226212116693.png)

  - 线程自旋时时是先执行 node.pre = pred, 然后再执行 pred.next = node，如果 unparkSuccessor 刚好在这两者之间执行，此时是找不到  head 的后继节点的.

  ![image-20210226212057847](http://iwehdio.gitee.io/img/my-img/21-02/image-20210226212057847.png)