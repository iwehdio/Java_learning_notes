## 1、回顾

- 数据类型：
  - 基本数据类型。
  - 引用数据类型。
- 类型转换：
  - 自动类型转换。
  - 强制类型转换。
- 运算符：算数 / 比较 / 赋值 / 逻辑。
- 方法：提高了代码的复用性和可维护性。
  - 定义格式。
  - 调用方式。
  - 方法重载。
  - `show()`方法输出显示所有成员变量的值。
- 数组：存储多个元素的容器。
  - 元素类型必须一致，可以存储基本或引用数据类型。
  - 定义好长度后无法改变。
  - 动态初始化 / 静态初始化。



## 2、static静态关键字

- 既可以修饰成员方法，也可以修饰成员变量。
- static 修饰的成员变量和成员方法，被所有的对象所共享，也就是对其中一个对象进行赋值时，实际上时对所有对象的这个成员变量或成员方法都进行了相同的赋值。
- 由于上面的特性，static 修饰的成员变量或成员方法可以使用类名直接调用。如`类名.静态变量名();`。
- 静态的加载优先于对象，是随着类的加载而加载。
- 注意事项：
  - 即使是在没有创建对象的情况下，静态方法也可以调用静态的成员变量，静态方法也可以调用静态方法。
  - 静态方法不可以调用非静态的成员变量和方法。
  - 非静态方法可以调用静态或非静态的成员变量和方法。
  - 静态的方法中没有 `this`这个对象。
- 静态的优缺点：
  - 优点：对对象的共享数据提供单独空间的存储，节省空间；可以制件被类名调用，不用在堆内存创建对象。
  - 缺点：访问出现局限性。

- 工具类：
  - 构造方法私有，不需要也无法创建对象。例：Math 类。
  - 工具类中大多数为静态方法。
  - 如果全是静态方法就不需要创建对象。
- Math 类
  - 返回绝对值：`Math.abs(double a);`。
  - 向上取整：`Math.ceil(double a);`。
  - 向下取整：`Math.floor(double a);`。
  - 四舍五入：`Math.round(double a);`。
  - a 的 b 次幂：`Math.pow(double a, double b);`。
  - 返回一个大于0小于1的随机数：`Math.random();`。

## 3、代码块

- 代码块：分为 局部 / 构造 / 静态 代码块。

- 局部代码块：在方法中。

  - 作用：控制变量的生命周期（作用域），代码块外无法使用代码块内的变量。
  - 在方法中，一对`{ }`。

- 构造代码块：构造方法的重复内容。

  - 作用：提取构造方法中的共性，每次创建对象都会执行，并且在构造方法执行之前执行。
  - 类中，在构造方法外，与构造方法同级的一对`{ }`。

- 静态代码块：在加载类时需要做的初始化操作，比较加载驱动。

  - 作用：随着类的加载而加载，而且只加载一次。
  - 类中，在构造方法外，与构造方法同级的`static { }`。

- 例题：

  - 以下的输出顺序：

  ```java
  public class BlockTest{
      public static void main(String[] args){
          Coder c = new Coder;
      }
  }
  class Coder{
      static {
          System.out.println("Coder静态代码块执行");
      }
      
      {
          System.out.println("Coder构造代码块执行");
      }
      
      public Coder(){
          System.out.println("Coder无参空构造执行");
      }
  }
  ```

  1. 先是`Coder c`载入类，静态代码块先执行。
  2. 然后是`new Coder`创建对象，先执行共性的构造代码块，再执行构造方法。

- 以下的输出顺序：

  ```java
  public class BlockTest{
      static {
          System.out.println("BlockTest静态代码块执行");
      }
      
      {
          System.out.println("BlockTest构造代码块执行");
      }
      
      public BlockTest(){
          System.out.println("BlockTest无参空构造执行");
      }
      public static void main(String[] args){
          System.out.println("BlockTest主函数执行");
          Coder c1 = new Coder;
          Coder c2 = new Coder;
      }
  }
  class Coder{
      static {
          System.out.println("Coder静态代码块执行");
      }
      
      {
          System.out.println("Coder构造代码块执行");
      }
      
      public Coder(){
          System.out.println("Coder无参空构造执行");
      }
  }
  ```

  1. 先是载入BlockTest类，BlockTest静态代码块执行。
  2. 由于没有创建BlockTest对象，因此构造代码块和构造方法都不执行。
  3. BlockTest主函数执行。
  4. 再是`Coder c1`载入类，Coder静态代码块先执行。
  5. 然后是`new Coder`创建对象，先执行共性的Coder构造代码块，再执行Coder构造方法。
  6. 第二次`Coder c2`载入类时，Coder静态代码块不再执行。
  7. 然后是`new Coder`创建对象，先执行共性的Coder构造代码块，再执行Coder构造方法。