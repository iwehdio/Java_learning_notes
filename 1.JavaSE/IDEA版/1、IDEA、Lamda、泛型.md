## 1、IDEA

- 快捷键：
  - alt + enter：导入包，自动修正代码。
  - ctrl + Y：删除光标所在行。
  - ctrl + D：复制光标所在行。
  - ctrl + alt + L：格式化语句。
  - ctrl + /：单行注释。
  - ctrl + shift + /：多行注释。
  - alt + insert：自动生成方法。
  - alt + shift + 上下箭头：移动当前代码行。
  - ctrl + alt + v：自动补全返回值。

## 2、Lamda表达式

- 标准格式：`(参数列表) -> {重写方法的代码}`。

  - 包括一些参数、一个箭头和一段代码。

  - () 表示接口中抽象方法的参数列表，没有参数则为空。

  - -> 表示将参数传递给方法体。

  - {} 表示重写接口的抽象方法的方法体。

  - 如果有返回值则 return 返回值。

  - 相比匿名内部类，Lamda表达式不会生成 .class 文件。

  - 示例：

    ```java
    public interface Cook {
        public void makeFood();
    }
    
    public class Demo {
        public static void main(String[] args) {
            invokeCook(() -> {
                System.out.println("cook");
            })
        }
        
        public static void invokeCook(Cook cook) {
            cook.makeFood();
        }
    }
    ```

- Lamda表达式的简化：

  - 参数列表中的数据类型可省略。
  - 参数如果只有一个，那么 () 可以省略。
  - 如果重写方法的代码只有一行，那么可以省略 {} 和 return; 。

- Lamda表达式的使用前提：

  - 接口中有且只有一个抽象方法，即函数式接口。
  - 必须有上下文推断，方法的参数或局部变量类型必须为Lamda对应的接口类型。

## 3、泛型

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

## 4、Arrays、Math工具类

Arrays工具类

> 是一个与数组相关的工具类，提供静态方法实现数组的常用操作。

- `public static String toString(数组)`：将参数数组变为字符串。
- `public static void sort(数组)`：安装默认升序对数组元素排序。如果如果是字符串按照字符升序，如果是自定义类型，需要有Comparable或Comparator接口。

Math工具类

- `public static double abs(double num)`：获取绝对值。
- `public static double ceil(double num)`：向上取整。
- `public static double floor(double num)`：向下取整。
- `public static long round(double)`：四舍五入。
- `Math.PI`：π值。

## 5、数据结构

Vector集合：

- 可以实现可增长的对象数组。单线程效率较低。
- `void addElement(E obj)`：在末尾添加一个元素。
- `Enumeration<E> elements()`：返回向量的元素枚举。

HashSet集合：

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

  



