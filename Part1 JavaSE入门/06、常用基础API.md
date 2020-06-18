## 1、API

> 应用程序编程接口

- API文档使用：
  1. 搜索：API文档 > 显示 > 索引。
  2. 看该API属于哪个包，java.lang 下的类使用不需要导包。
  3. 看类的表述。
  4. 看构造方法。
  5. 看成员方法。
  6. 调用方法：
     - 看返回值类型。
     - 看方法名。
     - 看形式参数。

## 2、常用API

- 键盘录入字符串数据：

  - Scanner 类用于获取键盘输出的数据，包括基本数据类型和字符串数据。
  - 录入字符串使用方法 `public String nextLine();`
  - 调用方式 `Scanner sc = new Scanner(System.in);` `String s = sc.nextLine();`

## 3、Random类

> 随机数类，用于产生一个随机数

- 使用步骤

```Java
//导包
import java.util.Random;
//创建对象
Random r = new Random();
//获取随机数
int number = r.nextInt(10);
//获取数据的范围为：[0,10),包括0不包括10
```

- 如何获取1-100之间的随机数:`int i = r.nextInt(100)+1;`

- 猜数字

```Java
import java.util.Random;
import java.util.Scanner;
public class Demo {
	public static void main(String[] args) {
		Random r = new Random();
		int number = r.nextInt(100)+1;
		Scanner sc = new Scanner(System.in);
		int guess = sc.nextInt();
		while(true){
			if(guess>number){
				System.out.println("da");
			}else if(guess<number){
				System.out.println("xiao");
			}else{
				System.out.println("right");
				break;
			}
			guess = sc.nextInt();
		}
	}
}
```

## 4、String 类：

  - java.lang包下的类，不需要导包。

  - 字符串本质是字符数组。

  - 构造方法：

    - `String(String original); `把字符串数据封装成字符串对象。
    - `String(char[] value);`把字符数组的数据封装成字符串对象。
    - `String(char[] value, int index, int count);`把字符数组中的一部分数据封装成字符串对象

    ```Java
    //方法1：String(String original)
    String s1 = new String("hello");
    //方式2：String(char[] value)
    char[] chs = {'h','e','l','l','o'};
    String s2 = new String(chs);
    //方式3：String(char[] value, int index, int count)
    String s3 = new String(chs,0,chs.length);
    //方式4：直接赋值
    String s4 = "hello"；
    ```

  - `==`号用于引用数据类型，比较的是地址值是否相同。

  - 字符串的内容是存储在方法区的常量池里的，是为了方便字符串的重复使用。

  - 构造方法与直接赋值的区别：

    - 两种方式生成的对象用`==`为 false。
    - 两个用直接赋值生成的内容相同的对象用`==`为 true。
    - 构造方法是栈中的引用先指向堆内的对象，堆内的对象再指向方法区中的字符串。
    - 直接赋值是栈中的引用直接指向方法区中的字符串，因为指向的同一个内容的字符串的地址，所以直接赋值生成的多个对象是相等的。
    - 构造方法创建字符串对象是在堆内存，直接赋值是在方法区的常量池。

  - 直接输出字符串对象，输出的是该对象中的数据。

  - Object是类层次结构中的根类，所有类型都直接或间接的继承自该子类。如果一个方法的形参是Object，那么可以传递他的任意子类对象。

  - 成员方法1——判断功能：

    1. `boolean equals(Object);`比较字符串的内容是否相同。
    2. `boolean equalsIgnoreCase(String str);`比较字符串内容是否相同，忽略大小写。
    3. `boolean startsWith(String str);`判断字符串是否以指定str开头。
    4. `boolean endsWith(String str);`判断字符串是否以指定str结尾。

  - 成员方法2——获取功能：

    1. `int length();`获取字符串长度。
    2. `char charAt(int index);`获取指定索引处的字符。
    3. `int indexOf(String str);`获取str在字符串中第一次出现的索引，返回 -1 时表示没找到。
    4. `String substring(int start);`从start开始截取字符串。
    5. `String substring(int start,int end);`从start开始到end介绍截取字符串，截取的包括start不包括end索引。

  - 成员方法3——转换功能：

    1. `char[] toCharArray();`把字符串转换为字符数组。
    2. `String toLowerCase();`把字符串转换为小写字符串。
    3. `String toUpperCase();`把字符串转换为大写字符串。

  - 字符数组获取长度是属性 `.length`，字符串对象获取长度是方法`.length()`。

  - 成员方法4——其他功能：

    1. `String trim();`去除字符串两端的空格。
    2. `String[] split(String str);`按照指定符号str分割字符串，返回的是字符串数组`String[]`。

  - 字符串拼接过程`s1+=s2`中，常量池中先存储了s1和s2，然后拼接产生s3，变量s1指向s3，原来的s1和s2存储的内容都被当作垃圾等待回收。

## 5、StringBuilder 类：

- 一个可变的字符序列，字符串缓冲区类，可以直接在字符串后边拼接。
- 与String的区别：String内容固定，而StringBuilder内容可变。
- 构造方法：
  - 创建对象：`StringBuilder sb = new StringBuilder();`
- 成员方法：
  - `public int capacity();`返回当前容量。
  - `public int length();`返回长度。
  - `public StringBuilder append(任意类型)；`添加功能。
    - 添加数据并返回自身对象，只需`sb.append("hello");`。
    - 链式：`sb.append("hhh").append("eee");`
  - `public StringBuilder reverse();`反转功能。
- StringBuilder转String：`String s = sb.toString();`
- String转StringBuilder：`StringBuilder sb = new StringBuilder(s)`;