## 1、Java介绍

Java SE	标准版，普通桌面

Java EE	企业版，Web应用

- 跨平台

  原理：Java虚拟机 JVM

  平台：指操作系统

- JRE

  全称Java Runtime Environment ，java运行时环境。它是运行java程序的必须条件。

  包括Java虚拟机JVM和核心类库。

- JDK

  全称JavaSE Development Kit，java应用程序的开发包，它提供了编译、运行java程序所需的各种工具和资源，包括java编译器、java运行时环境，以及常用的java类库等。

  JDK开发的程序由JRE运行。

Dos命令

- 打开控制台： Win+R      cmd
- 盘符切换  d：
- 显示所有文件和文件夹  dir
- 进入某个目录  cd + 目录名称
- 回退目录  cd..
- 多级进入，一次进入两个目录  cd 目录1 \\ \  目录2
- 多级回退 cd\\
- 清屏  cls

## 2、HelloWorld

.java源文件 >  编译器 >  字节码  >  解释器

1. 首先定义一个类，类是java最基本的单位，类名要求与文件名一致。
2. 定义一个主方法，是程序的入口方法，程序的执行从主方法开始。
3. System.out.println()  输出。
4. 命令行下进入JDK的bin目录，并将所要运行的文件复制到这个目录下。
5. 命令行下输入`javac Hello.java`编译源文件。
6. 如果没有错误，目录下会出现`Hello.class`字节码文件。
7. 命令行下输入`java Hello`解释字节码（不需要输入后缀 .class）

```java
public class Hello {
	public static void main (String [] args) {
		System.out.println("Hello");
	}
} 
```

注意分号和print后是小写 L 不是大写 i 。

## 3、环境变量

为了在除了JDK/bin目录下，都可以运行 java 程序（javac与java命令）。

系统变量中添加 `JAVA_HOME`值为JDK的目录

path系统变量中添加 `%JAVA_HOME%\bin;`

## 4、注释

单行注释	`//`

多行注释	`/*	*/`

## 5、关键字

> 被Java赋予特定含义的单词

- 全部小写
- 颜色标记

## 6、常量

> 程序运行中不可改变

- 字符串常量	""
- 整数常量
- 字符常量      ''
- 布尔常量       true / false
- 空常量         null

## 7、变量

> 内存中的一小块区域，执行过程中值可以在一定范围内发生改变

组成与定义格式：

- 对区域的限定（数据类型）。
- 区域名称（变量名）。
- 区域内容（初始化值）。

## 8、存储单元

* 最小信息单元	位  bit    -    b
* 最小存储单元    字节  Byte     -     B

## 9、数据类型

> Java是强类型语言，对每种数据都有明确的数据类型

数据类型分类：

- 基本数据类型
- 引用数据类型（类、接口、数组）

基本数据类型：

1. 整数
   - byte	
   - short
   - int
   - long
2. 浮点数
   - float
   - double
3. 字符
   - char
4. 布尔
   - boolean

![image-20200112211000013](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20200112211000013.png)

- 整数默认  int，浮点数默认  double 。
- 因此，为了不被提示损失，定义时在 long 定义的数字后加 L ，在  float 定义的数字后加F 。

## 10、标识符

> 用来给包、类、方法、变量起名的符号

组成规则：

- unicode ：数字、英文大小写、汉字（不建议）。
- 下划线  _
- 美元符号  $

注意事项：

- 不能以数字开头，比如不能用  `int 2a = 1`;  。
- 不能是Java中的关键字。

命名规则：

- 见名知意
- 包（对类进行管理，相似类的集合）
  - 全部小写
  - 多级包用 . 隔开
- 类
  - 一个单词则首字母大写
  - 多个单词则每个单词的首字母大写
- 方法和变量
  - 一个单词首字母小写
  - 多个单词从第二个单词开始首字母大写

## 11、变量定义

byte类型

- `byte b = 10;`

short类型

- `short s = 100;`

int类型

- `int i = 1000`;

long类型

- `long l = 1000000000L`
- 注意最后最好直接加   L   。

float类型

- `float f = 12.32F;`
- 注意float最好最好直接加    F   。

double类型

- `double d = 13.33;`

char类型

- `char c = 'A';`

boolean类型

- `boolean b = true;`

变量定义的注意事项：

- 变量未赋值不能直接使用，不能直接使用 `int b;`，需要在使用前赋值。
- 变量只在所属的范围，所属的大括号（｛｝），在括号外无效。
- 一行可以定义多个变量（不建议），如`int a,b;`（不能直接赋值，需要之后分别赋值）。

## 12、类型转换

> 进行运算时，一般要求参与运算的数据类型一致

- 不同数据类型运算产生的结果，

```java
byte bb = 2;
int cc = 5;

// byte接收byte与int的和，提示可能出现精度损失（如果结果在byte可表达的范围内，实际上强制转换后不会损失）
byte dd = bb + cc；

// 应该用参与运算的字节数最大的数据类型的接收
int ee = bb + cc;
```

类型转换：

- 隐式转换。
  - byte, short, char默认转换为int参与运算，最小转换为4个字节。
  - 然后按   int  -   long   -    float    -    double   , 的顺序，按参与运算最大的数据类型接收。
- 强制转换。
  - 目标类型  变量名 = （目标类型）（被转换的数据）。
  - 比如`byte d = (byte)(a + b)`。
  - 不建议随意转换。
- boolean类型不能转换为其他数据类型。





