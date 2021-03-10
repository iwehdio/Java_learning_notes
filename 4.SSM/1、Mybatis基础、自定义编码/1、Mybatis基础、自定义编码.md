## 1、Mybatis基础、自定义编码

- 框架：软件开发中的一套解决方案。封装了许多细节，使得开发者能较为简单的完成需求。

- 三层架构：

  - 表现层：展示数据（SpingMVC）。
  - 业务层：处理业务需求（Spring控制反转和面向切片编程）。
  - 持久层：与数据库交互（MyBatis）。

- 持久层解决方案：

  - JDBC技术：是一个规范而不是框架。
  - Spring的JdbcTemplate是对JDBC的简单封装。
  - Mybatis内部封装了JDBC，屏蔽了底层访问细节。只需要关注sql语句本身。
  - 使用了ORM思想（对象关系映射），将数据库表和实体类的属性对应起来，操作实体类就可以操作数据库表。

- Mybatis使用流程：

  - 创建数据库：

    ```mysql
    CREATE TABLE `user` (
      `id` int(11) NOT NULL auto_increment,
      `username` varchar(32) NOT NULL COMMENT '用户名称',
      `birthday` datetime default NULL COMMENT '生日',
      `sex` char(1) default NULL COMMENT '性别',
      `address` varchar(256) default NULL COMMENT '地址',
      PRIMARY KEY  (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
    
    
    insert  into `user`(`id`,`username`,`birthday`,`sex`,`address`) values (41,'老王','2018-02-27 17:47:08','男','北京'),(42,'小二王','2018-03-02 15:09:37','女','北京金燕龙'),(43,'小二王','2018-03-04 11:34:34','女','北京金燕龙'),(45,'传智播客','2018-03-04 12:04:06','男','北京金燕龙'),(46,'老王','2018-03-07 17:37:26','男','北京'),(48,'小马宝莉','2018-03-08 11:44:00','女','北京修正');
    ```

  - 在 pom.xml 中添加依赖：

    ```xml
    <packaging>jar</packaging>
        <dependencies>
            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis</artifactId>
                <version>3.4.5</version>
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>5.1.6</version>
            </dependency>
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>1.2.12</version>
            </dependency>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>4.10</version>
            </dependency>
        </dependencies>
    ```

  - 在 java 文件夹下创建实体类 User 和 服务层接口 UserDao：

    ```java
    //User.java
    public class User implements Serializable {
        private Integer id;
        private String username;
        private Date birthday;
        private String sex;
        private String address;
        /*
        	setter & getter
        */
    }
    
    //UserDao.java
    public interface UserDao {
        List<User> findAll();
    }
    ```

  - 在 resources 文件夹下创建 SqlMapConfig.xml 文件：

    - 导入约束：

      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <!DOCTYPE configuration  
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"  
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
      ```

    - 编写主配置文件：

      ```xml
      <!-- Mybatis主配置文件 -->
      <configuration>
          <!-- 配置mysql环境 -->
          <environments default="mysql">
              <environment id="mysql">
                  <!-- 配置事务类型 -->
                  <transactionManager type="JDBC"></transactionManager>
                  <!-- 配置数据源（连接池） -->
                  <dataSource type="POOLED">
                      <!-- 配置连接数据库的四个基本信息 -->
                      <property name="driver" value="com.mysql.jdbc.Driver"/>
                      <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
                      <property name="username" value="root"/>
                      <property name="password" value="root"/>
                  </dataSource>
              </environment>
          </environments>
      
          <!-- 指定映射配置文件的位置，映射配置文件指的是每个Dao独立的配置文件 -->
          <mappers>
              <mapper resource="cn/iwehdio/Dao/UserDao.xml" />
          </mappers>
      </configuration>
      ```

  - 编写针对 UserDao 的配置文件：

    - 在 resources 文件夹下创建 cn.iwehdio.Dao，其中创建 UserDao.xml配置文件，并导入约束：

      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <!DOCTYPE mapper  
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"  
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
      ```

    - 配置查询：

      ```xml
      <mapper namespace="cn.iwehdio.Dao.UserDao">
          <!-- 配置查询所有，id是UserDao中的方法名称,resultType是返回值被封装为哪一个实体类， 其中包含的是sql语句 -->
          <select id="findAll" resultType="cn.iwehdio.Domin.User">
              select * from user;
          </select>
      </mapper>
      ```

- Mybatis环境搭建：

  1. 创建 maven 工程并导入坐标。
  2. 创建实体类和 Dao 接口。
  3. 创建 Mybatis 的主配置文件 SqlMapConfig.xml 。
  4. 创建映射配置文件 UserDao.xml。

  - 注意事项：
    - 创建的 Dao 接口 UserDao.java 和映射配置文件 UserDao.xml 要名称一致。Mybatis 中称其为映射（Mapper）。
    - Mybatis 的映射配置文件位置必须与 Dao 接口的包结构相同。
    - 映射配置文件的 mapper 标签 namespace 属性的值必须是 Dao 接口的全类名。
    - 映射配置文件的操作配置，id 属性的取值必须为 Dao 接口的方法名。
    - 实现以上三个必须，在开发中就无需写 Dao 的实现类，而由Mybatis实现。

- Mybatis入门案例：

  1. 将 log4j.properties 复制到 resources 目录下。

  2. 在 test.java 目录下创建包 cn.iwehdio.test，在其中创建 MybatisTest.java。

     ```java
     public class MybatisTest {
         public static void main(String[] args) throws Exception {
             //1.读取配置文件
             InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
             //2.创建SqlSessionFactory工厂
             SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
             SqlSessionFactory factory = builder.build(in);
             //3.使用工厂生产SqlSession对象
             SqlSession session = factory.openSession();
             //4.使用SqlSession创建Dao接口的代理对象
             UserDao userDao = session.getMapper(UserDao.class);
             //5.使用代理对象执行方法
             List<User> users = userDao.findAll();
             for(User user : users) {
                 System.out.println(user);
             }
             //6.释放资源
             session.close();
             in.close();
         }
     }
     ```

  3. 运行，终端打印出查询到的信息。

- Mybatis入门案例的步骤：

  1. 读取配置文件。

     - 使用类加载器，但是只能读取类路径的配置文件。

     - 使用 ServletContext对象的 getRealPath() 方法。

  2. 创建SqlSessionFactory工厂。

     - Mybatis 使用了构建者模式（builder就是构建者），只需要传入配置。隐藏了对象的创建细节，直接调用方法即可获取对象。

  3. 使用工厂生产 SqlSession 对象。

     - 生产SqlSession 使用了工厂模式。降低了类直接的依赖关系，解耦。

  4. 使用SqlSession创建Dao接口的代理对象。

     - 创建Dao接口实现类使用了代理模式。不修改源码的基础上对已有方法增强。

  5. 使用代理对象执行方法。

  6. 释放资源。

  - 注意事项：
    - 不要忘记在映射配置中告知数据被封装为哪个实体类。

- Mybatis注解代替xml映射配置：

  - 注解可以代替 UserDao.xml 配置文件。

  - 在 UserDao 接口中的 `findAll()`方法上添加注解：

    ```java
    public interface UserDao {
        @Select("select * from user")
        List<User> findAll();
    }
    ```

  - 在 SqlMapConfig.xml 主配置文件中，需要使用class属性指定被注解的Dao接口的全类名替代xml文件的配置。

    ```xml
     <mappers>
        <mapper resource="cn.iwehdio.Dao.UserDao" />
    </mappers>
    ```


- Mybatis是支持写Dao的实现类的。

- Mybatis 在使用代理 Dao 的方式实现增删改查时：
  1. 创建代理对象。
  2.  在代理对象中调用 selectList 方法实现功能。
- Mybatis入门案例的执行流程：
  1. 根据主配置文件中的信息创建 Connection 对象，注册驱动、获取连接。
  2. 根据主配置文件中的 mapper 映射找到 Dao 配置文件。获取预处理对象 PrepareStatement 并传入 SQL 语句。
  3. 执行 PrepareStatement 对象的 executeQuery() 方法进行查询，返回结果集对 ResultSet 。
  4. 封装结果集，将查询到的结果封装到实体类中存入 并返回 list（此处使用了反射）。
- Mybatis入门案例的执行所需要的信息：
  1. 主配置文件中的连接信息。
  2. 主配置文件和Dao 配置文件中的映射信息。
     - 包括执行的SQL语句和封装结果的实体类的全类名。
     - 将这两个信息组合起来定义为一个`Mapper`对象，作为值存入 Map 中，键为 Dao 接口中所要执行方法的名称，即 Dao 接口的全类名（Dao配置文件中的命名空间）+ 方法名。
- Mybatis入门案例中创建代理对象的分析：
  - 根据 Dao 接口的字节码创建 Dao 的代理对象。
    - 使用与被代理对象相同的类加载器。
    - 实现和被代理对象相同的接口。
  - 在增强的接口实现中调用 selectList 实现查询功能。


## 2、自定义编码

> 自定义类完成与Mybatis框架原理相同的代码。

- 根据之前的 MybatisTest.java 文件中的流程进行创建自定义类。

1. 读取配置文件。

   ```java
   //1.读取配置文件
   InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
   ```

   - 在包 mybatis.io 下创建`class Resources`：使用类加载器读取配置文件。根据传入的参数，获取一个字节输入流。

     ```java
     public class Resources {
     
         public static InputStream getResourceAsStream(String filepath) {
             return Resources.class.getClassLoader().getResourceAsStream(filepath);
         }
     }
     ```

2. 创建 SQLSessionFactory 工厂。

   ```java
   //2.创建SqlSessionFactory工厂
   SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
   SqlSessionFactory factory = builder.build(in);
   ```

   - 在包 mybatis.sqlsession 下创建`class SqlSessionFactoryBuilder `：根据参数的字节输入流，创建一个 SqlSessionFactory 对象。

     ```java
     public class SqlSessionFactoryBuilder {
         public SqlSessionFactory build(InputStream config) {
             Configuration cfg = XMLConfigBuilder.loadConfiguration(config);
             return new SqlSessionFactoryImpl(cfg);
         }
     }
     ```

     - 这里返回的是一个`SqlSessionFactory`接口实现类生成的`SqlSession`操作数据库的对象。

     - `build`方法根据传入的配置文件的字节流，使用工具类对XML进行解析，将解析到的数据封装为`Configure`对象。

       ```java
       public class Configuration {
           private String driver;
           private String url;
           private String username;
           private String password;
       
           private Map<String, Mapper> mappers = new HashMap<String, Mapper>();
       
           public Map<String, Mapper> getMappers() {
               return mappers;
           }
       
           public void setMappers(Map<String, Mapper> mappers) {
               this.mappers.putAll(mappers);  //追加而不是覆盖
           }
       	/* getter & setter */
       }
       ```

       - `Configure`对象中，包含了两个部分。一是连接数据库的驱动、url、用户名和密码的配置。二是要进行操作的SQL语句和返回的查询结果所要封装为的实体类对象的全类名，也就是`Mapper`对象。

         ```java
         public class Mapper {
             private String queryString;
             private String resultType;
             	/* getter & setter */
         }
         ```

       - 调用的`XMLConfigBuilder.loadConfiguration(config)`工具类：

         ```java
         public class XMLConfigBuilder {
             /**
              * 解析主配置文件，把里面的内容填充到SqlSessionImpl所需要的地方
              * 使用的技术：
              *      dom4j+xpath
              */
             public static Configuration loadConfiguration(InputStream config){
                 try{
                     //定义封装连接信息的配置对象（mybatis的配置对象）
                     Configuration cfg = new Configuration();
         
                     //1.获取SAXReader对象
                     SAXReader reader = new SAXReader();
                     //2.根据字节输入流获取Document对象
                     Document document = reader.read(config);
                     //3.获取根节点
                     Element root = document.getRootElement();
                     //4.使用xpath中选择指定节点的方式，获取所有property节点
                     List<Element> propertyElements = root.selectNodes("//property");
                     //5.遍历节点
                     for(Element propertyElement : propertyElements){
                         //判断节点是连接数据库的哪部分信息
                         //取出name属性的值
                         String name = propertyElement.attributeValue("name");
                         if("driver".equals(name)){
                             //表示驱动
                             //获取property标签value属性的值
                             String driver = propertyElement.attributeValue("value");
                             cfg.setDriver(driver);
                         }
                         if("url".equals(name)){
                             //表示连接字符串
                             //获取property标签value属性的值
                             String url = propertyElement.attributeValue("value");
                             cfg.setUrl(url);
                         }
                         if("username".equals(name)){
                             //表示用户名
                             //获取property标签value属性的值
                             String username = propertyElement.attributeValue("value");
                             cfg.setUsername(username);
                         }
                         if("password".equals(name)){
                             //表示密码
                             //获取property标签value属性的值
                             String password = propertyElement.attributeValue("value");
                             cfg.setPassword(password);
                         }
                     }
                     //取出mappers中的所有mapper标签，判断他们使用了resource还是class属性
                     List<Element> mapperElements = root.selectNodes("//mappers/mapper");
                     //遍历集合
                     for(Element mapperElement : mapperElements){
                         //判断mapperElement使用的是哪个属性
                         Attribute attribute = mapperElement.attribute("resource");
                         if(attribute != null){
                             System.out.println("使用的是XML");
                             //表示有resource属性，用的是XML
                             //取出属性的值
                             String mapperPath = attribute.getValue();//获取属性的值"com/itheima/dao/IUserDao.xml"
                             //把映射配置文件的内容获取出来，封装成一个map
                             Map<String,Mapper> mappers = loadMapperConfiguration(mapperPath);
                             //给configuration中的mappers赋值
                             cfg.setMappers(mappers);
                         }
                     }
                     //返回Configuration
                     return cfg;
                 }catch(Exception e){
                     throw new RuntimeException(e);
                 }finally{
                     try {
                         config.close();
                     }catch(Exception e){
                         e.printStackTrace();
                     }
                 }
             }
         
             /**
              * 根据传入的参数，解析XML，并且封装到Map中
              * @param mapperPath    映射配置文件的位置
              * @return  map中包含了获取的唯一标识（key是由dao的全限定类名和方法名组成）
              *          以及执行所需的必要信息（value是一个Mapper对象，里面存放的是执行的SQL语句和要封装的实体类全限定类名）
              */
             private static Map<String,Mapper> loadMapperConfiguration(String mapperPath)throws IOException {
                 InputStream in = null;
                 try{
                     //定义返回值对象
                     Map<String,Mapper> mappers = new HashMap<String,Mapper>();
                     //1.根据路径获取字节输入流
                     in = Resources.getResourceAsStream(mapperPath);
                     //2.根据字节输入流获取Document对象
                     SAXReader reader = new SAXReader();
                     Document document = reader.read(in);
                     //3.获取根节点
                     Element root = document.getRootElement();
                     //4.获取根节点的namespace属性取值
                     String namespace = root.attributeValue("namespace");//是组成map中key的部分
                     //5.获取所有的select节点
                     List<Element> selectElements = root.selectNodes("//select");
                     //6.遍历select节点集合
                     for(Element selectElement : selectElements){
                         //取出id属性的值      组成map中key的部分
                         String id = selectElement.attributeValue("id");
                         //取出resultType属性的值  组成map中value的部分
                         String resultType = selectElement.attributeValue("resultType");
                         //取出文本内容            组成map中value的部分
                         String queryString = selectElement.getText();
                         //创建Key
                         String key = namespace+"."+id;
                         //创建Value
                         Mapper mapper = new Mapper();
                         mapper.setQueryString(queryString);
                         mapper.setResultType(resultType);
                         //把key和value存入mappers中
                         mappers.put(key,mapper);
                     }
                     return mappers;
                 }catch(Exception e){
                     throw new RuntimeException(e);
                 }finally{
                     in.close();
                 }
             }
         }
         ```

3.  使用工厂生产`SqlSession`对象。

   ```java
   //3.使用工厂生产SqlSession对象
   SqlSession session = factory.openSession();
   ```

   -  `SqlSessionFactory` 是一个接口，其下有一个返回新的`sqlsession`对象的方法：

     ```java
     public interface SqlSessionFactory {
         //用于打开新的sqlsession对象
         public SqlSession openSession();
     }
     ```

   - `SqlSessionFactoryImpl`是上边接口的实现类，读入了配置文件，并且将配置文件传入了`openSession()`方法，使用`sqlsession`的实现类`sqlsessionImpl`生产操作数据库的对象。

     ```java
     //SqlSessionFactory的实现类
     public class SqlSessionFactoryImpl implements SqlSessionFactory{
         private Configuration cfg;
     
         public SqlSessionFactoryImpl(Configuration cfg) {
             this.cfg = cfg;
         }
         //创建一个新的操作数据库对象
         public SqlSession openSession() {
             return new SqlSessionImpl(cfg);
         }
     }
     ```

4. 使用`SqlSession`创建Dao接口的代理对象。

   ```java
   //3.使用工厂生产SqlSession对象
   SqlSession session = factory.openSession();
   ```

   

   - `sqlsession`是与数据库交互的核心类，其下有一个获取`Mapper`即获取SQL语句和封装目标实体类的方法和释放资源的方法。

     ```java
     //与数据库交互的核心类
     public interface SqlSession {
         <T> T getMapper(Class<T> daoInterfaceClass);
         public void close();
     }
     ```

   - `sqlsessionImpl`是`sqlsession`的实现类，构造方法中根据传入的配置获取连接。`getMapper`方法根据传入的`UserDao`接口创建代理对象，传入方法增强的实现类`MapperProxy`。

     ```java
     public class SqlSessionImpl implements SqlSession {
     
         private Configuration cfg;
         private Connection connection;
     
         public SqlSessionImpl(Configuration cfg) {
             this.cfg = cfg;
             this.connection = DataSourceUtil.getConnection(cfg);
         }
         //创建代理对象
         public <T> T getMapper(Class<T> daoInterfaceClass) {
             return (T)Proxy.newProxyInstance(daoInterfaceClass.getClassLoader(),
                     new Class[]{daoInterfaceClass},
                     new MapperProxy(cfg.getMappers(), connection));
         }
         //释放资源
         public void close() {
             if(connection != null) {
                 try {
                     connection.close();
                 } catch (SQLException e) {
                     e.printStackTrace();
                 }
             }
         }
     }
     ```

5. 使用代理对象执行方法。

      ```java
      //5.使用代理对象执行方法
      List<User> users = userDao.findAll();
      ```

      - 方法增强的实现类`MapperProxy`中对`UserDao`中的`findAll()`方法进行增强，最后调用工具类`Executor`中的`selectList()`方法进行查询，传入`Mapper`对象组成的表`mapper`和连接`conn`。

        ```java
        public class MapperProxy implements InvocationHandler {
        
            private Map<String, Mapper> mappers;
            private Connection conn;
        
            public MapperProxy(Map<String, Mapper> mappers, Connection conn) {
                this.mappers = mappers;
                this.conn = conn;
            }
        
            //用于对方法进行增强，调用selectList方法
            public Object invoke(Object o, Method method, Object[] objects) throws Throwable {
                //1、获取方法名
                String methodName = method.getName();
                //2、获取方法所在类的名称
                String className = method.getDeclaringClass().getName();
                //3、组合key
                String key = className + "." + methodName;
                //4、获取Map中的Mapper对象
                Mapper mapper = mappers.get(key);
                //5、判断是否有mapper
                if(mapper == null) {
                    throw new IllegalAccessException("传入参数有误");
                }
                //6、调用工具类执行查询
                return new Executor().selectList(mapper, conn);
            }
        }
        ```

      - 工具类`Executor`执行SQL语句并封装结果。

        ```java
        public class Executor {
        
            public <E> List<E> selectList(Mapper mapper, Connection conn) {
                PreparedStatement pstm = null;
                ResultSet rs = null;
                try {
                    //1.取出mapper中的数据
                    String queryString = mapper.getQueryString();//select * from user
                    String resultType = mapper.getResultType();//com.itheima.domain.User
                    Class domainClass = Class.forName(resultType);
                    //2.获取PreparedStatement对象
                    pstm = conn.prepareStatement(queryString);
                    //3.执行SQL语句，获取结果集
                    rs = pstm.executeQuery();
                    //4.封装结果集
                    List<E> list = new ArrayList<E>();//定义返回值
                    while(rs.next()) {
                        //实例化要封装的实体类对象
                        E obj = (E)domainClass.newInstance();
        
                        //取出结果集的元信息：ResultSetMetaData
                        ResultSetMetaData rsmd = rs.getMetaData();
                        //取出总列数
                        int columnCount = rsmd.getColumnCount();
                        //遍历总列数
                        for (int i = 1; i <= columnCount; i++) {
                            //获取每列的名称，列名的序号是从1开始的
                            String columnName = rsmd.getColumnName(i);
                            //根据得到列名，获取每列的值
                            Object columnValue = rs.getObject(columnName);
                            //给obj赋值：使用Java内省机制（借助PropertyDescriptor实现属性的封装）
                            PropertyDescriptor pd = new PropertyDescriptor(columnName,domainClass);//要求：实体类的属性和数据库表的列名保持一种
                            //获取它的写入方法
                            Method writeMethod = pd.getWriteMethod();
                            //把获取的列的值，给对象赋值
                            writeMethod.invoke(obj,columnValue);
                        }
                        //把赋好值的对象加入到集合中
                        list.add(obj);
                    }
                    return list;
                } catch (Exception e) {
                    throw new RuntimeException(e);
                } finally {
                    release(pstm,rs);
                }
            }
        
        
            private void release(PreparedStatement pstm,ResultSet rs){
                if(rs != null){
                    try {
                        rs.close();
                    }catch(Exception e){
                        e.printStackTrace();
                    }
                }
        
                if(pstm != null){
                    try {
                        pstm.close();
                    }catch(Exception e){
                        e.printStackTrace();
                    }
                }
            }
        }
        ```

      - `sqlsessionImpl`中需要与数据库的连接对象`conn`，通过工具类`DataSourceUtils`获取。

        ```java
        //用于创建数据源的工具类
        public class DataSourceUtil {
            public static Connection getConnection(Configuration cfg) {
                try {
                    Class.forName(cfg.getDriver());
                    return DriverManager.getConnection(cfg.getUrl(), cfg.getUsername(), cfg.getPassword());
                } catch (Exception e) {
                    throw new RuntimeException(e);
                }
            }
        }
        ```


- 体会一下构建者模式（`SqlSessionFactoryBuilder.build()`）、工厂模式（`SqlSessionFactory.openSession()`）和代理者模式（`SqlSessiongetMapper()`）的使用。

   
