学习自：

- [Java 集合框架看这一篇就够了](https://mp.weixin.qq.com/s/bVOSat47L0Hskfx9akAN6Q)
- [有关 HashMap 面试会问的一切](https://mp.weixin.qq.com/s/HaP1dbofRTAn-XMlhtZn-g)
- [《吊打面试官》系列-HashMap](https://mp.weixin.qq.com/s/0Gf2DzuzgEx0i3mHVvhKNQ)
- [hashmap扩容时死循环问题](https://blog.csdn.net/chenyiminnanjing/article/details/82706942)
- [万万没想到，HashMap默认容量的选择，竟然背后有这么多思考！？](https://mp.weixin.qq.com/s/ktre8-C-cP_2HZxVW5fomQ)
- [全网把Map中的hash()分析的最透彻的文章，别无二家](http://mp.weixin.qq.com/s?__biz=MzI3NzE0NjcwMg==&mid=2650120877&idx=1&sn=401bb7094d41918f1a6e142b6c66aaac&chksm=f36bbf8cc41c369aa44c319942b06ca0f119758b22e410e8f705ba56b9ac6d4042fe686dbed4&scene=21#wechat_redirect)
- [面试官：HashMap 为什么线程不安全？](https://mp.weixin.qq.com/s/VtIpj-uuxFj5Bf6TmTJMTw)
- [Java集合之HashMap（1.7）](https://blog.csdn.net/woshimaxiao1/article/details/83661464)
- [史上最详细的 JDK 1.8 HashMap 源码解析](https://joonwhee.blog.csdn.net/article/details/78996181?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2.control)
- [美团面试题：Hashmap的结构，1.7和1.8有哪些区别，史上最深入的分析](https://blog.csdn.net/qq_36520235/article/details/82417949)
- [《吊打面试官》系列-ArrayList](https://mp.weixin.qq.com/s/WoGclm7SsbURGigI3Mwr3w)
- [ArrayList源码解析，老哥，来一起复习一哈？](https://mp.weixin.qq.com/s/3PNWmtS-bEZgZjd9wyMiDA)
- [List集合就这么简单【源码剖析】](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247484130&idx=1&sn=4052ac3c1db8f9b33ec977b9baba2308&chksm=ebd743e3dca0caf51b170fd4285345c9d992a5a56afc28f2f45076f5a820ad7ec08c260e7d39&scene=21###wechat_redirect)
- [HashMap就是这么简单【源码剖析】](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247484139&idx=1&sn=bb73ac07081edabeaa199d973c3cc2b0&chksm=ebd743eadca0cafc532f298b6ab98b08205e87e37af6a6a2d33f5f2acaae245057fa01bd93f4&scene=21###wechat_redirect)
- [CopyOnWriteArrayList你都不知道，怎么拿offer？](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247484380&idx=1&sn=c1aa61d29818005f886c61575d2a5d36&chksm=ebd742dddca0cbcb4c8d5792cccfb774a268e64d2b65a94dd0781c17a73bfadf4f930c33f4be&scene=21###wechat_redirect)
- [《求求大厂给个Offer》List面试题](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247491557&idx=1&sn=3d2e237ba7a485322455556c1a2e4b24&chksm=ebd75ee4dca0d7f200fcf77d2a611273a6695fc5ff0881c4f59d2b0219a32f7a7b424914a56e&token=67449918&lang=zh_CN#rd)
- [《求求大厂给个Offer》Map面试题](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247491738&idx=1&sn=6da7d0b7fdcbbf4142ae2cda0065d0a5&chksm=ebd4a19bdca3288d0bfd16548608a8c0f2b27050fd9fe1ce212bc1aadf40c43b56edf7af82ad&token=67449918&lang=zh_CN#rd)



- Java 集合，也称作容器，主要是由`两大接口 (Interface)` 派生出来的：`Collection 和 Map`。
- 这两大接口的不同之处在于：
  - Collection 存放单一元素；
  - Map 存放 key-value 键值对。
- 学习这些集合框架，应当有 4 个目标：
  1. 明确每个接口和类的对应关系；
  2. 对每个接口和类，熟悉常用的 API；
  3. 对不同的场景，能够选择合适的数据结构并分析优缺点；
  4. 学习源码的设计。

## 1、Collection

![image-20210107204637501](http://iwehdio.gitee.io/img/my-img/21-01/image-20210107204637501.png)

- 最上层的 Collection里定义了很多方法，这些方法也都会继承到各个子接口和实现类里。

- 操作集合，无非就是「增删改查」四大类，也叫 `CRUD`:

  Create, Read, Update, and Delete。

- 把这些 API 分为这四大类：

  | 功能 |            方法             |
  | :--- | :-------------------------: |
  | 增   |       add()/addAll()        |
  | 删   |    remove()/ removeAll()    |
  | 改   | Collection Interface 里没有 |
  | 查   |  contains()/ containsAll()  |
  | 其他 | isEmpty()/size()/toArray()  |

- 增：

  ```java
  boolean add(E e);
  ```

  - `add()` 方法传入的数据类型必须是 Object，所以当写入基本数据类型的时候，会做自动装箱 auto-boxing 和自动拆箱 unboxing。
  - 还有另外一个方法 `addAll()`，可以把另一个集合里的元素加到此集合中。

  ```java
  boolean addAll(Collection<? extends E> c);
  ```

- 删：

  ```java
  boolean remove(Object o);
  ```

  - `remove()`是删除的指定元素。
  - 那和 `addAll()` 对应的，自然就有`removeAll()`，就是把集合中的所有元素都删掉。

  ```java
  boolean removeAll(Collection<?> c);
  ```

- 改：Collection Interface 里并没有直接改元素的操作。

- 查：

  - 查下集合中有没有某个特定的元素：

  ```java
  boolean contains(Object o);
  ```

  - 查集合 A 是否包含了集合 B：

  ```java
  boolean containsAll(Collection<?> c);
  ```

- 集合整体操作：

  - 判断集合是否为空：

  ```java
  boolean isEmpty();
  ```

  - 集合的大小：

  ```java
  int size();
  ```

  - 把集合转成数组：

  ```java
  Object[] toArray();
  ```

### List

- List 最大的特点就是：`有序`，`可重复`。

- List 的实现方式有 LinkedList 和 ArrayList 两种。

- API时间复杂度：

  | 功能 |        方法         | ArrayList | LinkedList |
  | :--- | :-----------------: | :-------: | :--------: |
  | 增   |      add(E e)       |   O(1)    |    O(1)    |
  | 增   | add(int index, E e) |   O(n)    |    O(n)    |
  | 删   |  remove(int index)  |   O(n)    |    O(n)    |
  | 删   |     remove(E e)     |   O(n)    |    O(n)    |
  | 改   | set(int index, E e) |   O(1)    |    O(n)    |
  | 查   |   get(int index)    |   O(1)    |    O(n)    |

  - `add(E e)` 是在尾巴上加元素，虽然 ArrayList 可能会有扩容的情况出现，但是均摊复杂度（amortized time complexity）还是 O(1) 的。
  - `add(int index, E e)`是在特定的位置上加元素，LinkedList 需要先找到这个位置，再加上这个元素，虽然单纯的「加」这个动作是 O(1) 的，但是要找到这个位置还是 O(n) 的。
  - 二者的选择：
    1. 改查选择 ArrayList；
    2. 增删在尾部的选择 ArrayList；
    3. 其他情况下，如果时间复杂度一样，推荐选择 ArrayList，因为 overhead 更小，或者说内存使用更有效率。
  
- 其他API：

  - void trimToSize()：将此 ArrayList 实例的容量调整为列表的当前大小。
  - int indexOf(Object o)：返回此列表中首次出现的指定元素的索引，或如果此列表不包含元素，则返回 -1。
  - int lastIndexOf(Object o)：返回此列表中最后一次出现的指定元素的索引，或如果此列表不包含索引，则返回 -1。
  - boolean contains(Object o)：如果此列表中包含指定的元素，则返回 true。

- ArrayList是什么？

  - ArrayList就是数组列表，主要用来装载数据，当我们装载的是基本类型的数据int，long，boolean，short，byte…的时候我们只能存储他们对应的包装类，它的主要底层实现是数组Object[] elementData。是线程不安全的。

  ![image-20210112192359810](http://iwehdio.gitee.io/img/my-img/21-01/image-20210112192359810.png)

  ![image-20210112192245764](http://iwehdio.gitee.io/img/my-img/21-01/image-20210112192245764.png)

- 为啥线程不安全还使用他呢？

  - 因为正常使用的场景中，都是用来查询，不会涉及太频繁的增删，如果涉及频繁的增删，可以使用LinkedList，如果你需要线程安全就使用Vector。

- ArrayList的初始化：

  - ArrayList可以通过构造方法在初始化的时候指定底层数组的大小。
  - 通过无参构造方法的方式ArrayList()初始化，则赋值底层数Object[] elementData为一个默认空数组Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {}所以数组容量为0，只有真正对数据进行添加add时，才分配默认DEFAULT_CAPACITY = 10的初始容量。

  ![image-20210112192230918](http://iwehdio.gitee.io/img/my-img/21-01/image-20210112192230918.png)

  - `new ArrayList()`会将`elementData` 赋值为 DEFAULTCAPACITY_EMPTY_ELEMENTDATA，`new ArrayList(0)`会将`elementData` 赋值为 EMPTY_ELEMENTDATA，EMPTY_ELEMENTDATA添加元素会扩容到容量为`1`，而DEFAULTCAPACITY_EMPTY_ELEMENTDATA扩容之后容量为`10`。

- ArrayList的扩容：

  - 数组的长度是有限制的，而ArrayList是可以存放任意数量对象，长度不受限制。是通过扩容实现的。
  - 比如有一个长度为10的数组，现在要新增一个元素，发现已经满了。
  - 第一步会重新定义一个长度为10+10/2的数组也就是新增一个长度为15的数组。
  - 然后把原数组的数据，原封不动的复制到新数组中，这个时候再把指向原数的地址换到新数组。

- ArrayList的默认数组大小为什么是10？

  - 据说是因为sun的程序员对一系列广泛使用的程序代码进行了调研，结果就是10这个长度的数组是最常用的最有效率的。

- ArrayList在增删的时候是怎么做的？

  - 有指定index新增，也有直接新增的，在这之前他会有一步校验长度的判断ensureCapacityInternal**，就是说如果长度不够，是需要扩容的。

  ![image-20210112193532939](http://iwehdio.gitee.io/img/my-img/21-01/image-20210112193532939.png)

  - 调用grow扩容时，扩容为原来的1.5倍。如果原容量的1.5倍比minCapacity小，那么就扩容到minCapacity。

  ![image-20210112193642423](http://iwehdio.gitee.io/img/my-img/21-01/image-20210112193642423.png)

  - 校验之后，就是数组的copy。具体的原理是，如果要插入的位置是index，那就把index后的数组元素都复制一份，并且放在index+1处。然后再将设置index处的元素。

  ![image-20210112193906986](http://iwehdio.gitee.io/img/my-img/21-01/image-20210112193906986.png)

  - 效率低的原因也就是在此，即新增元素需要复制后边所有的元素。

- `ArrayList(int initialCapacity)`会不会初始化数组大小size？

  - 不会初始化大小。因为这里只是初始化了底层数组的大小，但是并没有关联数组的大小size。
  - 而且此时并没有往ArrayList中添加数据，size也应该是0。只有添加元素，size才会变化。

- ArrayList插入删除一定慢么？

  - 取决于你删除的元素离数组末端有多远，ArrayList拿来作为堆栈来用还是挺合适的，push和pop操作完全不涉及数据移动操作。

- 删除怎么实现的呢？

  - 删除其实跟新增是一样的，不过叫是叫删除，但是在代码里面我们发现，他还是在copy一个数组。
  - 即如果要删除index处的元素，则复制index+1开始的数组，放到index处，相当于覆盖了index。

  ![image-20210112194818511](http://iwehdio.gitee.io/img/my-img/21-01/image-20210112194818511.png)

- ArrayList是线程安全的么？

  - 不是，线程安全版本的数组容器是Vector。
  - Vector的实现很简单，就是把所有的方法统统加上synchronized。
  - 也可以不使用Vector，用Collections.synchronizedList把一个普通ArrayList包装成一个线程安全版本的数组容器也可以，原理同Vector是一样的，就是给所有的方法套上一层synchronized。

- 不用Vector，线程安全的List还有什么？

  - 在java.util.concurrent包下还有一个类，叫做CopyOnWriteArrayList。是一个线程安全的List，底层是通过复制数组的方式来实现的。
  - 写入时复制CopyOnWrite机制：
    - 如果有多个调用者（callers）同时请求相同资源（如内存或磁盘上的数据存储），他们会共同获取相同的指针指向相同的资源。
    - 直到某个调用者试图修改资源的内容时，系统才会真正复制一份专用副本（private copy）给该调用者，而其他调用者所见到的最初的资源仍然保持不变。
    - 优点是如果调用者没有修改该资源，就不会有副本（private copy）被建立，因此多个调用者只是读取操作时可以共享同一份资源。
  - `add()`方法的实现：加`lock`锁（ReentrantLock），然后会复制出一个新的数组，往新的数组里边`add`真正的元素，最后把array的指向改变为新的数组。
  - `get()`方法又或是`size()`方法只是获取array所指向的数组的元素或者大小。读不加锁，写加锁。
  - CopyOnWriteArrayList很耗费内存的，每次`set()/add()`都会复制一个数组出来。
  - CopyOnWriteArrayList只能保证数据的最终一致性，不能保证数据的实时一致性。假设两个线程，线程A去读取CopyOnWriteArrayList的数据，还没读完，现在线程B把这个List给清空了，线程A此时还是可以把剩余的数据给读出来。

- ArrayList用来做队列合适么？

  - 队列一般是FIFO（先入先出）的，如果用ArrayList做队列，就需要在数组尾部追加数据，数组头部删除数组，反过来也可以。
  - 但是无论如何总会有一个操作会涉及到数组的数据搬迁，这个是比较耗费性能的。所以ArrayList不适合做队列。

- 那数组适合用来做队列么？

  - 数组是非常合适的。
  - 比如ArrayBlockingQueue内部实现就是一个环形队列，它是一个定长队列，内部是用一个定长数组来实现的。
  - 简单点说就是使用两个偏移量来标记数组的读位置和写位置，如果超过长度就折回到数组开头，前提是它们是定长数组。

- ArrayList和LinkedList性能比较如何？

  - 论遍历ArrayList要比LinkedList快得多，ArrayList遍历最大的优势在于内存的连续性，CPU的内部缓存结构会缓存连续的内存片段，可以大幅降低读取内存的性能开销。
  - ArrayList的增删底层调用的copyOf()被优化过，现代CPU对内存可以块操作，ArrayList的增删一点儿也不会比LinkedList慢。

- 为什么elementData没有用private修饰呢？

  - `private`修饰会影响内部类的访问。

- Vector：
  - Vector 和 ArrayList 一样，也是继承自 java.util.AbstractList，底层也是用数组来实现的。但是现在已经被弃用了，因为它加了太多的 synchronized，导致效率降低。
  -  ArrayList 的扩容实现是将新容量变为原容量的1.5倍。
  -  Vector 的扩容实现是，先看有没有定义扩容量 capacityIncrement，没定义则扩容为原来的2倍。



### Queue & Deque

- Queue有两组 API，基本功能是一样的，但是呢：

  - 一组是会抛异常的；
  - 另一组会返回一个特殊值。

  | 功能 |  抛异常   |  返回值  |
  | :--- | :-------: | :------: |
  | 增   |  add(e)   | offer(e) |
  | 删   | remove()  |  poll()  |
  | 查   | element() |  peek()  |

- 为什么会抛异常呢？

  - 比如队列空了，那 remove() 就会抛异常，但是 poll() 就返回 null；element() 就会抛异常，而 peek() 就返回 null 。

- Deque 是两端都可以进出的，那自然是有针对 First 端的操作和对 Last 端的操作，那每端都有两组，一组抛异常，一组返回特殊值：

  | 功能 |           抛异常            |           返回值            |
  | :--- | :-------------------------: | :-------------------------: |
  | 增   |   addFirst(e)/ addLast(e)   | offerFirst(e)/ offerLast(e) |
  | 删   | removeFirst()/ removeLast() |   pollFirst()/ pollLast()   |
  | 瞧   |    getFirst()/ getLast()    |   peekFirst()/ peekLast()   |

- 实现类有三个：`LinkedList`、`ArrayDeque`、`PriorityQueue`：

  - 如果想实现「普通队列 - 先进先出」的语义，就使用 LinkedList 或者 ArrayDeque 来实现；
  - 如果想实现「优先队列」的语义，就使用 PriorityQueue；
  - 如果想实现「栈」的语义，就使用 ArrayDeque。

- 在实现普通队列时，如何选择用 LinkedList 还是 ArrayDeque 呢？

  - 推荐使用 ArrayDeque，因为效率高，而 LinkedList 还会有其他的额外开销（overhead）。

- ArrayDeque 和 LinkedList 的区别有哪些呢？

  1. ArrayDeque 是一个可扩容的数组，LinkedList 是双向链表结构；
  2. ArrayDeque 里不可以存 null 值，但是 LinkedList 可以；
  3. ArrayDeque 在操作头尾端的增删操作时更高效，但是 LinkedList 只有在当要移除中间某个元素且已经找到了这个元素后的移除才是 O(1) 的；
  4. ArrayDeque 在内存使用方面更高效。

- 什么情况下要选择用 LinkedList 呢？

  - Java 6 以前。因为 ArrayDeque 在 Java 6 之后才有的。

- Stack：

  -  Java 中有 Stack 这个类，但是呢，官方文档都说不让用了。因为 Vector 已经过被弃用了，而 Stack 是继承 Vector 的。
  - 想实现 Stack 的语义，就用 ArrayDeque 。



### Set

- Set 的特点是`无序`，`不重复`的。
- Set 的常用实现类有三个：

  - HashSet: 采用 Hashmap 的 key 来储存元素，主要特点是无序的，基本操作都是 O(1) 的时间复杂度，很快。
  - LinkedHashSet: 这个是一个 HashSet + LinkedList 的结构，特点就是既拥有了 O(1) 的时间复杂度，又能够保留插入的顺序。
  - TreeSet: 采用红黑树结构，特点是可以有序，可以用自然排序或者自定义比较器来排序；缺点就是查询速度没有 HashSet 快。
- 每个 Set 的底层实现其实就是对应的 Map：
  - 数值放在 map 中的 key 上，value 上放了个 PRESENT，是一个静态的 Object，相当于 place holder，每个 key 都指向这个 object。



## 2、Map

- Map 也有这三个实现类：

  - HashMap: 与 HashSet 对应，也是无序的，O(1)。
  - LinkedHashMap: 这是一个「HashMap + 双向链表」的结构，落脚点是 HashMap，所以既拥有 HashMap 的所有特性还能有顺序。
  - TreeMap: 是有序的，本质是用二叉搜索树来实现的。

- HashMap基本操作：

  - 增：put(K key, V value)
  - 删：remove(Object key)
  - 改：还是用的 put(K key, V value)
  - 查：get(Object key) / containsKey(Object key)
  - 为什么有些 key 的类型是 Object，有些是 K 呢？
    - 这是因为如果重写了equals()和HashCode()，在 get/remove 的时候，不一定是用的同一个 object。

- HashMap的实现原理：

  - 对于 HashMap 中的每个 key，首先通过hashCode函数计算出哈希值，然后通过hash函数（最基本的是模于数组的长度），将哈希值转换为数组的地址。
  - 数组里面每个地方都存了Key-Value这样的实例，在Java7叫Entry在Java8中叫Node。是HashMap中的一个静态内部类，实现了Map.Entry接口。

  ![image-20210107220831982](http://iwehdio.gitee.io/img/my-img/21-01/image-20210107220831982.png)

- HashMap 中是如何保证元素的唯一性？即相同的元素会不会算出不同的哈希值呢？

  - 通过 hashCode() 和 equals() 方法来保证元素的唯一性。
  - 如果 key 的 hashCode() 值相同，那么有可能是要发生 hash collision 了，也有可能是真的遇到了另一个自己。那么如何判断呢？继续用 equals() 来比较。
    - hashCode() 决定了 key 放在这个桶里的编号，也就是在数组里的 index；
    - equals() 是用来比较两个 object 是否相同的。

- 为什么重写 equals() 方法，一定要重写 hashCode() 呢？

  - 对于hashCode()而言，不同的对象会返回不同的哈希值。
  - 那么在这个条件下，有两个对象是equals的，那如果不重写 hashCode()，算出来的哈希值都不一样，就会去到不同的 buckets 了。而get的时候，也找不到对应bucket中的equals的对象。
  - hashCode() 和 equals() 方法都是在 Object类中定义的。
    - 在Object中，`equals()` 方法就是比较这两个 references 是否指向了同一个 object，即与`==`一样。
    - 在Object中声明，是为了让继承Object的类重写。
    - hashCode() 返回的并不一定是对象的（虚拟）内存地址，具体取决于运行时库和JVM的具体实现。
  - 所以，HashMap中的key如果是Object类型，需要重写hashCode和equals方法。
    - hashCode用于确定存储数据的位置，实现不当会导致严重的哈希冲突。
    - equals用于比较存储位置上是否存在这个key。

- 如果不同的元素算出了相同的哈希值，即多个 key 对应了同一个桶。就是发生了哈希碰撞。

  - 哈希碰撞一般有两类解决方式：
    - Separate chaining独立链和Open addressing开放寻址。
    - Java中使用的是第一种 `Separate chaining`，即在发生碰撞的那个桶后面再加一条“链”来存储。
    
    ![image-20210108193422690](http://iwehdio.gitee.io/img/my-img/21-01/image-20210108193422690.png)
    
  - 对于数组中存放的每一个节点，都会保存自身的hash、key、value、以及下个节点：
  
    ![image-20210108192243749](http://iwehdio.gitee.io/img/my-img/21-01/image-20210108192243749.png)
  
- 在多线程环境下，HashMap有可能会有数据丢失和获取不了最新数据的问题。

- 新的Entry节点在插入链表的时候，是怎么插入的？

  - java8之前是头插法，就是说新来的值会取代原有的值，原有的值就顺推到链表中去。因为写这个代码的作者认为后来的值被查找的可能性更大一点，提升查找的效率。

  - 但是，在java8之后，都是用尾部插入了。因为头插法在多线程并发扩容时，会导致死循环（如果获取哈希值相同但是不存在的键）。

  - 在java7中为什么出现死循环？

    - 场景：多线程操作一个HashMap，并且并发扩容。
    - 问题主要出现在将旧Entry数组中的元素移到新Entry数组的`transfer()`方法中。
    - 如果线程A在do循环的第四句时停住了，等到线程B扩容完成才开始执行。本来应该从链表的第一个元素开始移动，但是线程B扩容时使用了头插法，导致原本的第一个元素变为了最后一个。
    - 注意根据Java内存模型，在线程B完成后，线程A获取到达都是最新的newTable，但是e和next还是之前的。

    ```java
    void transfer(Entry[] newTable)
    {
        Entry[] src = table;
        int newCapacity = newTable.length;
        //  从OldTable里摘一个元素出来，然后放到NewTable中
        for (int j = 0; j < src.length; j++) {
            Entry<K,V> e = src[j];
            if (e != null) {
                src[j] = null;
                do {
                    Entry<K,V> next = e.next;	
                    int i = indexFor(e.hash, newCapacity);
                    e.next = newTable[i];
                    newTable[i] = e;	//如果线程A在这里停住了
                    e = next;
                } while (e != null);
            }
        }
    }
    ```

    - 这样，线程A就会把原本的第一个元素插入两次，导致死循环。在有些情况下还会导致数据丢失。

    ![image-20210108202226270](http://iwehdio.gitee.io/img/my-img/21-01/image-20210108202226270.png)

  - 总之，是因为头插法改变了链表的顺序，如果其他线程获取的是扩容前的顺序关系，就会出现死循环。

  - 在Java 8中使用尾插法，不会出现循环链表，但是在put时可能会出现数据覆盖，还是线程不安全的。

- HashMap的扩容机制：
  
  - 如果 pairs 太多，buckets 太少怎么破？
  
    - 扩容。 也就是碰撞太多的时候，会把数组扩容至两倍（默认）。所以这样虽然 hash 值没有变，但是因为数组的长度变了，所以算出来的 index 就变了，就会被分配到不同的位置上了。
  
  - 什么时候会 resize 呢？也就是怎么衡量桶里是不是足够拥挤要扩容了呢？
  
    - 有两个因素：
  
      - Capacity：HashMap当前长度。
      - LoadFactor：负载因子，默认值0.75f。
  
      ![image-20210108193044981](http://iwehdio.gitee.io/img/my-img/21-01/image-20210108193044981.png)
  
    - 即用 pair 的数量除以 buckets 的数量，也就是平均每个桶里装几对。Java 中默认值是 0.75f，如果超过了这个值就会 rehashing。
  
  - 怎么扩容的呢？
  
    - 扩容：创建一个新的Entry空数组，长度是原数组的2倍。
    - Rehash：遍历原Entry数组，把所有的Entry重新Hash到新数组。
  
  - 为什么要重新Hash呢，而不是直接复制过去？
  
    - 是因为长度扩大以后，Hash的规则也随之改变。Hash的公式---> index = HashCode（Key） & （Length - 1）
    - 原来长度（Length）是8你位运算出来的值是2 ，新的长度是16你位运算出来的值明显不一样了。
  
    ![image-20210108193947075](http://iwehdio.gitee.io/img/my-img/21-01/image-20210108193947075.png)
  
  - java 7 是先扩容再插入，java 8 是先插入再扩容。
  
- 为什么HashMap的默认初始化长度是16？

  ![image-20210108202906869](http://iwehdio.gitee.io/img/my-img/21-01/image-20210108202906869.png)

  - 在创建HashMap的时候，阿里巴巴规范插件会提醒我们最好赋初值，而且最好是2的幂。16可能是考量了扩容可能和空间的经验值。

  - 首先，这样是为了位运算的方便，位与运算比算数计算的效率高了很多。

  - 其次，是为了服务将Key均匀的映射到index的算法（即`hash & (length-1)`）。因为对于2的幂，length-1是全为1，这样数组的索引就是hash值的低位，如果哈希值是均匀的，则数组也是均匀分布的。

    ![image-20210108204331828](http://iwehdio.gitee.io/img/my-img/21-01/image-20210108204331828.png)

  - 最后，在扩容时，新容量默认是原容量的二倍，这样就只有最高位不同，原数组中的一些元素的索引位置不变。

    <img src="http://iwehdio.gitee.io/img/my-img/21-01/image-20210108204318619.png" alt="image-20210108204318619" style="zoom:67%;" />

  - 为什么默认加载因子是0.75？

    - 首先，0.75 * 16=12是一个整数，大于16的就更是了。
    - 其次，是一种查询效率和空闲效率的折中。
      - 加载因子过高，虽然减少了空间开销，提高了空间利用率，但同时哈希冲突的概率增加，增加了查询时间成本。
      - 加载因子过低，虽然可以减少查询时间成本，但是空间利用率很低，同时提高了rehash操作的次数。

- HashMap指定容量初始化：

  - 当我们通过HashMap(int initialCapacity)设置初始容量的时候，HashMap并不一定会直接采用我们传入的数值，而是经过计算，得到一个新值，目的是提高hash的效率。(1->1、3->4、7->8、9->16)。
  - 在JDK 1.7和JDK 1.8中，HashMap初始化这个容量的时机不同。JDK 1.8中，在调用HashMap的构造函数定义HashMap的时候，就会进行容量的设定。而在JDK 1.7中，要等到第一次put操作时才进行这一操作。
  - HashMap根据用户传入的初始化容量，利用无符号右移和按位或运算等方式计算出第一个大于该数的2的幂。

- HashMap中的`hash()`方法：

  - 在同一个版本的Jdk中，HashMap、HashTable以及ConcurrentHashMap里面的hash方法的实现是不同的。不同的版本的JDK中（Java7 和 Java8）中也是有区别的。

  - hash方法的功能是根据Key来定位这个K-V在链表数组中的位置的。也就是hash方法的输入应该是个Object类型的Key，输出应该是个int类型的数组下标。

  - 基本原理：只要调用Object对象的hashCode()方法，该方法会返回一个整数，然后用这个数对HashMap或者HashTable的容量进行取模就行了。

  - HashMap In Java 7：

    ```java
    final int hash(Object k) {
       int h = hashSeed;
       if (0 != h && k instanceof String) {
           return sun.misc.Hashing.stringHash32((String) k);
       }
    	//扰动
       h ^= k.hashCode();
       h ^= (h >>> 20) ^ (h >>> 12);
       return h ^ (h >>> 7) ^ (h >>> 4);
    }
    
    static int indexFor(int h, int length) {
       return h & (length-1);
    }
    ```

    - 由于HashMap使用位运算代替了取模运算，这就带来了另外一个问题，那就是有可能发生冲突。比如：`CA11 1000`和`0001 1000`在对`0000 1111`进行按位与运算后的值是相等的。
    - 对key的hashCode进行扰动计算，防止不同hashCode的高位不同但低位相同导致的hash冲突。简单点说，就是为了把高位的特征和低位的特征组合起来，降低哈希冲突的概率，也就是说，尽量做到任何一位的变化都能对最终得到的结果产生影响。

  - HashTable In Java 7：

    ```java
    private int hash(Object k) {
       // hashSeed will be zero if alternative hashing is disabled.
       return hashSeed ^ k.hashCode();
    }
    //起了indexFor方法的作用
    int index = (hash & 0x7FFFFFFF) % tab.length;
    ```

    - 把hash值和0x7FFFFFFF做一次按位与操作呢，主要是为了保证得到的index的的二进制的第一位为0（一个32位的有符号数和0x7FFFFFFF做按位与操作，其实就是在取绝对值。），也就是为了得到一个正数。因为有符号数第一位0代表正数，1代表负数。
    - HashTable简单的取模是有一定的考虑在的。涉及到HashTable的构造函数和扩容函数：
      - HashTable默认的初始大小为11，之后每次扩充为原来的2n+1。
      - 也就是说，HashTable的链表数组的默认大小是一个素数、奇数。之后的每次扩充结果也都是奇数。
      - 由于HashTable会尽量使用素数、奇数作为容量的大小。当哈希表的大小为素数时，简单的取模哈希的结果会更加均匀（可以证明）。

  - 比较：

    - 当哈希表的大小为素数时，简单的取模哈希的结果会更加均匀，所以单从这一点上看，HashTable的哈希表大小选择，似乎更高明些。因为hash结果越分散效果越好。
    - 在取模计算时，如果模数是2的幂，那么我们可以直接使用位运算来得到结果，效率要大大高于做除法。所以从hash计算的效率上，又是HashMap更胜一筹。
    - 但是，HashMap为了提高效率使用位运算代替哈希，这又引入了哈希分布不均匀的问题，所以HashMap为解决这问题，又对hash算法做了一些改进，进行了扰动计算。

  - ConcurrentHashMap In Java 7：

    ```java
    private int hash(Object k) {
       int h = hashSeed;
    
       if ((0 != h) && (k instanceof String)) {
           return sun.misc.Hashing.stringHash32((String) k);
       }
    
       h ^= k.hashCode();
    
       // Spread bits to regularize both segment and index locations,
       // using variant of single-word Wang/Jenkins hash.
       h += (h <<  15) ^ 0xffffcd7d;
       h ^= (h >>> 10);
       h += (h <<   3);
       h ^= (h >>>  6);
       h += (h <<   2) + (h << 14);
       return h ^ (h >>> 16);
    }
    
    int j = (hash >>> segmentShift) & segmentMask;
    ```

    - 上面这段关于ConcurrentHashMap的hash实现其实和HashMap如出一辙。都是通过位运算代替取模，然后再对hashcode进行扰动。区别在于，ConcurrentHashMap 使用了一种变种的Wang/Jenkins 哈希算法，其主要目的也是为了把高位和低位组合在一起，避免发生冲突。

  - HashMap In Java 8:

    ```java
    static final int hash(Object key) {
       int h;
       return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
    ```

    - 原理和Java 7中基本类似。Java 8中这一步做了优化，只做一次16位右位移异或混合，而不是四次，但原理是不变的。
    - 优化了高位运算的算法，通过hashCode()的高16位异或低16位实现的。主要是从速度、功效、质量来考虑的。
    - 认为引入红黑树后，即使哈希冲突比较严重，寻址效率也足够高，所以并未在哈希值的计算上做过多设计。

  - HashTable In Java 8：和Java 7中的实现几乎无差别。

  - ConcurrentHashMap In Java 8：将Key的hashCode值与其高16位作异或并保证最高位为0（从而保证最终结果为正整数）。

- 为什么java8中使用了红黑树：
  - 如果只使用链表，在哈希碰撞很严重的情况下，这个链表可能会很长，那么put/get操作都可能需要遍历这个链表。也就是说时间复杂度在最差情况下会退化到O(n)。
  - 红黑树可以将这种极端情况下的查询复杂度降低到O(log n)。
  
- 为什么Hashmap中的链表大小超过八个时会自动转化为红黑树，当删除小于六时重新变为链表？
  - 首先，桶中元素的个数符合泊松分布。根据泊松分布，在负载因子默认为0.75的时候，单个hash槽内元素个数大于8的概率小于百万分之一。这就是说红黑树只是面对极端情况下的。
  - 其次，由于树节点的大小大约是常规节点的两倍，因此我们仅在容器包含足够的节点以保证使用时才使用它们，当它们变得太小（由于移除或调整大小）时，它们会被转换回普通的链表。
  - 最后，如果元素小于等于6树还原转为链表，大于等于8转为树），中间有个差值7可以有效防止链表和树频繁转换。
  - 此外，链表转为红黑树还需要散列表底层数组长度大于64才行。
  
- 为什么 HashMap 中 String、Integer 这样的包装类适合作为 key 键：
  - 是final类型，具有不可变性。保证了key的不可更改，不会出现放入和获取时哈希值不同。
  - 内部重写了equals和hashCode方法，不容易出现哈希碰撞。
  
- HashMap的k-v都允许为null：
  - key为null是唯一的，因为key为null，哈希值默认为0。
  - 值可以有多个null。
  
- HashMap不保证有序，而且存储顺序可能变化：
  - 存储顺序是根据hash值得到的数组下标，讲求随机和均匀性。
  - 存在扩容操作，可能会导致存储位置变化。


