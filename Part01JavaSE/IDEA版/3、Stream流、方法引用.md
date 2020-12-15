# 3、Stream流、方法引用

## 1、Stream流

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

## 2、方法引用

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
  - 通过`类名::静态方法`来引用。

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

