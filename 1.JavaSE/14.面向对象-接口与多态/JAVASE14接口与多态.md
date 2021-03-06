## 1、接口

> 接口，用于处理继承单一的局限性，接口里所有的方法都是抽象方法，接口和类的关系不是 继承 而是 实现 。

- 接口格式：`interface`关键字代替`class`，其他与类相同。

  ```java
  interface Animal {
      public static final int num = 10;
      public abstract void eat();
  }
  ```

- 接口的成员特点：

  1. 只能有抽象方法。
  2. 只能有常量，默认修饰符为`public static final`。
  3. 默认且只能使用`abstract和public`关键字。
  4. 接口不能实例化，不能创建对象。
  5. 类与接口的关系是实现关系，一个类实现一个接口鼻血实现他的所有方法。

- 实现格式：`implements`关键字。

  ```java
  class Cat implements Animal {
      public void eat() {
          System.out.println("Eat");
      }
  }
  ```

-  类与接口之间的关系：

  - 类与类之间：继承关系，单一继承，多层继承。
  - 类与接口：实现关系，多实现，一个类可以实现多个接口。
  - 接口与接口：继承关系，而且可以多继承。一个类要实现其接口，也要实现其接口的父类。

  ```java
  interface InterA {
  	public abstract void a();
  }
  interface InterB {
  	public abstract void b();
  }
  interface InterC extends InterA,InterB {	//多继承
  	public abstract void c();
  }
  class D implements InterC {			//实现接口的父接口
  	@Override
  	public void a() {
  	}
  	@Override
  	public void b() {	
  	}
  	@Override
  	public void c() {
  	}
  }
  ```

- 接口的优点：

  1. 多实现打破了继承的局限性。
  2. 对外提供实现的规则。
  3. 降低了程序的耦合性，模块化开发。

- 接口与抽象类的异同：

  1. 同：抽取抽象的概念，类当中的共性。
  2. 异1：类与接口是实现关系，类与抽象类是继承关系。一个是多实现，一个是单继承。
  3. 异2：成员变量：抽象类可以有成员变量，也可以有常量。接口只能有常量。
  4. 异3：成员方法：抽象类可以有抽象方法，也可以有非抽象方法。接口只能有抽象方法，而且默认修饰符`public abstract`。
  5. 异4：构造方法：抽象类有构造方法。接口没有构造方法。

- 案例：

  - 分析时，由下至上，不断抽取共性。

  - 实现时，由上至下，先实现共性。

  - 使用时，使用具体的子类。

  - 子类同时继承父类和实现接口：

    ```java
    class Demo extends A implements B {}
    ```

## 2、匿名对象和final关键字

- 匿名对象：没有变量引用的对象。
  - 可以直接调用方法，格式为`new 对象.方法;`，如 `new Student().study();`。
  - 一般在当方法只调用一次的时候使用。
  - 对匿名对象的赋值没有意义。
  - 可以当作参数进行传递。
- `final`关键字：修饰符，可以修饰类、成员方法和成员变量。
  - 修饰的类不能被继承，不能有子类。
  - 修饰的方法不能被重写。
  - 修饰的变量的值不能被修改，是常量，变量命名一般为全大写。
  - 这种常量成为自定义常量，必须初始化，分为显式初始化和构造初始化。

## 3、多态

- 多态的前提：子父类的继承关系（包括接口实现）；方法的重写和父类引用指向子类对象。

- 父类引用指向子类对象：

  ```java
  public class Demo {
      public static void main(String[] args) {
          // 父类引用指向子类对象
          Animal a = new Cat();
          // Animal a --- 父类引用
          // = --- 指向
          // new Cat() --- 子类对象
          a.eat();  // 调用的是子类的 eat()方法
      }
  } 
  class Animal {
      System.out.println("动物")；
  }
  class Cat extends Animal {
      System.out.println("猫")；
  }
  ```

- 动态绑定：在运行期间调用的方法，是根据其具体的类型。父类引用指向子类对象，使用的还是子类的方法。

- 多态成员的特点：

  1. 成员变量，编译时和运行时看的都是左边（父类中的），因为成员变量没有重写的概念。
  2. 成员方法，编译时看的是左边（父类中的），运行时看的时右边（子类中的）。
  3. 静态方法，编译时和运行时看的都是左边（父类中的），因为使用变量调用静态方法，其实相当于用变量类型的类名去调用。
  4. 编译时看的都是左边，运行时成员方法看右边，其他看左边。

- 向上转型和向下转型：

  - 是引用类型之间的转换。
  - 向上转型，由小到大（子类型转换成父类型），是自动的。
  - 向上转型：`Animal a = new Cat();`。这样的对象 a 只能调用子父类中所共有的方法，不能调用子类所特有的方法。
  - 向下转型，由大到小（父类型转换成子类型），是强制的。
  - 向下转型：`Cat c = (Cat) a;`。这样是为了可以调用子类中的特有方法，同时注意转型到别的子类型会出错。

- 多态的优缺点：

  - 缺点：父类引用无法直接访问子类特有的成员。

  - 优点1：提高可维护性（继承）。

  - 优点2：提高可扩展性，可以从父类型的层面传入参数对象。

    ```java
    class Factory {
        public void createPhone(Phone p) {
            p.call();
        }
    }
    interface Phone {
        public abstract void call();
    }
    class A implements Phone {
        public void call() {
            System.out.println("A");
        }
    }
    class B implements Phone {
          public void call() {
            System.out.println("B");
        }
    }
    ```

    

