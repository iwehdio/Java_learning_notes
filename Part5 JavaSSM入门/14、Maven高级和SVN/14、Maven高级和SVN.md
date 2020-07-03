## 1、创建maven工程

- 在 web.xml 下添加约束：

  ```xml
  <web-app xmlns="http://java.sun.com/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
            http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
           version="3.0">
    
  </web-app>
  ```

- maven 工程要导入jar包的坐标，就要考虑jar 包冲突。

  - 解决冲突的方式一：

    - 第一声明优先原则，那个jar 包的坐标靠上，这个包就行先声明的。
    - 先声明的依赖包可以优先进入项目中。

  - 解决冲突的方式二：

    - 直接依赖：项目中直接导入的jar 包就是该项目的直接jar 包。
    - 传递依赖：项目中没有直接导入的包，通过直接依赖传递到项目中。
    - 路径近者优先原则，直接依赖比传递依赖路径近，把传递依赖转为直接依赖可以解决冲突。

  - 解决冲突的方式三：

    - 直接排除法：排除某个坐标的间接依赖包。

      ```xml
      <exclusions>
          <exclusion>
              <groupId>org.springframework</groupId>
              <artifactId>spring-core</artifactId>
          </exclusion>
      </exclusions>
      ```

- 导入解决冲突的 SSM的jar包坐标：

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
  
      <groupId>org.example</groupId>
      <artifactId>JuHeMavenTest</artifactId>
      <packaging>pom</packaging>
      <version>1.0-SNAPSHOT</version>
      <modules>
          <module>Dao</module>
          <module>Service</module>
          <module>Web</module>
      </modules>
  
      <!-- 统一管理jar包版本 -->
      <properties>
          <spring.version>5.0.2.RELEASE</spring.version>
          <slf4j.version>1.6.6</slf4j.version>
          <log4j.version>1.2.12</log4j.version>
          <shiro.version>1.2.3</shiro.version>
          <mysql.version>5.1.6</mysql.version>
          <mybatis.version>3.4.5</mybatis.version>
          <spring.security.version>5.0.1.RELEASE</spring.security.version>
      </properties>
  
      <!-- 锁定jar包版本 -->
      <dependencyManagement>
          <dependencies>
              <dependency>
                  <groupId>org.springframework</groupId>
                  <artifactId>spring-context</artifactId>
                  <version>${spring.version}</version>
              </dependency>
              <dependency>
                  <groupId>org.springframework</groupId>
                  <artifactId>spring-web</artifactId>
                  <version>${spring.version}</version>
              </dependency>
              <dependency>
                  <groupId>org.springframework</groupId>
                  <artifactId>spring-webmvc</artifactId>
                  <version>${spring.version}</version>
              </dependency>
              <dependency>
                  <groupId>org.springframework</groupId>
                  <artifactId>spring-tx</artifactId>
                  <version>${spring.version}</version>
              </dependency>
              <dependency>
                  <groupId>org.springframework</groupId>
                  <artifactId>spring-test</artifactId>
                  <version>${spring.version}</version>
              </dependency>
              <dependency>
                  <groupId>org.mybatis</groupId>
                  <artifactId>mybatis</artifactId>
                  <version>${mybatis.version}</version>
              </dependency>
          </dependencies>
      </dependencyManagement>
  
      <!-- 项目依赖jar包 -->
      <dependencies>
          <!-- spring -->
          <dependency>
              <groupId>org.aspectj</groupId>
              <artifactId>aspectjweaver</artifactId>
              <version>1.6.8</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-aop</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-context</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-context-support</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-web</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-orm</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-beans</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-core</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-test</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-webmvc</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-tx</artifactId>
              <version>${spring.version}</version>
          </dependency>
          <dependency>
              <groupId>junit</groupId>
              <artifactId>junit</artifactId>
              <version>4.12</version>
              <scope>test</scope>
          </dependency>
          <dependency>
              <groupId>mysql</groupId>
              <artifactId>mysql-connector-java</artifactId>
              <version>${mysql.version}</version>
          </dependency>
          <dependency>
              <groupId>javax.servlet</groupId>
              <artifactId>javax.servlet-api</artifactId>
              <version>3.1.0</version>
              <scope>provided</scope>
          </dependency>
          <dependency>
              <groupId>javax.servlet.jsp</groupId>
              <artifactId>jsp-api</artifactId>
              <version>2.0</version>
              <scope>provided</scope>
          </dependency>
          <dependency>
              <groupId>jstl</groupId>
              <artifactId>jstl</artifactId>
              <version>1.2</version>
          </dependency>
          <!-- log start -->
          <dependency>
              <groupId>log4j</groupId>
              <artifactId>log4j</artifactId>
              <version>${log4j.version}</version>
          </dependency>
          <dependency>
              <groupId>org.slf4j</groupId>
              <artifactId>slf4j-api</artifactId>
              <version>${slf4j.version}</version>
          </dependency>
          <dependency>
              <groupId>org.slf4j</groupId>
              <artifactId>slf4j-log4j12</artifactId>
              <version>${slf4j.version}</version>
          </dependency>
          <!-- log end -->
          <dependency>
              <groupId>org.mybatis</groupId>
              <artifactId>mybatis</artifactId>
              <version>${mybatis.version}</version>
          </dependency>
          <dependency>
              <groupId>org.mybatis</groupId>
              <artifactId>mybatis-spring</artifactId>
              <version>1.3.0</version>
          </dependency>
          <dependency>
              <groupId>c3p0</groupId>
              <artifactId>c3p0</artifactId>
              <version>0.9.1.2</version>
              <type>jar</type>
              <scope>compile</scope>
          </dependency>
          <dependency>
              <groupId>com.github.pagehelper</groupId>
              <artifactId>pagehelper</artifactId>
              <version>5.1.2</version>
          </dependency>
          <dependency>
              <groupId>org.springframework.security</groupId>
              <artifactId>spring-security-web</artifactId>
              <version>${spring.security.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework.security</groupId>
              <artifactId>spring-security-config</artifactId>
              <version>${spring.security.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework.security</groupId>
              <artifactId>spring-security-core</artifactId>
              <version>${spring.security.version}</version>
          </dependency>
          <dependency>
              <groupId>org.springframework.security</groupId>
              <artifactId>spring-security-taglibs</artifactId>
              <version>${spring.security.version}</version>
          </dependency>
          <dependency>
              <groupId>com.alibaba</groupId>
              <artifactId>druid</artifactId>
              <version>1.0.9</version>
          </dependency>
      </dependencies>
      <!-- 添加tomcat7插件 -->
      <build>
          <plugins>
              <plugin>
                  <groupId>org.apache.tomcat.maven</groupId>
                  <artifactId>tomcat7-maven-plugin</artifactId>
                  <version>2.2</version>
              </plugin>
              <plugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-compiler-plugin</artifactId>
                  <version>2.0.2</version>
                  <configuration>
                      <source>1.11</source>
                      <target>1.11</target>
                  </configuration>
              </plugin>
          </plugins>
      </build>
  
  </project>
  ```

- `<dependencyManagement>`标签中的依赖只用来锁定jar 包版本，不用来导入依赖。

- SSM下的WEB案例：

  - Items实体类：

    ```java
    public class Items {
        private Integer id;
        private String name;
        private double price;
        private String pic;
        private Date createtime;
        private String detail;
    }
    ```

  - Dao接口：

    ```java
    public interface ItemsDao {
        public Items findById(Integer id);
    }
    ```

  - Mybatis管理的ItemsDao.xml：

    ```xml
    <mapper namespace="cn.iwehdio.dao.ItemsDao">
        <select id="findById" parameterType="int" resultType="items">
            select * from items where id = #{id}
        </select>
    </mapper>
    ```

  - Spring管理的Dao层配置文件：

    ```xml
    <!-- Dao层配置文件 -->
    <!-- druid连接池 -->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
        <property name="url" value="jdbc:mysql:///maven"></property>
        <property name="username" value="root"></property>
        <property name="password" value="root"></property>
    </bean>
    <!-- 配置SqlSessionFactory -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
        <!-- 扫描包，给pojo对象起别名 -->
        <property name="typeAliasesPackage" value="cn.iwehdio.domain"></property>
    </bean>
    <!-- 扫描接口包路径，生成代理对象并放入Spring容器中 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="cn.iwehdio.dao"></property>
    </bean>
    ```

  - ItemsService及其实现类：

    ```java
    public interface ItemsService {
        public Items findById(Integer id);
    }
    
    @Service
    public class ItemsServiceImpl implements ItemsService {
        @Autowired
        private ItemsDao itemsDao;
        @Override
        public Items findById(Integer id) {
            return itemsDao.findById(id);
        }
    }
    ```

  - Spring管理的Service层配置文件：

    ```xml
    <!-- Service层配置文件 -->
    <!-- 扫描注解 -->
    <context:component-scan base-package="cn.iwehdio"></context:component-scan>
    <!-- 配置事务管理器 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <!-- 配置事务通知 -->
    <tx:advice id="advice">
        <tx:attributes>
            <tx:method name="save*" propagation="REQUIRED" ></tx:method>
            <tx:method name="update*" propagation="REQUIRED"></tx:method>
            <tx:method name="delete*" propagation="REQUIRED"></tx:method>
            <tx:method name="find*" read-only="true"></tx:method>
        </tx:attributes>
    </tx:advice>
    <!-- 配置切面 -->
    <aop:config>
        <aop:pointcut id="pointcut" expression="execution(* cn.iwehdio.service.Impl.*.*(..))"/>
        <aop:advisor advice-ref="advice" pointcut-ref="pointcut"></aop:advisor>
    </aop:config>
    ```

  - ItemsController控制器：

    ```java
    @Controller
    @RequestMapping("/items")
    public class ItemsController {
    
        @Autowired(required = false)
        private ItemsService itemsService;
    
        @RequestMapping("/findDetail")
        public String findDetail(Model model) {
            Items items = itemsService.findById(1);
            model.addAttribute("item", items);
            return "itemDetail";
        }
    }
    ```

  - springmvc.xml配置文件：

    ```xml
    <!-- 组件扫描 -->
        <context:component-scan base-package="cn.iwehdio.controller"></context:component-scan>
    
        <!-- 处理器映射器，处理器适配器 -->
        <mvc:annotation-driven></mvc:annotation-driven>
        <!-- 视图解析器 -->
        <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
            <property name="prefix" value="/WEB-INF/pages/"></property>
            <property name="suffix" value=".jsp"></property>
        </bean>
        <!-- 释放静态资源 -->
        <mvc:default-servlet-handler></mvc:default-servlet-handler>
    ```

  - web.xml按之前配置Servlet、过滤器、监听器。

  - itemDetail.jsp：

    ```jsp
    <body> 
    	<form>
    		<table width="100%" border=1>
    			<tr>
    				<td>商品名称</td>
    				<td> ${item.name } </td>
    			</tr>
    			<tr>
    				<td>商品价格</td>
    				<td> ${item.price } </td>
    			</tr>
    			<tr>
    				<td>生成日期</td>
    				<td> <fmt:formatDate value="${item.createtime}" pattern="yyyy-MM-dd HH:mm:ss"/> </td>
    			</tr>
    			<tr>
    				<td>商品简介</td>
    				<td>${item.detail} </td>
    			</tr>
    		</table>
    	</form>
    </body>
    </html>
    ```




## 2、maven工程的拆分聚合

-  不同工程中有相同的dao层数据库查询操作。应该不同的工程公用一套代码。

- maven解决代码可重用问题：把一个完整的项目分成不同的独立模块，这些模块都有各自独立的坐标，那个地方需要其中某个模块，直接引用该模块的坐标即可。

- 拆分零散的模块也可以聚合到一起，成为一个完整的项目。

- 创建父工程：创建一个只有pom.xml的maven父工程。

- 创建子工程：在父工程下创建模块为maven子工程。

- 父工程中module标签标记子工程，子工程中parent标签标记父工程。

- 工程与模块：

  - 工程与模块都不等于一个完整的项目。

  - 完整的项目是看代码是否完整。

  - 工程创建后只能使用自己内部的资源，是独立的。创建后可以与其他工程或模块建立关联。

  - 模块创建和不是独立的，而是属于父工程，可以使用父工程的所有资源。

  - 子模块之间被创建后是没有任何关系的，但可以建立关系。

  - 子模块引入其他子模块的依赖：

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.example</groupId>
            <artifactId>mavenDao</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
    </dependencies>
    ```

  - 子工程对于父工程下导入的包是传递依赖，这些包存在作用域时可能在子工程中导不进去，需要再导一次。

- 编写各个子工程：

  - 在子工程的pom文件中填写依赖关系。

  - 在各个子工程中按照dao、service、web层编写代码。

  - 在resources目录下创建spring目录，在其中分别放置dao部分的配置applicationContext-dao.xml和service部分的配置applicationContext-service.xml。

  - 在web层中同时也放入jsp页面和web.xml配置文件。在applicationContext.xml中引入以上两个文件：

    ```xml
    <import resource="classpath:/spring/applicationContext-dao.xml"></import>
    <import resource="classpath:/spring/applicationContext-service.xml"></import>
    ```

- maven父子工程的三种启动方式：

  - 在父工程的tomcat插件中启动。
  - 在web子工程的tomcat插件中启动，前提是父工程已经使用插件install。
  - 使用外部tomcat启动。
  
-  私服：

   - 安装：管理员权限运行并安装nexus。
   - 启动：浏览器输入 localhost:8081/nexus，登录界面。
   - 连接：在maven的setting.xml中的`<serve>`标签中配置连接私服的信息。
   - 上传配置：将相关配置放在所要上传的模块的pom.xml下。
   - 上传：在maven插件中执行deploy。
   - 下载配置：将相关配置放在maven的setting.xml中，并激活。

-  安装第三方jar包到本地仓库：

   - 进入jar包所在目录：

     ```shell
     mvn install:install-file -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.1.37 -Dfile=fastjson-1.1.37.jar -Dpackaging=jar
     ```

   - 不进入jar包所在目录，直接cmd：

     ```shell
     mvn install:install-file -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.1.37 -Dpackaging=jar -Dfile=C:\my_java\授课资料\资料：maven【高级】\安装第三方jar包\fastjson-1.1.37.jar
     ```



## 3、SVN

- SVN是一个版本控制器。将文件存放在中心版本库，可以记录每一次文件和目录的修改情况。
- 把项目源码下载到本地：checkout。
- 把本地源码更新为服务器上的最新版本：update。
- 将本地源码更新内容提交到服务器：commit。
- 用户共享信息并且不互相干扰：
  - 复制-修改-合并方案。
  - 锁定-修改-解锁方案。
- 运行方式：独立服务器或借助apache。
- 存储版本数据的方式：BDB和FSFS。
- 服务器visualSVN：创建仓库和用户。
- 客户端TortoiseSVN：
  - 浏览仓库：
    - 创建文件夹。
    - 右键菜单TortoiseSVN下的repo-browser。
    - 输入仓库的url，用户名和密码。
  - checkout：
    - 右键菜单SVN checkout。
  - add：
    - 右键菜单TortoiseSVN下的add。
  - commit：
    - 右键菜单SVN commit。
  - 更新到历史版本：
    - 右键菜单TortoiseSVN下的show log。
    - 右键点击update item to revisor。 
  - delete：
    - 右键菜单TortoiseSVN下的delete。
    - 再commit一下。
  - 导入和导出。
    - import的和export。
- 冲突：多个用户同时操作同一个文件造成的。
  - 原文件：本地和服务器上文件合并后的结果。
  - .mine后缀：本地修改后的文件。
  - .r前一个版本号：修改前的文件。
  - .r后一个版本号：修改后的文件。
- 解决冲突：
  - 右键菜单TortoiseSVN下的edit conflicts。
  - 手动解决冲突。
- 避免冲突：每次修改前先update。
- IDEA下的SVN配置：
  - settings -> Version control -> Subversion。
  - 指定svn.exe的位置。
  - 安装时需要选择command line。
  - 提交到服务器：VCS -> import int version control -> share project（subversion）。
  - 添加仓库路径。
- SVN目录规范：
  - Trunk：主干目录，此目录下的文件为基准文件。
  - Branches：用于开发的分支目录。
  - Tags：用于发布的版本目录。
- SVN分支：
  - 分支的定义规则：Protect name + 日期时间 + 功能点。
  - Tags的定义规则：Project name + 版本号。
  - 添加到主干：share project时选择放到trunk下。
  - 打分支：右键菜单TortoiseSVN下的branch/tags。然后保存到指定路径下。
  - 主干合并到分支：
    - 首先保证主干是最新的（update）。
    - 在分支下右键菜单TortoiseSVN下的merge。
    - url选择主干的url，working copy选择分支的位置。



## 4、AdminLTE

- 依赖于Bootstrap3和JQ1.11，用于构建后台页面。
- 将页面分为四部分：
  - .wrapper包住了body下的所有代码。
  - .main-header里是网站的logo和导航栏的代码。
  - .main-sidebar里是用户面板和侧边栏菜单的代码。
  - .content-wrapper里是页面的页面内容区域代码。
  - .main-footer是页脚代码。
  - .control-sidebar里是页面右侧侧边栏区域代码。







