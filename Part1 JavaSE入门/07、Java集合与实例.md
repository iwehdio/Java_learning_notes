## 1、对象数组

- 自动生成构造方法：
  - 无参构造方法：右键 > source > Generate Constructors from Superclass.... 
  - 有参构造方法：右键 > source > Generate Constructors using Fields.... 
- 自动生成getXxx和setXxx方法：
  - 右键 > source > Generate Getters and Setters 
- 创建以对象为元素的数组：
  - 例： `Student[] students = new Student[3];`
  - 输出方式：`System.out.println(getName() + getAge());`
  - 再堆内存中先生成了空数组 Student[3] ， 然后创建了三个Student对象，最后将三个对象的地址按顺序填入空数组中。

## 2、集合

> 面向对象编程时，为了方便对多个对象进行操作。
>
> 长度可以变化的数组。

- ArrayList<E> 类
  - <E> 表示泛型。
  - 在出现E的地方可以使用引用数据类型替换。
  - `Systemo.out.println();`获取的为集合对象的内容。
- 构造方法：
  
  - 创建对象为字符串的集合：`ArrayList<String> array = new ArrayList<String>();`
- 添加方法：
  - 添加元素：`public boolean add(E e);`
  - 指定索引处添加元素：`public void add(int index,E element);`
- 获取方法：
  
  - 返回指定索引处的元素：`public E get(int index);`
- 集合长度：
  
- 集合中的元素个数：`public int size();`
  
- 删除元素：

  - 删除指定的元素：`public boolean remove(Object o);`元素不存在时返回 false。
  - 删除指定位置的元素：`public E remove(int index);`返回被删除的元素。

- 修改元素：

  - 修改指定索引处的元素，返回被删除的元素：`public E set(int index,E element);`

- 集合遍历：

  ```Java
  for(int x=0;x<arr.size();x++){
      String s= array.get(x);
      System.out.println(s);
  }
  ```

- 键盘录入数据并存储：

  ```Java
  import java.util.ArrayList;
  import java.util.Scanner;
  
  public class Demo {
  	public static void main(String[] args) {		
  		ArrayList<Student> array = new ArrayList<Student>();		
  		addStudent(array);	
  		Student s = array.get(0);
  		System.out.println(s.getName()+" "+s.getAge());
  	}
  	
  	public static void addStudent(ArrayList<Student> arr){
  		Scanner sc = new Scanner(System.in);
  		String name = sc.nextLine();
  		String age = sc.nextLine();
  		Student s = new Student();
  		s.setName(name);
  		s.setAge(age);
  		arr.add(s);
  	}
  }
  ```

  学生类：

  ```Java
  public class Student {
  	private String name;
  	private String age;
  	
  	public Student() {
  	}
  	public Student(String name, String age) {
  		this.name = name;
  		this.age = age;
  	}
  	public String getName() {
  		return name;
  	}
  	public void setName(String name) {
  		this.name = name;
  	}
  	public String getAge() {
  		return age;
  	}
  	public void setAge(String age) {
  		this.age = age;
  	}
  }
  ```

## 3、学生管理系统

- 学生类

  ```java
  public class Student {
  	private String id;
  	private String name;
  	private String age;
  	private String address;
  	
  	public Student() {
  	}
  	
  	public Student(String id, String name, String age, String adress) {
  		this.id = id;
  		this.name = name;
  		this.age = age;
  		this.address = address;
  	}
  
  	public String getId() {
  		return id;
  	}
  
  	public void setId(String id) {
  		this.id = id;
  	}
  
  	public String getName() {
  		return name;
  	}
  
  	public void setName(String name) {
  		this.name = name;
  	}
  
  	public String getAge() {
  		return age;
  	}
  
  	public void setAge(String age) {
  		this.age = age;
  	}
  
  	public String getAddress() {
  		return address;
  	}
  
  	public void setAdress(String address) {
  		this.address = address;
  	}
  }
  ```

- 操作类

  ```java
  import java.util.ArrayList;
  import java.util.Scanner;
  
  public class Manager {
  	public static void main(String[] args) {
  		System.out.println("----进入系统-----");
  		
  		ArrayList<Student> array = new ArrayList<Student>();
  		while(true){
  			System.out.println("1.查看");
  			System.out.println("2.添加");
  			System.out.println("3.删除");
  			System.out.println("4.修改");
  			System.out.println("5.退出");
  			System.out.println("请输入");
  			Scanner sc = new Scanner(System.in);
  			String choice = sc.nextLine();
  			
  			switch(choice){
  			case "1":
  				findStudent(array);
  				break;
  			case "2":
  				addStudent(array);
  				break;
  				
  			case "3":
  				deletStudent(array);
  				break;
  				
  			case "4":
  				updateStudent(array);
  				break;
  			case "5":
  			default:
  				System.out.println("退出成功");
  				System.exit(0);  //JVM退出
  				break;
  			}
  		}
  	}
  	public static void findStudent(ArrayList<Student> arr){
  		if(arr.size() ==0 ){
  			System.out.println("无数据信息");
  		}else{
  			System.out.println("学号\t姓名\t年龄\t居住地");
  			for(int x=0;x<arr.size();x++){
  				Student s = arr.get(x);
  				System.out.println(s.getId()+"\t"+s.getName()+"\t"+s.getAge()+"\t"+s.getAddress());
  			}
  		}
  	}
  	
  	public static void addStudent(ArrayList<Student> arr){
  		Scanner sc = new Scanner(System.in);
  		String id;
  		while(true){
  			System.out.println("学号");
  			id = sc.nextLine();
  			boolean flag = false;
  			for(int x=0;x<arr.size();x++){
  				Student s = arr.get(x);
  				if(s.getId().equals(id)){
  					System.out.println("学号重复");
  					flag = true;
  					break;
  				}
  			}
  			if(flag){
  				System.out.println("重新输入");
  			}else{
  				break;
  			}
  		}
  		
  		System.out.println("姓名");
  		String name = sc.nextLine();
  		System.out.println("年龄");
  		String age = sc.nextLine();
  		System.out.println("地址");
  		String address = sc.nextLine();
  		
  		Student s = new Student();
  		s.setId(id);
  		s.setName(name);
  		s.setAge(age);
  		s.setAdress(address);
  		
  		arr.add(s);
  		System.out.println("添加成功");
  		
  	}
  	
  	public static void deletStudent(ArrayList<Student> arr){
  		Scanner sc = new Scanner(System.in);
  		System.out.println("删除学生学号");
  		String id = sc.nextLine();
  		int index = -1;
  		
  		for(int x=0;x<arr.size();x++){
  			Student s = arr.get(x);
  			if(s.getId().equals(id)){
  				index = x;
  				break;
  			}
  		}
  		
  		if(index == -1){
  			System.out.println("不存在此学号");
  		}else{
  			arr.remove(index);
  			System.out.println("删除成功");
  		}
  	}
  	
  	public static void updateStudent(ArrayList<Student> arr){
  		Scanner sc = new Scanner(System.in);
  		System.out.println("修改学生学号");
  		String id = sc.nextLine();
  		int index = -1;
  		
  		for(int x=0;x<arr.size();x++){
  			Student s = arr.get(x);
  			if(s.getId().equals(id)){
  				index = x;
  				break;
  			}
  		}
  		
  		if(index == -1){
  			System.out.println("不存在此学号");
  		}else{
  			System.out.println("新姓名");
  			String name = sc.nextLine();
  			System.out.println("新年龄");
  			String age = sc.nextLine();
  			System.out.println("新地址");
  			String address = sc.nextLine();
  			Student s = new Student();
  			s.setId(id);
  			s.setName(name);
  			s.setAge(age);
  			s.setAdress(address);
  			arr.set(index, s);
  			System.out.println("修改成功");
  		}
  	}
  }
  ```

  