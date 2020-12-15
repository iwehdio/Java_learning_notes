## 1、JUC概述

- JUC相关的JDK下的包：
  - java.util.concurrent包。
  - java.util.concurrent.automic包。
  - java.util.concurrent.locks包。
  - java.util.function包。
- 普通业务的线程代码：Thread。
- Runnable接口实现：没有返回值，效率没有Callable高。
- 进程：一个程序或程序的集合，一个进程往往包含多个线程。
- Java可以开启线程吗？
  - 其实是不能的，Java没有权限去开启线程，只能通过`native`本地方法，调用底层的C++开启线程。
  - Java运行在虚拟机上，无法直接操作硬件。
- 并发与并行：
  - 并发：多线程操作同一个资源。
    - CPU单核模拟多条线程，快速交替实现多线程。
  - 并行：多个线程同时执行。
    - CPU多核，真正的同时执行。可以使用线程池操作。
    - 获取CPU的核数`Runtime.getRuntime().availableProcessors()`。
  - 并发编程的本质：充分利用CPU的资源。

- 线程的状态：新生/运行/阻塞/等待/超时等待/终止。

- wait()方法和sleep()方法的区别：

  - 来自不同的类，wait来自Object类，sleep来自Thread类。
  - （一般sleep都用java.util.concurrent下的TimeUnit下的sleep方法操作。）
  - wait会释放锁，sleep不会释放锁。
  - wait必须在同步代码块中使用，sleep可以在任何地方使用。
  
- 传统的Synchronized锁解决卖票问题：

  ```java
  public class Test1 {
      public static void main(String[] args) {
          Ticket ticket = new Ticket();
          //在实战中使用Ticket实现Runnable会导致高耦合，一般使用函数式编程
          new Thread(()->{
              for (int i = 0; i < 40; i++) {
                  ticket.sale();
              }
          }, "A").start();
          new Thread(()->{
              for (int i = 0; i < 40; i++) {
                  ticket.sale();
              }
          }, "B").start();
          new Thread(()->{
              for (int i = 0; i < 40; i++) {
                  ticket.sale();
              }
          }, "C").start();
      }
  }
  
  class Ticket{
     ReentrantLock ， ReentrantReadWriteLock.ReadLock ， ReentrantReadWriteLock.WriteLock  private int number = 30;
      public synchronized void sale(){
          if(number>0){
              System.out.println(Thread.currentThread().getName() + " " + number--);
          }
      }
  }
  ```

- Lock接口：

  - 实现类：可重入锁ReentrantLock ，读可重入锁锁ReentrantReadWriteLock.ReadLock，写可重入锁锁ReentrantReadWriteLock.WriteLock 。

  - 可重入锁ReentrantLock：不传入参数默认非公平锁，可以传入参数设置为公平锁还是非公平锁。

    - 公平锁：在队列中有先来后到。
    - 非公平锁：在队列中可以插队，由CPU调度。

  - 使用：

    ```java
    lock.lock();	//加锁
    lock.tryLock();	//尝试获取锁，返回是否获取到了锁
    try {
        if(number>0){
            System.out.println(Thread.currentThread().getName() + " " + number--);
        }
    } finally {
        lock.unlock();	//解锁
    }
    ```

  - Lock锁和Synchronized锁的区别：

    - Synchronized是内置的关键字，Lock是一个接口。
    - Synchronized无法判断获取锁的状态，Lock可以判断是否获取到了锁。
    - Synchronized会自动释放锁，Lock锁必须手动释放（否则会死锁）。
    - Synchronized在线程1阻塞后线程2会一直等待，Lock锁可以判断能否获取锁，不会一直等待。
    - Synchronized是可重入锁、不可以中断的、非公平的，Lock是可重入锁、可以判断锁、可以设置是否公平锁（Lock锁的灵活度更高）。
    - Synchronized适合锁少量的代码同步问题，Lock适合锁大量的同步代码。

- 生产者的消费者问题（线程通信）：

  - 流程：业务 -> 判断 -> 执行 -> 唤醒。

  - Synchronized版：

    ```java
    public class Test3 {
        public static void main(String[] args) {
            Source source = new Source();
            new Thread(()->{
                for (int i = 0; i < 10; i++) {
                    try {
                        source.increment();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            },"A").start();
            new Thread(()->{
                for (int i = 0; i < 10; i++) {
                    try {
                        source.decrement();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            },"B").start();
        }
    }
    
    class Source{
        private int number = 0;
    
        public synchronized void increment() throws InterruptedException {
            if(number!=0){
                this.wait();
            }
            number++;
            System.out.println(Thread.currentThread().getName() + "->" + number);
            this.notifyAll();
        }
    
        public synchronized void decrement() throws InterruptedException {
            if(number==0){
                this.wait();
            }
            number--;
            System.out.println(Thread.currentThread().getName() + "->" + number);
            this.notifyAll();
        }
    }
    ```

  - 存在的问题：

    - 如果存在多个生产者和消费者，需要将判断if改为循环while，否则会虚假唤醒。
    - 比如线程A和C都是加1，number为1时，A和C都在判断中阻塞，被唤醒后，A先加1，此时虽然number已经是1了，但是C还是会加1。

  - Lock版：

    - java.util.concurrent.locks下的Condination接口，可以用await方法代替wait方法，signalAll方法代替notifyAll。

    ```java
    public class Test4 {
        public static void main(String[] args) {
            Source1 source = new Source1();
            new Thread(()->{
                for (int i = 0; i < 10; i++) {
                    try {
                        source.increment();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            },"A").start();
            new Thread(()->{
                for (int i = 0; i < 10; i++) {
                    try {
                        source.decrement();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            },"B").start();
        }
    }
    
    class Source1{
        private int number = 0;
        Lock lock = new ReentrantLock();
        Condition condition = lock.newCondition();
        public void increment() throws InterruptedException {
            lock.lock();
            try {
                if(number!=0){
                    condition.await();
                }
                number++;
                System.out.println(Thread.currentThread().getName() + "->" + number);
                condition.signalAll();
            } finally {
                lock.unlock();
            }
        }
    
        public void decrement() throws InterruptedException {
            lock.lock();
            try {
                if(number==0){
                    condition.await();
                }
                number--;
                System.out.println(Thread.currentThread().getName() + "->" + number);
                condition.signalAll();
            } finally {
                lock.unlock();
            }
        }
    }
    ```

    - Condination接口的优势：可以精准的通知和唤醒线程。
    - 通过设置多个同步监视器，Condition可以实现精准的通知唤醒某个线程。

    ```java
    //线程A通知线程B运行，线程B通知线程C运行，线程C通知线程A运行
    public class Test5 {
        public static void main(String[] args) {
            Source2 source = new Source2();
            new Thread(()->{
                for (int i = 0; i < 10; i++) {
                    source.printA();
                }
            },"A").start();
            new Thread(()->{
                for (int i = 0; i < 10; i++) {
                    source.printB();
                }
            },"B").start();
            new Thread(()->{
                for (int i = 0; i < 10; i++) {
                    source.printC();
                }
            },"C").start();
        }
    }
    
    class Source2{
        private int num = 0;
        private Lock lock = new ReentrantLock();
        private Condition condition1 = lock.newCondition();
        private Condition condition2 = lock.newCondition();
        private Condition condition3 = lock.newCondition();
    
        public void printA(){
            lock.lock();
            try {
                if(num!=0){
                    condition1.await();
                }
                System.out.println(Thread.currentThread().getName());
                num = 1;
                condition2.signal();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
        }
        public void printB(){
            lock.lock();
            try {
                if(num!=1){
                    condition2.await();
                }
                System.out.println(Thread.currentThread().getName());
                num = 2;
                condition3.signal();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
        }
        public void printC(){
            lock.lock();
            try {
                if(num!=2){
                    condition3.await();
                }
                System.out.println(Thread.currentThread().getName());
                num = 0;
                condition1.signal();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
        }
    }
    ```

    

- 锁是什么，如何判断锁的是谁？
  
  - 锁只会锁两个东西：`new`出来的实例对象，对象的class字节码。
  - 普通方法synchronized，锁的是方法的调用者（实例化的对象）。
  - 如果是静态方法synchronized，锁的是Class类模板。
  - 锁实例对象和锁Class类模板不冲突，是两个锁。
  - 非同步方法不会受锁的影响。



- Callable接口：
  - 相比Runnable接口，它可以有返回值，而且可以抛出异常。run方法变为了call方法。
  - 带一个泛型参数，是call方法的返回值。
  - 使用thread.start启动线程：`new Thread(new Futuretask<V>(Callable<V> c))。`
  - 获取Callable的返回值：`futureTask.get()`。线程有缓存，而且get方法可能会产生阻塞。



- 读写锁：

  - JUC包下的ReadWriteLock接口，实现类ReentrantReadWriteLock。
  - 读可以被多线程同时读，但写的时候只能有一个线程去写。也不能读写同时操作。
  - 更加细粒度的控制，可以提高效率。
  - 可以用`writeLock()`方法和`readLock()`方法创建读锁和写锁，创建出的是Lock锁对象。
  - 读锁就是一种共享锁，写锁就是一种独占锁。

  ```java
  public class Test9 {
      public static void main(String[] args) {
          Cache cache = new Cache();
          for (int i = 0; i < 5; i++) {
              final int temp = i;
              new Thread(()->{
                  cache.write(temp+"",temp+"");
              },String.valueOf(i)).start();
          }
          for (int i = 0; i < 5; i++) {
              final int temp = i;
              new Thread(()->{
                  cache.read(temp+"");
              },String.valueOf(i)).start();
          }
      }
  }
  
  class Cache {
      private volatile Map<String,Object> map = new HashMap<>();
      private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();
  
      public void read(String key){
          Lock lock = readWriteLock.readLock();
          lock.lock();
          try {
              System.out.println(Thread.currentThread().getName()+"读取"+key);
              Object o = map.get(key);
          } finally {
              lock.unlock();
              System.out.println(Thread.currentThread().getName()+"读取完成");
          }
      }
  
      public void write(String key, Object o){
          Lock lock = readWriteLock.writeLock();
          lock.lock();
          try {
              System.out.println(Thread.currentThread().getName()+"写入"+key);
              map.put(key,o);
          } finally {
              lock.unlock();
              System.out.println(Thread.currentThread().getName()+"写入完成");
          }
      }
  }
  ```



## 2、集合类不安全

- 多条线程对ArrayList进行add操作：
  - 使用ArrayList，在并发下，资源是不安全的。会产生并发修改异常ConcurrentModificationexception。
  - 解决1：ArrayList是线程不安全的，Vector是线程安全的（其实就是add方法加了synchronized）。
  - 解决2：使用Collection工具类下的`synchronizedList(new ArrayList<>())`。
  - 解决3：也可以使用`CopyOnWriteArrayList`，是写入时复制（COW思想，是计算机程序设计领域的一种优化策略）。
  - 写入时复制是指，在写入之前先将原有的数据复制出来一份，在复制出来的这份数据上进行修改，修改完后再返回新复制的这份数据。避免数据覆盖。
  - CopyOnWriteArrayList比Vector好在哪：没有使用synchronized，使用Lock锁，效率更高。

- 多条线程对HashSet进行add操作：
  - 与List出现的问题类似。
  - 解决1：使用Collection工具类下的`synchronizedSet(new HashSet<>())`。
  - 解决2：使用`CopyOnWriteArraySet`。
  - HashSet的底层就是HashMap，set.add方法本质就是map.put了一个key。



- 多条线程对HashMap进行add操作：
  - HashMap的构造有多个重载，除了空参以外还有加载因子和初始化容量的构造。在实际情况下都是用带参构造。
  - 在并发下也会出现并发修改异常。
  - 解决1：使用Collection工具类下的`synchronizedMap(new HashMap<>())`。
  - 解决2：使用`ConCurrentHashMap`。



## 3、常用的辅助类

- CountDownLatch类：

  - 构造方法可以传入计数器的初始值，使用`countDown()`方法每次减1。
  - 等待计数器归零，然后再向下执行`await()`方法。
  - 其实就是一个减法计数器。

  ```java
  public class Test7 {
      public static void main(String[] args) {
          CountDownLatch countDownLatch = new CountDownLatch(5);
          for (int i = 0; i < 7; i++) {
              new Thread(()->{
                  System.out.println(Thread.currentThread().getName());
                  countDownLatch.countDown();
              },String.valueOf(i)).start();
          }
          try {
              countDownLatch.await();
          } catch (InterruptedException e) {
              e.printStackTrace();
          }
          System.out.println("减到0");
  
      }
  }
  ```

  

- CyclicBarrier类：

  - 其实就是一个加法计数器。
  - 有两个构造方法，其中一个只需要传入计数器的目标值；另一个不但需要初始值，还需要传入一个达到目标值后执行的线程。
  - `await()`方法在计数器达到目标值之前等待。

  ```java
  public class Test7 {
      public static void main(String[] args) {
          CyclicBarrier cyclicBarrier = new CyclicBarrier(7,()->{
              System.out.println("达到5");
          });
          for (int i = 0; i < 7; i++) {
              new Thread(()->{
                  System.out.println(Thread.currentThread().getName());
                  try {
                      cyclicBarrier.await();
                  } catch (InterruptedException e) {
                      e.printStackTrace();
                  } catch (BrokenBarrierException e) {
                      e.printStackTrace();
                  }
              },String.valueOf(i)).start();
          }
      }
  }
  ```

  

- Semaphore类：

  - 构造方法传入的是线程数量，可以理解为停车位，限流控制数量。
  - `acquire()`方法获得车位，假设已经满了，线程就需要等待，等待其他线程被释放。
  - `release()`方法离开车位，会将当前信号量的释放，然后唤醒等待的线程。
  - 可以用于多个资源互斥的作用，并发限流，控制最大的线程数。

  ```java
  public class Test8 {
      public static void main(String[] args) {
          Semaphore semaphore = new Semaphore(3);
          for (int i = 0; i < 6; i++) {
              final int temp = i;
              new Thread(()->{
                  try {
                      semaphore.acquire();
                      System.out.println(temp+"抢到车位");
                      TimeUnit.SECONDS.sleep(1);
                  } catch (InterruptedException e) {
                      e.printStackTrace();
                  } finally {
                      semaphore.release();
                      System.out.println(temp+"离开车位");
                  }
              }).start();
          }
      }
  }
  ```



## 4、阻塞队列

- 阻塞队列：

  - 是一个队列结构，有写入和读取操作，即入队和出队。
  - 对于写入操作，如果队列满了，就必须阻塞等待。
  - 对于读取操作，如果队列是空的，必须阻塞生产。
  - JUC包下的BlockingQueue接口，继承于Collection的子接口Queue，实现类包括ArrayBlockingQueue、LinkedBlockingQueue、SynchronousQueue等。
  - 此外，Queue还有实现类双端队列Deque和非阻塞队列AbstractQueue。

- 什么情况下使用阻塞队列：多线程并发处理，线程池。

- 阻塞队列的四组API：

  | 方式         | 抛出异常 | 不抛出异常，有返回值 | 阻塞等待 | 超时等待 |
  | ------------ | -------- | -------------------- | -------- | -------- |
  | 添加         | add      | offer                | put      | offer    |
  | 移除         | remove   | poll                 | take     | poll     |
  | 检测队首元素 | element  | peek                 | -        | -        |

  - 不抛出异常，有返回值的API，如果添加/移除失败会返回false/null，不会抛出异常。
  - 阻塞等待是指一直阻塞，等待超时是指超过指定时间后不再阻塞而是退出。
  - 超时等待的方法，可以传入参数超时时间和时间单位。



- SynchronizedQueue同步队列：
  - 容量只有1，进去一个元素，必须等待取出来之后才能再往里面放一个元素。
  - 和其他的阻塞队列不同，同步队列相当于不存储元素，put一个元素必须先take取出来，否则不能再put进去元素。

















