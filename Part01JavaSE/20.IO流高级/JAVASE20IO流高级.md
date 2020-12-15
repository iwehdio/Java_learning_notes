## 1、File类

> 文件和目录路径名的抽象表示形式，实例不可改变。

- 构造方法：
  - `File(String pathname)`：将指定的路径名转换成一个 File 对象。
  - `File(String parent, String child)`：根据指定的父路径和相对文件路径创建 File 对象。
  - `File (File parent, String child)`：根据指定的父路径对象和相对文件路径创建 File 对象。
- 绝对路径：固定不可变、以盘符开头的路径。相对路径：相对某个参照物，不以盘符开头。Eclipse中默认相对路径为项目根目录下。
- File 的常用功能：
  - 创建功能：
    - `boolean creatNewFile()`：创建文件。当指定文件不存在时创建文件并返回 true ，否则返回 false 。
    - `boolean mkdir()`：创建文件夹。当指定文件夹不存在时创建文件夹并返回 true ，否则返回 false 。
    - `boolean mkdirs()`：创建文件夹。当文件夹所在的父目录不存在，则也创建父文件夹。
  - 删除功能：
    - `boolean delete()`：删除文件 / 文件夹。当指定文件 / 文件夹存在时返回 true ，否则返回 false 。删除文件夹时，其下方不能有子文件和子文件夹。
  - 判断功能：
    - `boolean exist()`：判断文件 / 文件夹是否存在。
    - `boolean isAbsolute()`：判断 File 对象指向的是否是绝对路径。
    - `boolean isDirectory()`：判断 File 对象指向的是否是文件夹。
    - `boolean isFile()`：判断 File 对象指向的是否是文件。
    - `boolean isHidden()`：判断 File 对象指向的是否是隐藏文件 / 文件夹。
  - 获取功能：
    - `File getAbsoluteFile()`：返回 File 对象指向的绝对路径，返回值为 File 对象。
    - `String getAbsolutePath()`：返回 File 对象指向的绝对路径，返回值为字符串。
    - `String getParent()`：获取父路径（前提是创建时按由父路径的构造方法），返回值为字符串。
    - `File getParentFile()`：获取父路径（前提是创建时按由父路径的构造方法），返回值为 File 对象。
    - `String getName()`：获取文件或文件夹名称。
    - `String getPath()`：返回创建 File 对象时给出的路径。
    - `long lastModified()`：返回最后一次修改的时间，返回值为毫秒的形式。
    - `long length()`：返回文件的大小，返回值为字节数。
  - 修改功能：
    - `boolean renameTo(File dest)`：将当前 File 对象指向的路径修改为指定的 File 指向的路径。不允许修改为已存在的路径。
  - 批量获取功能：
    - `String[] list()`：返回当前路径下所有文件和文件夹名称，返回值为字符串。调用该方法的 File 对象指向的必须是文件夹。
    - `File[] listFiles()`：返回当前路径下所有文件和文件夹名称，返回值为 File 对象。调用该方法的 File 对象指向的必须是文件夹。
    - `static File[] listRoots()`：返回所有盘符即根目录。

## 2、文件操作例

- 通过指定父路径和子路径来创建文件（直接由不存在的父路径创建会出错），例：

  ```java
  File parent = new File("a");
  File f = new File(parent, "b.txt");
  if(!parent.exist()){
      parent.mkdirs();
  }
  f.createNewFile();
  ```

- 输出指定目录下的 java 文件名和子目录（递归），例：

  ```java
  public static void method(File file){
      if(file.isDirectory()){
          File[] files = file.listFiles();
          for(File f : files){
              if(f.isFile()){
                  // 输出文件
                  if(f.getName().endsWith(".java")){
                      System.out.println(f.getName);
                  }
              } else {
                  // 输出目录
                  method(f);
              }
          }
      }
  }
  ```

- 删除指定的目录（包含子目录），例：

  ```java
  public static void method(File file){
      if(file.isDirectory()){
          File[] files = file.listFiles();
          for(File f : files){
              if(f.isFile()){
                  // 删除文件
                  System.out.println(f.getName);
                  f.delete();
              } else {
                  // 递归删除子目录下的文件
                  method(f);
              }
          // 最后删除指定目录
          file.delete();
      }
  }
  ```

## 3、字节流

- IO 流分类：

  - 按流向：分为输入流（读取）和输出流（写出）。
  - 按数据类型：字节流和字符流。其下又按流向分类。

- 字节输入流：IO包下的 InputStream 抽象类。操作文件的是 FileInputStream ， 字节高效输入流 BufferedInputStream。

- 字节输出流：IO包下的 OutputStream 抽象类。操作文件的是 FileOutputStream ， 字节高效输出流 BufferedOutputStream。

- 二进制文件如图片视频音频只能用字节流，文本文件既可以用字节流也可以用字符流。

- 字节流复制图片：

  ```java
  FileInputStream fis = new FileInputStream("A.jpg");
  FileOutputStream fos = new FileOutputStream("B.jpg");
  int len;
  byte[] byt = new byte[1024];
  while((len = fis.read(bys)) != -1){
      fos.writer(byt, 0, len);
      // 使用字节流可以不刷新
  }
  fos.close();
  fis.close();
  ```

- 字节流复制文本：

  ```java
  FileInputStream fis = new FileInputStream("A.txt");
  FileOutputStream fos = new FileOutputStream("B.txt");
  // 方法1：一次读取一个字节
  int by;
  while((by = fis.read()) != -1){
      fos.write(by);
  }
  // 方法2：一次读取一个字节数组
  int len;
  byte[] byt = new byte[1024];
  while((len = fis.read(bys)) != -1){
      fos.writer(byt, 0, len);
  }
  // 释放资源
  fos.close();
  fis.close();
  ```

  

 