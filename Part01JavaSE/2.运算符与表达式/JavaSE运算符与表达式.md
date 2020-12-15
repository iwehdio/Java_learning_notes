## 1、Eclipse

创建过程：

1. 创建项目。File > New > project > Java project > project name
2. 创建包。 File > New > package > package name
3. 创建类。File > New > class > class name

内容辅助键：

1. main方法：main alt+/ + 回车
2. 输出语句：syso alt+/ + 回车

快捷键：

1. 单行注释：crtl +7
2. 多行注释：ctrl + shift + /
3. 取消多行注释：ctrl + shift + \
4. 格式化：ctrl + shift + F



## 2、IDEA

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

## 3、运算符

> 用于对常量和变量进行操作的符号

表达式

> 用运算符连接的符合语法的式子

运算符分类：

- 算数运算符
  - \+ \- \* /
  - 整数运算只能得到整数，有浮点数参与运算才能得到小数
  - % 取余 ， / 取商
  - 字符参与加法取ASCII码    ‘A’-65，‘a'-97，’0‘-48
  - 字符串参与加法运算是拼接，运算从左往右进行，`a+b+“hello”`得到的是 30hello（a=20，b=30）
  - ++ ， --  自增自减，可以放在变量的前后，a++的值是a，++a的值是a+1
- 赋值运算符
  - 基本赋值运算符：=
  - 扩展赋值运算符：+=，-=，*=，/=，   `a+=10`等价于`a=a+10`
  - 扩展赋值运算符隐含了强制类型转换。
- 关系运算符
  - ==，！=，>=,<=,<,>      判断大小关系，不能连续表示
  - 结果为boolean类型
- 逻辑运算符
  - 用于连接关系表达式
  - &与，|或，^异或，！非
  - &&和||与&和|结果一样，但有短路效果
  - &&在第一个是false时，第二个表达式不执行
  - ||在第一个是true时，第二个表达式不执行
  - &和|无论表达式结果如何，两个表达式都执行
- 三元运算符
  - 关系表达式？表达式1：表达式2
  - 计算关系表达式的值，true则结果为表达式1，为false则结果为表达式2
  - 如`int c =（a>b）? a : b；`

## 4、键盘录入

> 使用JDK提供的类Scanner

1. 导包。`import java.util.Scanner;`

   在一个类中顺序：package > import > class

2.  创建键盘录入对象。`Scanner sc = new Scanner(System.in);`

3. 接收数据。`int i = sc.nextInt();`

