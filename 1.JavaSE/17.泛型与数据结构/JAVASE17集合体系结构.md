## 1、集合的体系结构

> 数据的组织、存储方式

- 集合的体系结构：不同的集合功能相似，不断向上提取，将共性提取出来。最顶层包含了所有的共性，最底层就是具体的实现。

- Collection接口：

  - `boolean add(E e)`：添加元素，返回是否添成功。
  - `void clear()`：清空。
  - `boolean contains(Object o)`：判断集合中是否包含指定元素。
  - `boolean isEmpty()`：是否为控。
  - `boolean remove(Object o)`：删除元素，返回是否删除成功。
  - `int size()`：返回集合中的元素个数。
  - `Object toArray()`：将集合转换成一个Object类型的数组。

- 集合的遍历方式：

  1. `toArray()`：把集合转换成数组，然后遍历数组。

  2. `iterator()`：返回迭代器对象，对其进行迭代，可以遍历集合。

     - `Object next()`：返回下一个元素。
     - `boolean hasNext()`：判断是否有元素可以获取。
     - 遍历集合：

     ```java
     Collection c = new ArrayList();	//多态
     /*此处应添加元素*/
     Iterator it = c.iterator();	//获取迭代器对象
     while(it.hasNext()){
         System.out.println(it.next());
     }
     ```

- 并发修改异常：

  - 迭代器依赖于集合，相当于集合的副本。如果迭代器操作集合时如果发现迭代器与集合不一样，则抛出并发修改异常。

  - 在使用迭代器进行遍历时，不要用集合进行修改，用迭代器进行修改。

  - 使用 Iterator 的子接口 ListIterator ，其具有 add 方法进行迭代器添加。可以用 List 来获取。

  - 例：

    ```java
    List c = new ArrayList();
    /*此处应添加元素*/
    ListIterator lit = c.ListIterator();
    while(lit.hasNext()){
        String s = (String)lit.next();
        if(s.equals("Part1")){
            lit.add("Part2");	//用迭代器添加
        }
    }
    ```

- foreach ：增强for循环，一般用于遍历集合或者数组。

  - 格式：

    ```java
    /*
    for(元素类型 变量：集合或数组对象)｛
        直接使用变量；
    ｝
    */ 
    Collection c = new ArrayList();
    for(String s : c){
        System.out.println(s);
    }
    ```

  - 在 foreach 找那个不能修改集合，否则会出现并发修改异常。因为它的底层是迭代器。

## 2、泛型

> 是一种广泛的类型，把明确数据类型的工作提前到了编译时期，借鉴了数组的特点。避免了类型转换的问题。

- 使用范围：类名 / 接口名后有 `<E>`标记。

- 创建对象时需要在`<>`中指定数据类型。

- 存储 Student 对象的集合，例：

  ```java
  Collection<Student> c = new ArrayList<Student>();
  Student s = new Student("name");
  c.add(s);
  Iterator<Student> it = c.iterator();
  while(it.hasNext()){
      Student stu = it.next();
  }
  ```
  
- 不写泛型，默认为 Object 类型。

- 带有泛型的方法：

  - 定义格式`public <E> void method(E e)`。
  - 泛型在修饰符和返回值类型之间，如果有静态在静态之后。

- 带有泛型的实现类：

  - 实现类实现接口时，要在接口后指定具体的类型。
  - 或者在实现接口时，同时指定接口与实现类的泛型。

- 泛型的通配符：

  - 用`?`表示不知道使用什么类型来接收。
  - 不能创建对象使用，只能作为方法的参数使用。
  - 只能接收数据，不能存储数据。

- 受限泛型：

  - 泛型的上限：`<? extends E>`。表示使用的泛型只能是 E 类型的子类或 E 本身。
  - 泛型的下限：`<? super E>`。表示使用的泛型只能是 E 类型的父类或 E本身。

## 3、常见数据结构

- 数组：
  - 长度一旦定义则不可改变，元素都有整数索引。
  - 只能存储同一类型的元素，既可以存储基本数据类型也可以存储引用数据类型。
  - 查找快，增删慢。
- 链表：
  -  链连接起来的结点。
  - 结点包括：该结点的地址值，该结点存储的值，下一个结点的地址值。
  - 获取某个结点：遍历链表，一个一个查看。
  - 添加结点：比如在结点2、3之间添加新节点。把结点2的下一个地址值改为新结点的地址值，把新节点的下一个地址值改为结点3的地址值。
  - 删除结点：比如将新节点删除。将结点2的下一个地址值改为结点3的地址值，将新节点回收。
  - 查询慢，增删快。
- 栈：先进后出。
- 队列：先进先出。

> Collection的子接口，是有序的且运行重复。

- 操作对象是列表：`List list = new ArrayList();`。
- `void add(int index,E element)`：在指定位置插入元素。
- `E get(int index)`：获取元素。
- `E remove(int index)`：删除元素，返回删除的元素。
- `E set(int index,E element)`：在指定位置修改元素。
- List 的子类：

  - ArrayList ：底层是数组结构。
  - LinkedList ：底层结构是链表。
  - 查询多增删少选 ArrayList，查询少增删多选LinkedList，不明确则选ArrayList。
- LinkedList 类：

  - 操作对象：`LinkedList list = new LinkedList();`.
- `void addFirst(E e)`：元素添加到索引为0的位置。
  - `void addLast(E e)`：元素添加到索引为 size()-1 的位置。
  - `E getFirst()`：获取索引为0的元素。
  - `E getLast()`：获取索引为 size()-1 的元素。
  - `E removeFirst()`：删除索引为0的元素，返回删除的元素。
  - `E removeLast()`：删除索引为 size()-1 的元素，返回删除的元素。
- Vector集合：

  - 可以实现可增长的对象数组。单线程效率较低。
  - `void addElement(E obj)`：在末尾添加一个元素。
  - `Enumeration<E> elements()`：返回向量的元素枚举。


- HashSet集合：

  - 实现了Set接口，是一个哈希表实例，不保证迭代顺序。
  - 哈希表：
    - 哈希值：是一个十进制的整数，由系统随机给出，是对象的逻辑地址，不是实际的物理地址。
    - 可以使用`public native int hashCode()`方法获取。
    - `native`表示该方法调用的是本地操作系统的方法。
    - 哈希表的初始容量为16。
    - JDK1.8之前：哈希表=数组+链表。
    - JDK1.8之后：哈希表=数组+链表/红黑树。
    - 数组结构把元素进行分组（相同哈希值的元素是一组），链表/红黑树把相同哈希值元素的连接到一起。
    - 如果同组元素超过8个，则不使用链表而使用红黑树。
  - Set集合元素不重复的原理：
    - 创建HashSet，在堆内存中开启空间。
    - Set集合在调用add方法时，会调用元素的hashCode和equals方法，判断元素是否重复。
    - 先找数组中是否有同一哈希值的元素，再找链表/红黑树中有没有相同的元素。
  - hashSet存储自定义类型元素，必须重写hashCode和equals方法。

  LinkedHashSet集合：

  - 由哈希表和链表实现，迭代顺序是确定的。
  - 链表用于理论元素的存储顺序。

  可变参数：

  - 使用场景：方法的参数列表数据类型已经确定，但是参数个数不确定。

  - 使用格式：定义方法时使用，`修饰符 返回值类型 方法名(数据类型...变量名){}`。

  - 底层是一个数组，根据传入参数的不同，创建不同长度的数组。

  - 一个方法的参数列表，只能有一个可变参数。

  - 如果方法的参数有多个，可变参数必须写在参数列表末尾。

  - 实例：计算n个整数的和。

    ```java
    public static int add(int...arr) {
        int sum = 0
        for(int i : arr) {
            sum += arr[i]
        }
        retunr sum
    }
    ```

    