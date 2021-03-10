## 1、异常处理

> 异常：代码在编译或运行时出现的错误。

- 异常包含了错误的类型、原因和位置。

- 异常的体系结构：

  - Throwable() ，所有异常的顶层。
    - Error ：出现的不能处理的严重问题。
    - Exception ：可以处理的问题。

- 异常的处理方式：

  - 不处理异常：JVM帮助处理，显示异常的类型、原因和位置，并且终止程序。
  - 具体处理异常的手段：捕获处理和抛出处理。

- 捕获处理：

  - 根据已知可能出现的异常类型， 出入异常对象。

  - try ... catch 语句

    ```java
    try {
        有可能出问题的代码；
    } catch(传入的异常对象) ｛
        处理异常；
    ｝
    ```

  - 执行顺序：先执行 try 中的语句；如果发生异常，异常下边的代码不再执行；跳入 catch 中的语句；如果没有发生异常，只执行 try 中的语句，不执行 catch 中的语句。

  - Eclipse辅助编写：右键 > Surround with。

- 抛出处理：

  - 不想或没有能力处理异常时，此时谁调用方法，谁处理异常。

  - 使用关键字 throws 关键字在方法的声明中抛出异常。

    ```java
    public void function() throws Exception {}
    ```

  - 从主方法中抛出异常，是由 JVM 虚拟机处理。

- 如何处理多个异常：

  - 可以使用多个 try ... catch 语句。

  - 可以使用一个 try 和多个 catch 语句。

    ```java
    try {
        有可能出问题的代码1；
        有可能出问题的代码2
    } catch(传入的异常对象1)｛
        处理异常1；
    } catch(传入的异常对象2) ｛
        处理异常2；
    } catch(Exception e) {
        处理通用异常；
    }
    ```

  - 多个 catch之间的顺序：

    - 多个 catch 中传入的异常对象可能有子父类关系。
    - 同级的异常对象没有顺序关系。
    - 父类异常对象一定在子类的后面。

- Throwable 类：

  - `String getMessage()`：出现异常的原因。
  - `String toString()`：出现异常的类型和原因。
  - `void printStackTrace()`：出现异常的类型、原因和位置。

- finally 关键字：

  - 组合 try ... catch 语句，在最后的位置。用于释放资源等收尾工作。

  - 无论 try ... catch 语句如何执行，finally 语句一定执行。

    ```java
    try {
        有可能出问题的代码；
    } catch(传入的异常对象) ｛
        处理异常；
    } finally {
        收尾工作；
    }
    ```

  - IO流异常处理，例：

    ```java
    FileWriter fw = null;			// 在外部声明并初始化，防止
    try {
        System.out.println(2 / 0);		//异常1
        fw = new FileWriter("A.txt");
        fw.writer("A");
        System.out.println(2 / 0);		//异常2
        fw.writer("B");
    } catch(IOException e) {
        e.printStackTrace();
    } finally {					//无论是否发生异常都要close()
        try {
            if(fw != null){		//如果发生异常1，不能对空引用调用方法
                fw.close();
            }
        } catch(IOException e) {
        	e.printStackTrace();
        }
    }
    ```

- 异常的分类：

  - 运行时期异常：RuntimeException 的子类，在编译时期可以选择处理或不处理。
  - 编译时期异常：是Exception的子类，且不是 RuntimeException 的子类在编译时期必须处理。

- throw 关键字：制造异常的方式，并且结束方法，可用于自定义异常。

  - 要求输入在 0~100，例：

    ```java
    public static void checkScore(int score) {
        if(score < 0 || score > 100){
            throw new RuntimeException("不在0~100之间")；
        }
        System.out.println("在0~100之间");	
    }
    ```

  - 自定义异常类：实现创建一个类并实现多个构造方法，（如果是编译时异常，RuntimeException 改为 Exception）。

    ```java
    public class MyException extends RuntimeException{
        public MyException() {
            super();
        }
        public MyException(String s) {
            super(s);
        }
    }
    ```

  - 如果抛出（throw）的是编译时期异常，必须在方法声明处抛出（throws）。

## 2、递归

> 将大问题拆成小问题，然后把小问题拆成可以解决的更小的问题，最终解决大问题

- 递归的注意事项：

  - 一定要有出口（最小的问题可以解决）。
  - 递归次数不宜过多，防止内存溢出。

- 求阶乘，例：

  ```java
  public static int JC(int n) {
      if(n == 1) {
          return 1;
      } else {
          return n * JC(n - 1);
      }
  }
  ```

- 斐波那契数列，例

  ```java
  public static int Fib(int n) {
      if(n == 1 || n == 2) {
          return 1;
      } else {
          return Fib(n - 1) + Fib(n - 2); 
      }
  }
  ```


## 3、函数式接口

- 函数式接口：有且只有一个抽象方法的接口。适用于Lamda表达式。
- 在JDK8以上，接口中可以添加静态方法或非抽象的实例方法。
  - 在实例方法的申明中，需要增加default关键字修饰，因此这种方法也称为默认方法。
  - 非抽象的实例方法是接口自带的方法。接口被实现后，实例可以直接使用这些默认方法，同时如果对默认方法需要重写时，可以直接重写即可。
- 在接口上方添加一个 `@FunctionalInterface`注解，可以检测是否是一个函数式接口。如果不是函数式接口，则会编译失败。
- Lamda 具有延迟加载的特点。
  - 使用 Lamda 表达式作为参数传递，仅仅是把参数传递到方法中。
  - 只有在方法中调用 Lamda 表达式表达的参数时，才会执行 Lamda 表达式中的代码。
  - 如果有条件判断，导致 Lamda 表达式不执行，这样就对性能进行了优化。

## 4、常用函数式接口

- Supplier 接口：

  - 被称为生产型接口，指定的泛型是什么，就返回什么类型的数据。

  - 包含一个无参方法：`T get()`。

  - 示例：

    ```java
    public static int method(Supplier<Integer> sup) {
        return sup.get;
    }
    
    public static void main(String[] args) {
        int nun = method(() -> {
            return 2020;
        });
        System.out.println(num);
    }
    ```

    

- Consumer 接口：

  - 被称为消费型接口，指定的泛型是什么，就使用什么类型的数据。

  - 包含一个方法：`void accept(T t)`。

  - 默认方法：`con1.andThen(con2)`，可以将两个 Consumer 接口组合到一起，再对数据进行消费。

  - 示例：

    ```java
    Consumer<String> con1;
    Consumer<String> con2;
    String s = "qwer";
    //谁在前先消费谁（谁调用的方法先消费谁）
    con1.andThen(con2).accept(s);
    //等价于:
    con1.accept(s);
    con2.accept(s);
    ```

- Predicate 接口：

  - 对某种类型的数据进行判断，从而得到一个 boolean 返回值。
  - 包含一个方法：`boolean test(T t)`。
  - 默认方法1：`pre1.and(pre2)`，表示与的关系，连接判断条件。是通过逻辑与（&&）实现的。
  - 默认方法2：`pre1.or(pre2)`，表示或的关系，连接判断条件。是通过逻辑或（| |）实现的。
  - 默认方法3：`pre.negat()`，表示非的关系。是通过逻辑非（！）实现的。

- Function 接口：

  - 被称为转换型接口，根据一个类型的数据，得到另一个类型的数据。前者为前置条件，后者为后置条件。也可以用于同种数据类型中，既需要参数也需要返回值的情形。

  - 有两个泛型：`Function<T, R>`，从 T 转换为 R 类型。

  - 包含一个方法：`R apply(T t)`。

  - 默认方法：`fun1.andThen(fun2)`，将两个Function 接口进行组合，即先进行一次转换，再将转换后的结果再进行一次转换。

    



## 5、Lamda表达式

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

