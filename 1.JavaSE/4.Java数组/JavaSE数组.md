## 1、Random

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

## 2、数组

> 存储多个变量（元素）的容器

- 多个变量的数据类型要一致
- 定义类型

```Java
//格式1：数据类型[] 数组名；
int[] arr;
//格式2：数据类型 数组名[] ；
int arr[]；
```

- 数组初始化，为数组分配内存空间，并为数组中的每个元素赋初值
  - 动态初始化：只给出长度，由系统给出初始化值
  - 静态初始化：给出初始化值，由系统决定长度

```Java
//方法1：动态初始化
//数据类型[] 数组名 = new int[数组长度]；
int[] arr1 = new int[length];    //length为数组长度，元素个数

//方法2：静态初始化
//数据类型[] 数组名 = new 数据类型[]{元素1，元素2，...}；
//简化写法：数据类型[] 数组名 = {元素1，元素2，...}；
int[] arr2 = new int[]{1, 2, 3};
int[] arr3 = {1, 2, 3};
```

- 数组元素访问
  - 直接输出的是地址值
  - 数组中元素的索引从0到数组长度-1
  - 访问格式 `数组名[索引]`
  - 获取数组长度格式，数据名.length，`int len = arr.length;`
- 数组索引越界，访问了不存在的索引
- 数组是一种引用类型（还包括类和接口等），变量可以赋值给引用类型
- 空指针异常，数组已经不再指向堆内存的数据，但还用数组名去访问元素（比如访问前有`arr = null;`，arr不再指向原来的数组地址）

- 二维数组：元素是一维数组的数组

  * 定义格式

    ```java
    //定义格式
    //方法1：数据类型[][] 数组名；
    //方法2：数据类型 数组名 [][];
    //方法3：数据类型[] 数组名[];
    
    //动态初始化
    //数据类型[][] 数组名 = new 数据类型[m][n];
    //m是二维数组中一维数组的个数，n是一维数组中的元素个数
    
    //静态初始化
    //数据类型[][] 数组名 = new 数据类型[][]{{元素...}，{元素...},...}；
    //数据类型[][] 数组名 = {{元素...}，{元素...},...}；
    
    int[][] arr = {{1,2,3},{4,5,6},{7,8,9}};
    
    //索引格式（第p个数组中的第q个元素）：数组名[p][q]；
    System.out.println(arr[2][2]);
    ```

  * 遍历

    ```Java
    //二维数组.length；获取的是二维数组中有几个二维数组
    for(int i=0; i<arr.length;i++){
        for(int j=0;j<arr[i].length;j++){
        	System.out.println(arr[i][j]);
    	}
    }
    ```

  * 输出在同一行用`System.out.print();`

  * ln就代表换行，只有`System.out.println();`就是输出一个换行

## 3、Java内存分配

- 栈（存储局部变量）
  - 局部变量就是定义在方法中的变量
  - 使用完毕立即被回收
- 堆（存储new出来的东西即对象）
  - 每个对象都有地址值
  - 每个对象的数据都有默认值
    - byte, short, int ,long > 0
    - float, double > 0.0
    - char > '\u0000'
    - boolean > false
    - 引用类型 > null
  - 使用完毕后，会在垃圾回收器空闲的时候被回收
- 方法区（面向对象）
- 本地方法区（与系统相关）
- 寄存器（给CPU使用）

- 一个数组的内存表示 `int[] arr = new int[3];`
  - 栈中存储的是变量 `int[] arr`
  - 堆中存储的是对象 `new int[3]`，内存地址是001，堆中根据索引 0、1、2 在该内存地址下分配给数组的三个元素三块内存
  - 栈存储的变量通过指向内存地址001找到数组对象`new int[3]`，再通过索引找到数组下的元素

- 两个数组指向同一个地址
  - 把第二个数组指向第一个数组的地址`int[] arr2 = arr;`
  - 更改第二个数组的值`arr[0] = 1;`
  - 两个数组指向的是堆中的同一个数组，指向同一个地址，通过任何一个数组赋值都会改变内存中的值，从而同时改变两个数组的值