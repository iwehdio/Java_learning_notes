## 1、标准输入输出流

- 类字段：静态修饰的成员变量。

- 标准输入流：`public static final InputStream in`：字节输入流。

  - 用来读取键盘录入的数据：`Scanner sc = new Scanner(System.in);`。

- 标准输出流：`public static final OuputStream out`：字节输出流。

  - 将数据输出到命令行：`System.out.println();`。

- `OutputStreamWriter`：转换流，把字符输出流转换为字节输出流。

  * 构造方法：`OutputStreamWriter(OutputStream out)`。转换后的字节输出流在 out 中。
  * 字符数据 > 字符流 > OutputStreamWriter > 字节流 > 文件。
  * 虽然是以字符的输出流形式，操作字节流输出流，但是实际上还是以字节的形式输出。

- 读取文本文件并输出到命令行：

  ```java
  BufferedReader br = new BufferedReader(new FileReader("A.java"));
  BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
  String line;
  while((line =br.line())!=null){
      bw.writer(line);
      bw.newLine();
  }
  br.close();
  bw.close();
  ```

- `IntputStreamReader`：转换流，把字节输入流转换为字符输入流。

  * 构造方法：`InputStreamReader(InputStream in)`。
  * 字符数据 < 字符流 < OutputStreamWriter < 字节流 < 文件。
  * 字符的输入流虽然是以字符的形式操作，但是还是使用了字节流。
  * 在传输或者从文件读取数据的时候，文件里真正保存的数据永远是字节，终端点实际上是以字节的形式进行的。

- 读取键盘输入并输出到文本文件：

  ```java
  InputStream is = System.in;
  Reader r = new InputStreamReader(is);
  FileWriter fw = new FileWriter("A.txt");
  char[] chs = new char[1024];
  int len;
  while((len = r.read(chs)) != -1){
      fw.write(chs, 0, len);
      fw.flush();
  }
  fw.close();
  is.close();
  ```

## 2、打印流

- 只能输出，不能输入。

- 字符打印流：`PrintWriter`。字节打印流：`PrintStream`。

-  字符打印流：

  - 可以自动换行，`println()`。

  - 不能输出字节，但可以输出其他任意类型。

  - 通过某些配置，可以自动刷新（仅调用 println 、printf 、format时）。

  - 是包装流，不具有写出功能。

  - 可以把字节输出流转换成字符输出流。

  - 例：

    ```java
    PrintWriter pw = new PrintWriter("a.txt");
    pw.writer("ABC");
    pw.println("DEF");	//换行
    pw.close();
    ```

- 配置自动刷新：

  - 构造方法：`PrintWriter(Writer out, boolean autoFlush)`和`PrintWriter(OutputStream out, boolean autoFlush)`，并设定自动刷新为 true 。
  - 例：`PrintWriter pw = new PrintWriter(new FileWriter("a.txt"), true);`。

- 打印流复制文本文件：

  ```java
  BufferedRead br = new BufferedReader(new FileReader("source.java"));
  PrintWriter pw = new PrintWriter(new FileWriter("copy.java"), true);
  String line;
  while((line = br.readLine()) != null){
      pw.println(line);
  }
  pw.close();
  br.close();
  ```

## 3、对象操作流

> 用于读写任意类型的对象

- `ObjecctOutputStream`：对象字节输出流。

  - `writeObject`：写入方法。
  - `ObjecctOutputStream(OutputStream out)`：构造方法，输入为字节输出流。

- `ObjecctInputStream`：对象字节输入流。

  - `readObject`：读取方法。
  - `ObjecctInputStream(InputStream in)`：构造方法，输入为字节输入流。

- 使用对象输出流写出对象，只能使用对象输入流读取对象。

- 只能支持实现 java.io.Serializable 接口的对象，这个接口被称为序列化接口，是一个标识接口，只起标识作用，没有方法。。

- 对象输出流写入，例：

  ```java
  // 实现序列化接口
  class Student implements Serializable{
      String name;
      int age;
      public Student(String name, int age){
          this.name = name;
          this.age = age;
      }
      @Override
      public String toString(){
          return name + age;
      }
  }
  // 写入对象
  ObjecOutputStream oos = new ObjecOutputStream(new FileOutputStream("a.txt"));
  Student s1 = new Student("name1", 0);
  Student s2 = new Student("name2", 1);
  oos.writeObject(s1);
  oos.writeObject(s2);
  oos.close();
  // 读取对象
  ObjecInputStream ois = new ObjectInputStream(new FileInputStream("a.txt"));
  try {
      while(true){
      	Object obj = ois.readObject();
      	System.out.println(obj);
  	}
  } catch(EOFException e){	// 读取文件末尾异常
      System.out.println("到达文件末尾");
  }
  ```

- 解决对象输入流读取对象出现异常的问题：用集合存储多个对象，这样写入的只有一个集合对象。

  ```java
  ObjecOutputStream oos = new ObjecOutputStream(new FileOutputStream("a.txt"));
  ArrayList<Student> list = new ArrayList<Student>();
  list.add(new Student("name1", 0));
  list.add(new Student("name2", 1));
  oos.writeObject(list);
  oos.close();
  
  ObjecInputStream ois = new ObjectInputStream(new FileInputStream("a.txt"));
  Object obj = ois.readObject();
  ArrayList getlist = (ArrayList<Student>) obj;
  for(Student s : getlist){
      System.out.println(s);
  }
  ```

- 实现 Serializable 序列化接口时，会根据类中的成员赋予一个序列化编号。如果不想改变类中的成员时导致更改前后无法读取，需要固定序列编号。例：`private static final long serialVersionUID = 编号L;`。

## 4、Properties集合

- 这个类表示了一个持久的属性集,，实现了 map 接口。属性列表中的键值对都是字符串。

- 例：

  ```java
  // 创建属性列表对象，并添加映射关系
  Properties prop = new Properties();
  prop.put("A", 1);
  prop.put("B", 2);
  // 获取keys，遍历属性列表
  Set<Object> keys = prop.keySet();
  for(Object obj : keys){
      Object value = prop.get(key);
      System.out.println(key + value);
  }
  ```

- Properties 集合与 IO 流结合：

  - `void list(PrintWriter out)`：将属性列表写入到文件。
  - `void load(Read reader)`：将文件中的数据写入属性列表。
  - `void store(Writer writer, String comments)`：将属性列表写入到文件，comments为描述文本，没有时置为 null 。

## 5、编码表

> 计算机底层二进制编码转换成对应的字符

- ASCII ：最基础的编码表。

- GBK：中国标准编码表。

- Unicode：通用编码表，所有字符都占两个字节。

-  UTF-8：长度可变的码表。

- ANSI：本地编码表。

- Java中字符串默认使用ANSI（对于简体中文是 gbk）。

  - 指定编码写入：

    ```java
    String s = "abc简体中文";
    byte[] bys1 = s.getBytes();	// 默认编码
    byte[] bys2 = s.getBytes("UTF-8");	// 设置编码
    FileOutputStream fos = new FileOutputStream("a.txt");
    fos.write(bys);
    fos.close();
    ```

  - 指定编码读取：

    ```java
    FileInputStream fis = new FileInputStream("a.txt");
    byte[] bys = new byte[1024];
    int len = fis.read(bys);
    System.out.println(new String(bys, 0, len, "UTF-8"));
    ```

- 对于乱码，编码保持前后一致即可。

- 字符流中的编码：

  - 按编码把字符串转成字节数组，再把字节数组转回字符串。

    ```java
    FileWriter fw = new FileWriter("a.txt");
    String s = "编码";
    byte[] bys = s.getBytes("UTF-8");
    fw.write(new String(bys));
    fw.close();
    ```

  - 使用转换流，设置输入的字节流编码。

    ```java
    OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("a.txt"), "UTF-8");
    String s = "编码";
    osw.write(s);
    osw.close();
    ```

  - 字符流可以理解为字节流加编码。

    

    

    

    

