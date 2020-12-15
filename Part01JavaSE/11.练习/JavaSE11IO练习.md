## 1、IO练习

- 键盘录入学生信息存入集合，然后存入文件的步骤：
  1. 定义学生类。
  2. 创建集合对象。
  3. 写方法实现键盘录入学生信息，并把学生对象作为元素添加到集合。
  4. 创建输出缓冲流对象。
  5. 遍历集合，得到每一个学生信息，并将其按一定格式写入文件。

```java
public static void main(String[] args) throws IOException {
	
	ArrayList<Student> array = new ArrayList<Student>();
	
	addStudent(array);
	addStudent(array);
	addStudent(array);
	
	BufferedWriter bw = new BufferedWriter(new FileWriter("d.txt"));
	
	for(int x=0;x<array.size();x++){
		Student s = array.get(x);
		StringBuilder sb = new StringBuilder();
		sb.append(s.getId()).append(", ").append(s.getName()).append(", ").append(s.getAge()).append(", ").append(s.getAddress());
		
		bw.write(sb.toString());
		bw.newLine();
		bw.flush();
		
	}
	
	bw.close();
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
	s.setAddress(address);
	
	arr.add(s);
	System.out.println("添加成功");
	
}
```



- 将文本数据中的数据读到集合中并遍历集合：
  1. 定义学生类。
  2. 创建输入缓冲流对象。
  3. 创建集合对象。
  4. 读取文件数据，并把数据按照一定格式进行分割，赋值到学生对象，并把学生对象作为元素存储到集合。
  5. 遍历集合。

```java
public static void main(String[] args) throws IOException {
	BufferedReader br = new BufferedReader(new FileReader("d.txt"));
	ArrayList<Student> array = new ArrayList<Student>();
	
	String line;
	while((line=br.readLine())!=null){
		Student s = new Student();
		String[] strArr = line.split(", ");
		s.setId(strArr[0]);
		s.setName(strArr[1]);
		s.setAge(strArr[2]);
		s.setAddress(strArr[3]);
		array.add(s);
	}
	br.close();
	
	for(int x=0;x<array.size();x++){
		Student s = array.get(x);
		System.out.println(s.getId()+"\t"+s.getName()+"\t"+s.getAge()+"\t"+s.getAddress());
		
	}
}
```



## 2、IO版学生管理系统

- 把学生对象作为元素与集合联系起来。
- 把文件用读写与集合联系起来。
- 用集合做中介将学生对象和文件联系起来。
- 传入方法的是文件路径，然后在方法中在读写文件。

```java
public static void main(String[] args) throws IOException {
	System.out.println("----进入系统-----");
	String filename = "Student.txt";
	
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
			findStudent(filename);
			break;
		case "2":
			addStudent(filename);
			break;
			
		case "3":
			deletStudent(filename);
			break;
			
		case "4":
			updateStudent(filename);
			break;
		case "5":
		default:
			System.out.println("退出成功");
			System.exit(0);  //JVM退出
			break;
		}

	}
}
public static void findStudent(String filename) throws IOException{
	ArrayList<Student> arr = new ArrayList<Student>();
	readData(filename, arr);
	if(arr.size() ==0 ){
		System.out.println("无数据信息");
	}else{
		System.out.println("学号\t姓名\t年龄\t居住地");
		for(int x=0;x<arr.size();x++){
			Student s = arr.get(x);
			System.out.println(s.getId()+"\t"+s.getName()+"\t"+s.getAge()+"\t"+s.getAddress());
		}
	}
	writeData(filename, arr);
}

public static void addStudent(String filename) throws IOException{
	ArrayList<Student> arr = new ArrayList<Student>();
	readData(filename, arr);
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
	s.setAddress(address);
	
	arr.add(s);
	System.out.println("添加成功");
	writeData(filename, arr);
}

public static void deletStudent(String filename) throws IOException{
	ArrayList<Student> arr = new ArrayList<Student>();
	readData(filename, arr);
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
	writeData(filename, arr);
}

public static void updateStudent(String filename) throws IOException{
	ArrayList<Student> arr = new ArrayList<Student>();
	readData(filename, arr);
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
		s.setAddress(address);
		arr.set(index, s);
		System.out.println("修改成功");
	}
	writeData(filename, arr);
}

public static void readData(String filename,ArrayList<Student> arr) throws IOException{
	BufferedReader br = new BufferedReader(new FileReader(filename));
	
	String line;
	while((line=br.readLine())!=null){
		String[] data = line.split(", ");
		Student s = new Student();
		s.setId(data[0]);
		s.setName(data[1]);
		s.setAge(data[2]);
		s.setAddress(data[3]);
		arr.add(s);
	}
	br.close();
}

public static void writeData(String filename,ArrayList<Student> arr) throws IOException{
	BufferedWriter bw = new BufferedWriter(new FileWriter(filename));
	
	for(int x=0;x<arr.size();x++){
		Student s = arr.get(x);
		StringBuilder sb = new StringBuilder();
		sb.append(s.getId()).append(", ").append(s.getName()).append(", ").append(s.getAge()).append(", ").append(s.getAddress());
		bw.write(sb.toString());
		bw.newLine();
		bw.flush();
	}
	bw.close();
}
```



