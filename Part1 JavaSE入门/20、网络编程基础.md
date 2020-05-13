## 1、网络编程基础

- Socket：用于描述 IP 地址和端口，是一个通信链的句柄。
- 网络通信三要素：
  1. IP 地址：网络中设备的标识，32位二进制数，表示为点分十进制。
  2. 端口号：用于标识进程的逻辑地址。
  3. 协议：UDP 协议（无连接、速度快、不可靠，发送前打包，最大 64k ）和 TCP 协议（建立连接、速度较慢、可靠）。
- InetAddress 类：
  - 用于表示互联网协议即 IP 地址。
  - `static InetAddress getBuName(String host)`：根据主机名或 IP地址获取 InetAddress 对象。
  - `String getHostAddress(String host)`：获取InetAddress 对象的地址。
  - `String getHostName(String host)`：获取InetAddress 对象的主机名。

## 2、UDP协议

- 使用 UDP 协议发送数据：
  1. 创建发送端 Socket 对象。
  2. 创建数据并打包。
  3. 发送数据。
  4. 释放资源。
- 使用 UDP 协议接收数据：
  1. 创建接收端 Socket 对象。
  2. 接收数据。
  3. 解析数据。
  4. 释放资源。
- DatagramSocket 类：基于 UDP 协议的用于发送和接收数据报包的套接字。
  - 构造方法：
    - `DatagramSocket()`：创建 Socket 对象并随机分配端口号。
    - `DatagramSocket(int port)`创建 Socket 对象并指定分配端口号。
  - `void send(DatagramPacket p)`：发送数据包。
  - `void receive(DatagramPacket p)`：接收数据包。
- DatagramPacket 类：表示数据报包。
  - 数据：字节数组 `byte[]`。
  - 设备地址：IP 。
  - 进程的地址：端口号。
  - 构造方法：`DatagramPacket(byte[] buf, int length, InetAddress address, int port)`。
  - `InetAddress getAddress()`：接收端获取数据包发送地址。
  - `byte[] getData()`：接收端获取数据包发送数据。
  - `int getLength()`：接收端获取数据包发送数据长度。

- UDP 发送：

  ```java
  // 创建发送端Socket对象
  DatagramSocket ds = new DatagramSocket();
  String s = "UDP";
  byte[] bys = s.getBytes();	// 字符串转字节数组
  int length = bys.length;
  InetAddress address = InetAddress.getByName("iwehdio");
  int port = 28;
  // 数据打包
  DatagramPacket dp = new DatagramPacket(bys, length, address, port);
  ds.send(dp);	// 发送
  ds.close();		// 释放资源
  ```

- UDP 接收：

  ```java
  // 创建接收端Socket对象，指定端口号
  DatagramSocket ds = new DatagramSocket(28);
  byte[] bys = new byte[1024];
  DatagramPacket dp = new DatagramPacket(bys, bys.length);
  ds.receive(dp);	// 没有接收到数据前在此阻塞
  InetAddress address = dp.getAddress();
  byte[] data = dp.getData();		// data中的内容与 bys 中实际上是一样的
  int length = dp.getLength();
  System.out.println("sender-->" + address.getHostAddress());
  System.out.println(new String(data, 0, length));
  ds.close();
  ```

- UDP 协议收发数据的注意事项：
  - 端口号错误，数据可以正常发出，不会出现异常，但是收不到数据。
  - 同时运行的进程端口号不能重复。

## 3、TCP协议

- Socket 类：实现客户端（发送端）套接字。

  - 构造方法：
    - `Socket(InetAddress address, int port)`。
  - `OutputStream getOutputStream()`：获取输出流对象。

- SeverSocket 类：实现服务端（接收端）套接字。

  - 构造方法：`ServerSocket(int port)`。
  - `Socket accept()`：监听并接收到此套接字的的连接。

- TCP 协议发送数据：

  1. 创建发送端 Socket 对象（创建连接）。
  2. 获取输出流对象。
  3. 发送数据。
  4. 释放资源。

- TCP 协议接收数据：

  1. 创建接收端 Socket 对象。
  2. 监听（阻塞）。
  3. 获取输入流对象。
  4. 获取数据。
  5. 释放资源。

- TCP 发送：

  ```java
  // 创建发送端 Socket 对象，建立连接
  Socket s = new Socket(InetAddress.getByName("iwehdio"),29);
  // 获取输出流对象
  OutputStream os = s.getOutputStream();
  String str = "TCP";
  os.write(str.getBytes());	// 发送数据
  s.close();	//释放资源（Socket自动关闭 os ）
  ```

- TCP 接收：

  ```java
  // 创建接收端 Socket 对象
  ServerSocket ss = new ServerSocket(29);
  // 获取监听
  Socket s = ss.accept();
  // 获取输入流对象
  InputStream is = s.getInputStream();
  // 获取数据
  byte[] bys = new byte[1024];
  int len;
  len = is.read(bys);
  System.out.println("sender-->" + s.getInetAddress（);
  System.out.println(new String(bys, 0, len));
  s.close();	// 释放资源，服务器端可以不关闭
  ```

## 4、案例

- 客户端发送字符串到接收端，服务端将字符串转换为大写后返回客户端。

  - TCP 客户端：

    ```java
    Socket s = new Socket(InetAddress.getByName("iwehdio"),29);
    OutputStream os = s.getOutputStream();
    String str = "tcp";
    os.write(str.getBytes());
    // 创建输入流对象
    InputStream is = s.getInputStream();
    byte[] bys = new byte[1024];
    int len;
    len = is.read(bys);
    System.out.println(new String(bys, 0, len));
    s.close();	//释放资源
    ```

  - TCP 服务端：

    ```java
    ServerSocket ss = new ServerSocket(29);
    Socket s = ss.accept();
    InputStream is = s.getInputStream();
    byte[] bys = new byte[1024];
    int len;
    len = is.read(bys);
    String str = new String(bys, 0, len);
    System.out.println("sender-->" + s.getInetAddress());
    System.out.println(str);
    // 获取输出流对象
    OutputStream os = s.getOutputStream();
    String sss = str.toUpperCase();
    os.write(sss.getBytes());
    s.close();	// 释放资源
    ```

-  模拟用户登录：

  - 客户端流程：
    1. 创建客户端 Socket 对象。
    2. 获取用户名和密码。
    3. 获取用户名和密码。
    4. 获取输出流对象。
    5. 数据从客户端发送到服务端。
    6. 获取输入流对象。
    7. 获取服务器返回的数据。
    8. 释放资源。
  - 服务端流程：
    1. 创建服务端 Socket 对象。
    2. 监听。
    3. 获取输入流对象。
    4. 获取用户名和密码。
    5. 判断用户名好=和密码是否正确。
    6. 返回判断信息。
    7. 释放资源。

  - TCP 客户端：

    ```java
    // 创建发送端 Socket 对象，建立连接
    Socket s = new Socket(InetAddress.getByName("iwehdio"),29);
    // 高效流，键盘输入字节流转字符流
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    System.out.println("username");
    String username = br.readLine();
    System.out.println("password");
    String password = br.readLine();
    // 打印流自动换行输出
    PrintWriter out = new PrintWriter(s.getOutputStream(), true);
    out.println(username);
    out.println(password);
    // 获取服务端发送的判断结果
    BufferedReader serverBr = new BufferedReader(new InputStreamReader(s.getInputStream()));
    String res = serverBr.readLine();
    System.out.println(res);
    s.close();
    ```

  - TCP 服务端：

    ```java
    // 创建接收端 Socket 对象
    ServerSocket ss = new ServerSocket(29);
    // 获取监听
    Socket s = ss.accept();
    // 高效流，输入字节流转字符流
    BufferedReader br = new BufferedReader(new InputStreamReader(s.getInputStream()));
    // 按行获取数据
    String username = br.readLine();
    String password = br.readLine();
    // 匹配在已有的用户
    boolean flag = false;
    List<User> users = UserDB.getUsers();
    User user = new User(username, password);
    if(users.contains(user)){
        flag = true;
    }
    // 打印流输出判断结果
    PrintWriter out = new PrintWriter(s.getOutputStream(), true);
    if(flag){
        out.println("Y");
    }else{
        out.println("N");
    }
    s.close();
    ```

    

