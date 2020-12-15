## 1、IO流

> 用来处理设备之间的数据传输，包括存储数据到文件和从文件读取内容。

- 分类：输入流（读取数据）；输出流（存储数据）。

## 2、FileWriter类

- 将数据写入文件，输出流。
- 构造方法：`FileWriter(String filename);`
-  写入文件的方法：
  1. 创建输出流对象。
  2. 调用输出流对象写数据的方法。
  3. 释放资源。
- 创建输出流对象：
  - `FileWriter fw = new FileWriter("路径");`
  - main后抛除IO异常：`throws IOException`
- 创建输出流对象做了什么：
  1. 调用系统资源创建了一个文件。
  2. 创建输出流对象。
  3. 把输出流对象指向文件。
- 写一个字符串数据：
  - 将数据写入内存缓冲区：`fw.writer("字符串内容");`
  - 刷新缓冲区内的数据写入文件：`fw.flush();`
  - （先刷新缓冲区，再）释放资源：`fw.close();`
- 路径：
  - 绝对路径，带有盘符，如`"d\\a.txt"`
  - 相对路径，相对当前项目而言的，不带有盘符，在项目的根目录下。如`"a.txt"`
- 写数据的方法：
  - 写一个字符串数据：`void writer(String str);`
  - 写字符串中 index 开始 len 长度的数据：`void writer(String str,int index,int len);`
  - 写一个字符数据，既可以写char类型也可以写char对应的int数据：`void write(int ch);`
  - 写一个字符数组：`void wite(char[] chs);`
  - 写字符数组中 index 开始 len 长度的数据：`void write(char[] chs,int index,int len);`
- 数据换行：`fw.writer("换行符");`，
  - WIndows系统换行是：`\r\n`。
  - Linux是：`\n`。
  - Mac是：`\r`。
- 追加写入：`FileWriter fw = new FileWriter(String Filename,boolean append)`，追加写入则应有`append=true`。
- 创建 `FileWriter`对象读入文本后直接 `close()`，文本中存储的内容会被清除。

## 3、FileReader 类

- 从文件中读数据，输入流。

- 构造方法：

  - `FileReader fr = new FileReader("路径");`

- 输入流读文件：

  1. 创建输入流对象。
  2. 调用输入流对象的读数据方法。
  3. 释放资源。

- 读数据的方法：

  - 一次读入一个字符：`int read();`

    - `int ch = fr.read();`

    - 这得到的是字符串的ASCII码。
    - 得到字符需要`(char)ch`。
    - 当返回的值为 -1 时，代表没有数据了，读数据循环的终止条件。
    - 不需要 println 换行，因为已经从文件中读取了换行符。

    ```java
    int ch;
    while((ch=fr.read())!=-1){
        System.out.print((char)ch);
    }
    fr.close();
    ```

  - 一次读入一个字符串：`int read(char[] cbuf);`

    - `char[] chs = new char[size];`，（size一般取1024）。
    - `int len = fr.read(chs);`，返回的是实际读取的字符个数。
    - 得到每次读取到的数据：`new String(chs,0,len);`，chs 中 len 之后的内容为上次读取的未被覆盖的内容。
    - len 为 -1 时代表没有数据了。
    - 不需要 println 换行，因为已经从文件中读取了换行符，并且 \r\n 也有字符个数为2。

    ```java
    char[] chs = new char[1024];
    int len;
    while((len = fr.read(chs))!=-1){
        System.out.print(new String(chs,0,len));
    }
    fr.close();
    ```

- 一次读写一个字符复制文本文件：

  ```java
  public static void main(String[] args) throws IOException {
  	FileWriter fw = new FileWriter("Copydemo.java");
  	FileReader fr = new FileReader("Demo.java");
  		
  	int ch;
  	while((ch=fr.read())!=-1){
  		fw.write(ch);
  		System.out.print((char)ch);
  	}
  
  	fw.close();
      fr.close();
  }
  ```

- 一次读写一个字符数组复制文本文件：

  ```java
  public static void main(String[] args) throws IOException {
  	FileWriter fw = new FileWriter("Copydemo.java");
  	FileReader fr = new FileReader("Demo.java");
  		
  	char[] chs = new char[1024];
  	int len;
  	while((len=fr.read(chs))!=-1){
  		fw.write(chs,0,len);
  	}
  
  	fw.close();
  	fr.close();
  }
  ```



## 4、字符缓冲流

- BufferedWriter 类 / BufferedReader 类

- 写入 / 读取文本，缓冲各个字符，实现字符和字符数组的高效写入 / 读取。

- BufferedWriter 类：

  - 构造方法：
    - `BufferedWriter bw = new BufferedWriter(new FileWriter("路径"));`
  - 同样也需要`bw.flush();`和`bw.close();`。

- BufferedReader类：

  - 构造方法：
    - `BufferedReader br = new BufferedReader(new FileReader("路径"));`
  - 也具有一次读一个字符串或一个字符数组两种方式，方法与 FileReader 使用方法相同。
  - 同样也需要`br.flush();`和`br.close();`。

- 缓冲流的特殊功能：

  - BufferedWriter中：

    - `void newLine();`。
    - `bw.newLine();`写一个换行符，跟随系统决定。

  - BufferedReader中：

    - `String readLine();`。
    - `line = br.readLine();`一次读取一行数据，不读取换行符。
    - 没有数据时，返回值为 null 。

    ```java
    String line;
    while((line=br.readLine())!=null){
        System.out.println(line);
    }
    br.close();
    ```

- 特殊功能复制文件：

  ```java
  BufferedWriter bw = new BufferedWriter(new FileWriter("bufferedcopy.java"));
  BufferedReader br = new BufferedReader(new FileReader("Demo.java"));
  		
  String line;
  while((line=br.readLine())!=null){
  	bw.write(line);
  	bw.newLine();
  	bw.flush();
  }
  bw.close();
  br.close();
  ```

## 5、集合与文本文件

- 集合数据写入文本文件步骤：

  1. 创建集合对象。
  2. 往集合中添加字符串元素。
  3. 创建输出缓冲流对象。
  4. 遍历集合，得到每一个字符串元素，把该字符串元素作为数据写到文本文件。
  5. 释放资源。

  ```java
  ArrayList<String> array = new ArrayList<String>();
  array.add("hello");
  array.add("java");
  BufferedWriter bw = new BufferedWriter(new FileWriter("array.txt"));
  
  for(int x=0;x<array.size();x++){
      String s = array.get(x);
      bw.write(s);
      bw.newLine();
      bw.flush();
  }
  bw.close();
  ```

- 文本文件中的数据读取到集合步骤：

  1. 创建输入缓冲流对象。
  2. 创建集合对象。
  3. 读取数据，每次读取一行数据，把该行数据作为一个元素存储到集合中。
  4. 释放资源。

  ```java
  BufferedReader br = new BufferedReader(new FileReader("array.txt"));
  ArrayList<String> array = new ArrayList<String>();
  String line;
  while((line=br.readLine())!=null){
      array.add(line);
  }
  br.close();
  ```