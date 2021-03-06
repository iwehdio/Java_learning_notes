## 1、面向对象

> 封装步骤和功能

- 类：是一组相关的属性和行为的集合

- 对象：事物的具体体现

- 类

  - 成员变量，表达属性。
  - 成员方法，表征行为。
  - 以学生为例，属性包括姓名、年龄，行为包括学习、吃饭。

- 成员变量：在类中，方法外定义，不需要给初始化值。

- 成员方法：没有 static 关键字。

- 类的定义，例：

  Student是一个学生事物描述类，main方法不适合放在它里面。
  
  ```java
  public class Student {
      
      //成员变量
      int age;
      String name;
      
      //成员方法
      public void study() {
          System.out.println("study");
      }
}
  ```
  
- 使用一个类，其实就是使用该类的对象。

- 没有被关键字修饰的类，只能在其所在的包内使用。

- 使用成员变量和成员方法，首先需要创建对象。

  格式：`类名 对象名 = new 类名();

- 访问成员：

  - 成员变量：`对象名.变量名；`
  - 成员方法：`对象名.方法名(...)；`

- 使用类，例：

  ```java
  public class StudentDemo {
      ppublic static void main(String[] args) {
          //创建对象
          Student s = new Student();
          //字符串String是引用类型，默认为null
          System.out.println(s.name);
          s.study();
          //赋值
          s.name = "WX";
      }
  }
  ```

- 成员变量与局部变量的区别：

  - 类中的位置不同，成员变量在类中方法外，局部变量在方法中或方法声明上。
  - 内存中的位置不同，成员变量在堆内存，局部变量在栈内存。
  - 声明周期不同，成员变量随对象存在，局部变量随方法存在。
  - 初始化值不同，成员变量有默认值，局部变量没有。

- private 关键字：

  - 通过对象直接访问成员变量，会存在安全问题，让外界对象不直接访问成员变量。

  - 是一个权限修饰符，可以修饰成员变量和方法。

  - 被 private 修饰的成员只能在本类中访问。

  - 对 被 private 修饰的成员变量，需要编写getXxx()和setXxx()方法进行取值和赋值:

    ```Java
    public class Student {
        private int age;
        
        public void setAge(int a) {
            if(a<0 || a>200) {
                System.out.println("有误");
            }else {
                age = a;
            }
        }
        public int getAge() {
            return age;
        }
    }
    
    public class StudentDemo {
        Student s = new Student();
        s.setAge = 28;
        System.out.println(s.getAge);
    }
    ```

    

- 封装：变量隐藏在对象内部，外界无法直接操作好修改。

  - 将不需要对外提供的内容隐层起来。
  - 把属性隐层，提供公共方法对其访问。
  - 提高了安全性好复用性。

- this 关键字：

  - 局部变量与成员变量同名，则方法认为使用的是局部变量（就近原则）。

  - 代表所在类的对象的引用。

  - 方法被那个对象调用，this就代表那个对象。

  - 解决局部变量隐藏成员变量的问题。

  - 例：

    ```java
    public class Student {
        private int age;
        public void setAge(int age) {
            this.age = age;
        }
    }
    ```

- 类名作为形式参数传递，需要的是该类的对象，例：

  ```Java
  public class Teacher {
      public void test(Student s) {
          s.study;
      }
  }
  public class Student {
      public void study() {
          System.out.println("study");
      }
  }
  
  public class Test {
      public static void main(String[] args) {
          Teacher t = new Teacher();
          Student s = new Student();
          //传入的是Student对象
          t.test(s);
      }
  }
  ```

- 类名作为返回值类型，返回的是一个对象。例

  ```java
  public class Teacher {
      public  getStudent() {
          Student s = new Student();
          return s;
      }
  }
  public class Student {
      public void study() {
          System.out.println("study");
      }
  }
  
  public class Test {
      public static void main(String[] args) {
          Teacher t = new Teacher();
          Student s = t.getStudent();
          //返回的是Student对象
          s.study();
      }
  }
  ```

  

## 2、面向对象的内存调用

- 栈中的引用指向堆中的对象的地址，堆中的对象调用成员方法，是通过指向方法区中的成员方法的地址实现的。
- 成员变量的值存储在堆中的对象中。
- 执行成员方法时，先加载到栈内存，执行完成后从栈中消失。
- 用相同的类创建两个对象时，在堆中创建了多个对象（多个地址），但是调用成员方法时指向方法区的同一成员方法的地址。
- 可以通过已有的对象创建新的对象`Student s2 = s;`，这样两个引用指向了同一个对象的堆内存，改变一个另一个也会改变。

## 3、构造方法

- 给对象的数据进行初始化。

- 格式：

  - 方法名与类名相同。
  - 没有返回值类型，也不能写void。
  - 没有返回值。

- 调用：用 new 关键字调用，调用格式`类名对象名 = new 构造方法(...);`。

- 注意事项：

  - 如果没有给出构造方法，系统将提供一个默认的无参构造方法。但如果给出了构造方法，系统不再提供无参构造方法，推荐自己写出无参构造方法。

  - 构造方法也可以重载，比如 无参 / 有一个参数 / 有两个参数。

    ```Java
    public class Studet {
        private int age;
        private String name;
        //构造方法重载
        public Student () {}
        public Student (int age) {
            this.age = age;
        }
        public Student (int age, String name) {
            this.age = age;
            this.name = name;
        }
    }
    ```

## 4、标准类

```Java
public class Student {
    //成员变量
    private String name;
    private int age;
    //构造方法
    public Student() {}
    public Student(String name, int age) {
        this.name = name;
        this,age = age;
    }
    //成员方法
    public void setAge(int age) {
        this.age = age;
    }
    public int getAge() {
        return age;
    }
 	public void setName(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
}
```

