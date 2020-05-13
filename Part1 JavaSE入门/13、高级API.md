## 1.、Object类

> 是类层次结构的根类，每个类都使用Object作为超类

- 任何类都默认继承了Object类。
- `getClass()`返回一个字节码对象。
- `String toString()`方法：
  - 返回对象的字符串表示。
  - 输出一个对象就是默认输出这个对象的 toString() 方法。
  - 输出格式： 包名 . 类名 @ 对象的十六进制内存地址。
  - 一般要重写更有意义的 toString() 方法。
  - Eclipse快速生成 toString() 方法：右键 > Source > Generate toString()。
- `boolean equals(Object obj)`方法：
  - 使用 == 来比较两个对象是否相等。基本类型比较值，引用类型比较地址。
  - 一般要重写更有意义的 equals() 方法。比如字符串类重写的 equals 方法。
  - 通过比较传入参数是否与对象是同一个类下的对象`if(this.getClass()==o.getClass())`。
  - Eclipse快速生成 equals() 方法：右键 > Source > Generate equals() 。

## 2、System类

> 包含一些有用的类字段（静态修饰的成员变量）和方法，不能被实例化

- `static void arraycopy(Object src,int srcPos,Object dest,int desPos,int length)`：复制数组。传入参数（源数组，源数组起始索引位置，目标数组，目标数组的起始索引位置，复制的数组长度）。
- `static long currentTimeMillis()`：以毫秒值返回当前系统时间。基于1970-1-1。
- `static void exit(int status)`：终止正在运行的Java虚拟机。一般0表示正常终止，非0表示异常终止。
- `static void gc()`：运行垃圾回收器。

## 3、Date类

> 表示特定的瞬间，精确到毫秒。可以通过方法设定系统表示的时间。

- 构造方法：
  - `Data()`：创建一个表示当前系统时间的Date对象。
  - `Date(long dat)`：根据指定时间（毫秒值）创建Date对象。
- 用构造方法实例化对象，然后直接输出。
- `long getTime()`：获取毫秒值时间。
- `void setTime(long time)`：按毫秒值设置时间。

## 4、DateFormat类

> 格式化日期，是一个抽象类。有子类具体类SimpleDateFormat。

- 构造方法：
  - `SimpleDateFormat()`：默认模式。
  - `SimpleDateFormat(String pattern)`：指定模式。

- `String format(Date data)`：格式化

- `Date parse(String source)`：解析。解析的格式要与构造的格式一致。

- 例：

  ```java
  SimpleDateFormat sdf = new SimpleDateFormat();
  /*一种指定格式
  SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
  */
  Date d = new Date();
  String s = sdf.format(d);
  System.out.println(s);
  ```

## 5、Calender类

> 是一个抽象类，转换或操作日历对象（年月日时）。

- `static Calender getInstance()`：获得日历。

- `int get(int field)`：返回给定日历字段的值。

  ```java
  Calendar c = Calender.getInstance();
  int year = c.get(Calender.TEAR);
  int month = c.get(Calender.MONTH) + 1;    //月份从0开始
  ```

- `void set(int field,int value)`：把指定字段修改成指定的值。

- `void add(int field,int amount)`：在指定字段上加指定的值。

## 6、Arrays、Math工具类

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

## 7、包装类

> 封装了基本数据类型的类，提供了更多复杂方法和变量

- 基本数据类型对应的包装类：

  | 基本数据类型 |  包装类   |
  | :----------: | :-------: |
  |     byte     |   Byte    |
  |    short     |   Short   |
  |     char     | Character |
  |     int      |  Integer  |
  |     long     |   Long    |
  |    float     |   Float   |
  |    double    |  Double   |
  |   boolean    |  Boolean  |

- Integer类：

  - 构造方法：
    - `Integer(int value)`
    - `Integer(String s)`
  - `int intValue()`：返回 int 类型的Integer对象。
  - `static int parseInt(String s)`：String 转 int，无需创建对象（静态）。
  - `String toString()`：返回 String 类型的Integer对象。
  - `static String toString(int i)`：int 转 String，无需创建对象（静态）。

- 装箱：基本数据类型转化为包装类；拆箱：包装类转化为基本护甲类型。

- 自动装箱：`Integer i = value`。

  - 例`integer i = 10;`相当于`Integer i = new Integer(10);`。

- 自动拆箱：`int a = i;`

  + 例`integer i = 10;` `int a = i;`。相当于`int a = i.intValue();`。

- 自动装箱与拆箱，例：

  - 解决原本引用数据类型不能直接相加：

    ```java
    Integer i1 = 10;
    Integer i2 = 20;
    Integer i3 = i1 + i2;
    ```

  - 解决 ArrayList 集合中的元素必须为对象：

    ```java
    ArrayList list = new ArrayList();
    //自动装箱，相当于list.add(new Integer(1));
    list.add(1);    
    ```

## 8、正则表达式

> 一套用于匹配字符串的规则

- `boolean matches(String regex)`：判断当前字符串是否匹配指定的正则表达式（String类）。

-  常用正则表达式：

  | 正则表达式 |           含义           |
  | :--------: | :----------------------: |
  |     x      |          字符x           |
  |     \\     |        反斜线字符        |
  |   [abc]    |         a或b或c          |
  |   [^abc]   |   任何字符除了a或b或c    |
  |  [a-zA-Z]  | a到z或A到Z（两端都包括） |
  |    x？     |     一次或一次也没有     |
  |     x*     |        零次或多次        |
  |     x+     |        一次或多次        |
  |    x{n}    |         恰好n次          |
  |   x{n,}    |         至少n次          |
  |   x{n,m}   |     至少n次不超过m次     |

- 例：匹配5~15位的数字，第一个数字不能为0

  ```java
  String s = "123456789";
  boolean flag = s.mathcs("[1-9][0-9]{4,14}");
  ```

  

