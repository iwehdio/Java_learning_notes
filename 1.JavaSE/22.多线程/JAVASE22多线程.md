## 1、多线程

- 进程：当前正在运行的程序，即一个应用程序在内存中的执行区域。

- 线程：进程中的一个执行控制单元。一个进程可以有一个线程，也可以有多个线程。

- 单线程：安全性高但是效率低。多线程：安全性低但是效率高。

- CPU执行线程具有随机性。

- 多线程的实现方式1 ：Thread 类。

  1. 写一个子类，继承 Thread 类。
  2. 在子类中重写 Thread 类的 run 方法，内容是线程的操作内容。
  3. 创建线程实例。
  4. 使用 `.start()` 方法启动线程。

  ```java
  public class MyThread extends Thread {
      @Override
      public void run(){
          System.out.println("线程");
          System.out.println(getName());
      }
  }
  
  MyThread mt = new MyThread();
  mt.setName("线程A")；
  mt.start();
  ```

- Thread 类中的方法：

  - `String getName()`：获取线程的名字。
  - `void setName(String name)`：改变线程名字。
  - `static Thread currentThread()`：返回对当前正在执行的线程对象的引用。
  - `static void sleep(long millis)`：将线程休眠一段时间。

- 主方法是单线程的。

- 多线程的实现方式2 ：实现 Runnable 接口。

  1. 写一个子类，实现 Runnable 接口。
  2. 在子类中重写 run 方法，内容是线程的操作内容。
  3. 创建线程实例，并将其作为参数传入 Thread 对象。
  4. 使用 `.start()` 方法启动线程。

  ```java
  public class MyThread implements Runnable {
      @Override
      public void run(){
          System.out.println("线程");
      	System.out.println(Thread.currentThread().getName());
      }
  }
  
  MyThread mt = new MyThread(); // 可以只创建一个，但是所有 Thread 对象会共享成员变量
  Thread t1 = new Thread(mt);	// 将实现的接口的对象作为参数传入
  t1.setName("线程A")；
  t1.start();
  Thread t2 = new Thread(mt);
  t2.setName("线程B")；
  t2.start();
  ```

- 为什么多线程需要第二种方法的接口实现：因为 Java 中单一继承的原因，继承了 Thread 类就无法继承其他类。

- 线程的生命周期：

  1. 新建：创建线程对象。
  2. 就绪：具备了执行条件，但没有执行权利。
  3. 运行：具备了执行权利。
  4. 死亡：作为垃圾回收。

  - 等待与唤醒：`void wait()`：使当前线程等待。`void notify()`：唤醒等待的线程。需要使用锁对象调用，调用线程被唤醒后先进入就绪状态。

## 2、同步

- 多个线程共享同一个数据并且并发访问共享的数据，可能会出错。

- `synchronized`关键字：表示同步，可以修饰代码块和方法。被修饰的代码块和方法一旦被某个线程访问，则直接锁住，其他线程无法访问。

  - 同步代码块：

    ```java
    synchronized(锁对象){
        // 执行代码
    }
    ```

  - 同步方法：

    ```java
    private synchronized void method(){
        // 执行代码
    }
    ```

  - 锁对象需要被所有的线程所共享，非静态同步方法的锁对象默认为 this ，静态同步方法的锁对象默认为当前类的字节码对象。

- 同步：安全性高，效率低。

## 3、分票案例

```java
public class Ticket implements Runnable{
	int tickets = 100;
	Object obj = new Object();
	
	@Override
	public void run(){
		while(true){
			method();
		}
	}
	public synchronized void method(){
		if(tickets > 0){
			try {
				Thread.sleep(1000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			System.out.println(Thread.currentThread().getName() + ":" + tickets--);
		}
	}

}

public class Demo {
	public static void main(String[] args) {
		Ticket t = new Ticket();
		Thread td1 = new Thread(t);
		td1.setName("A");
		Thread td2 = new Thread(t);
		td2.setName("B");
		Thread td3 = new Thread(t);
		td3.setName("C");
		
		td1.start();
		td2.start();
		td3.start();
	}
}

```



## 3、Stream流

>  用于对集合和数组进行操作，用于JDK1.8之后。

- 流式思想：建立生产线，按照生产线来生产产品。
- Stream 流是一个集合元素的函数模型，不是集合也不是数据结构，本身不存储任何元素。
- 由于 Lamda 表达式的延迟特性，只有最后一步执行时，整个模型才会按序依此执行。
- Stream 操作的特征：
  - pipelining：中间操作都返回流对象本身，多个操作可以串联成一个管道。
  - 内部迭代。
  - 属于管道流，只能被消费（使用）一次。第一个 Stream 流调用方法，数据就会传到下一个流，第一个流就不能再使用了。
- Stream 操作步骤：
  - 获取数据源、数据转换、执行操作。
  - 每次转换原有的 Stream 流对象不变，返回一个新的 Stream 对象。
- 获取 Stream 流：
  - 所有的 Collection 集合都可以通过`stream()`方法获取流。
  - 对于 Map 集合，可以将其键值对（entrySet）通过`stream()`方法获取流。
  - 通过 Stream 中的`of(T... value)`静态方法方法将数组转换为Stream流。
- Stream 流中的常用方法。
  - 延迟方法：返回值仍然是 Stream 接口自身的方法，可以链式调用。
    - `filter(Predicate<? super T> predicate)`：传入的是 Predicate 接口，将一个流转换为另一个流（过滤）。比如只将以A开头的字符串传递到下一个流。
    - `map(Function<? super T, extends R> mapper)`：传入的是 Function 接口，将一个流转换为另一个流（映射）。比如将String类型的整数转换为Integer类型。
    - `limit(long maxSize)`：截取方法，只取前几个元素传入下一个流。
    - `skip(long n)`：跳过前几个元素，将后边的元素传入下一个流。
    - `concat(Stream<? extends T> a, Stream<? extends T> b)`：静态方法，将两个流合并为一个流。
  - 终结方法：返回值不再是 Stream 接口自身的方法，不可以链式调用。
    - `void forEach(Consumer<? super T> action)`：传入的是 Consumer 接口，用来遍历流中的数据。
    - `long count()`：获取元素格式。

## 4、方法引用

> 用于简化 Lamda 表达式。

- 适用情形：

  - Lamda 表达式中的代码实现，已经有地方存在相同的实现，则可以直接调用这个实现。

  - 前提是，所调用的对象和方法都是以及存在的。

  - 示例：

    ```java
    //函数式接口
    public interface Printable {
        void print(String s);
    }
    //方法以函数式接口为参数
    public static void printString(Printable p) {
        p.print("Hello");
    }
    //对于 Lamda 表达式
    printString((s)->{
        System.out.println(s);
    });
    //方法引用优化
    printString(System.out::println);
    ```

  - 因为 System.out 对象是自动创建好的，所有可以直接让 System.out 中的 println 方法取代 Lamda 表达式。

  - `::`为引用运算符，其所在的表达式被称为方法引用。

  - Lamda 表达式中传递的参数一定是方法引用中的那个方法可以接收的类型。

- 通过对象名引用成员方法：

  - 对象名和成员方法都是已经存在的，否则要先创建对象。
  - 通过`对象名::成员方法`来引用。

- 通过类名引用静态方法：

  -  类名和静态方法都是已经存在的，不需要实例化对象。
  -  通过`类名::静态方法`来引用。

- 通过 super 引用父类成员方法：

  - 存在父类和父类中存在此方法，不需要实例化父类对象。
  - 通过`super::成员方法`来引用。

- 通过 this 引用本类成员方法：

  - 本类中存在此方法。
  - 通过`this::成员方法`来引用。

- 类的构造器引用：

  - Lamda 表达式返回的是，通过构造方法一个新创建的对象。
  - 通过`类名::new`来引用。

- 数组的构造器引用：

  - 返回的是一个新创建的数组。
  - 通过`数据类型[]::new`来引用。



