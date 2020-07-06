# 3、Mybatis连接池、动态SQL

## 1、连接池

- 可以减少获取连接的次数，减少获取连接消耗的时间。
- 就是一个存储连接的一个容器，其实就是一个集合。该集合必须是线程安全的，而且必须实现队列的特性。
- Mybatis中的连接池：
  - 提供了三种方式的配置。
  - 需要配置在主配置文件中的`<dataSource>`标签中，`type`属性表示使用何种连接池方式。
  - `type`属性的取值可以为`POOLED`（采用传统的javax.sql.DataSource规范）、`UNPOOLED`（采用传统的获取连接的方式，没有使用池的思想）和`JNDI`（采用服务器提供的JNDI技术实现获取DataSource对象，不同服务器获取的对象不同，对于Tomcat是dbcp连接池）。
  - 使用`POOLED`配置连接：
    - 连接池分为空闲池和活动池。
    - 先判断空闲池中还有没有空闲的连接`connection`。
    - 如果有空闲的连接则取出一个连接。
    - 如果没有空闲的连接，如果活动池中连接的数量小于设定的最大值，则新建一个连接。
    - 如果以上都不成立，则获取活动池中最先建立的连接。
- Mybatis中的事务：
  - 通过`sqlsession`中的`commit()`和`rollback()`方法实现提交和回滚。
  - 将事务改为自动提交`factory.openSesseion(true)`。

## 2、动态SQL

- `<if>`标签：

  - 根据不同条件进行拼接语句。

  - 使用`test`属性进行判断，将所要拼接的SQL语句放在`<if>`标签内。

  - 例如`UserDao`接口其实现为：

    ```java
    @Override
    public List<User> findByCondition(User user) {
        SqlSession sqlSession = factory.openSession();
        List<User> users = sqlSession.selectList("cn.iwehdio.Dao.UserDao.findByCondition", user);
        sqlSession.close();
        return users;
    }
    ```

  - `UserDao.xml`中配置：

    ```xml
    <!-- 动态SQL -->
    <select id="findByCondition" parameterType="cn.iwehdio.Domin.User" resultType="cn.iwehdio.Domin.User">
        select * from user where 1=1
        <if test="username != null">
            and username = #{username};
        </if>
    </select>
    ```

  - 测试类：

    ```java
    @Test
    public void testfindByCondition() {
        User user = new User();
        user.setUsername("老王");
    
        List<User> users = userDao.findByCondition(user);
        for(User u : users) {
            System.out.println(u);
        }
    }
    ```

- `<where>`标签：

  - 其中可以包含`<if>`标签，不需要`where 1=1`了。

  - 即：

    ```xml
    <!-- 动态SQL -->
    <select id="findByCondition" parameterType="cn.iwehdio.Domin.User" resultType="cn.iwehdio.Domin.User">
        select * from user
        <where>
            <if test="username != null">
                username = #{username};
            </if>
        </where>
    </select>
    ```

    

## 3、Mybatis中的多表

- 表之间的关系：

  - 一对多。如用户的订单。
  - 多对一。如订单的用户。Mybatis将多对一看作多个一对一。
  - 一对一。如身份证号。
  - 多对多。如老师和学生。

- 一对一的查询：

  - 以用户和账户为例。一个用户可以有多个账户。

  - 步骤：

    1. 建立两张表，用户表和账户表。使用外键在账户表中添加。
    2. 建立两个实体类，让用户和账户的实体类体现出一对多的关系。
    3. 建立用户表和账户表的配置文件。
    4. 实现配置，查询用户可以获得账户信息，反之亦然。

  - 创建 account 表：

    ```mysql
    CREATE TABLE `account` (
      `ID` int(11) NOT NULL COMMENT '编号',
      `UID` int(11) default NULL COMMENT '用户编号',
      `MONEY` double default NULL COMMENT '金额',
      PRIMARY KEY  (`ID`),
      KEY `FK_Reference_8` (`UID`),
      CONSTRAINT `FK_Reference_8` FOREIGN KEY (`UID`) REFERENCES `user` (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
    
    
    insert  into `account`(`ID`,`UID`,`MONEY`) values (1,46,1000),(2,45,1000),(3,46,2000);
    ```

  - 创建`Account`实体类：

    ```java
    public class Account implements Serializable {
        private Integer id;
        private Integer uid;
        private Double money;
        /* getter & setter */
    }
    ```

  - 创建`AccountDao`接口：

    ```java
    public interface AccountDao {
        List<Account> findAll();
    }
    ```

  - 配置`AccountDao.xml`配置文件：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper
      PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
      "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="cn.iwehdio.Dao.AccountDao">
    
        <select id="findAll" resultType="cn.iwehdio.Domin.Account">
            select * from account;
        </select>
    
    </mapper>
    ```

  - 在主配置文件中注册配置：

    ```xml
    <mapper resource="cn/iwehdio/Dao/AccountDao.xml" />
    ```

  - 需求：

    - 查询所有账户时，还要获取当前账户的所属用户信息。

    - 实现的SQL语句：

      ```mysql
      select u.*, a.id as aid, a.uid, a.money from account a, user u where u.id = a.id; 
      ```

  - 通过写`Account`的子类`AccountUser`的方式查询.。在`toString()`方法中也调用父类的方法：

    ```java
    public class AccountUser extends Account {
    
        private String username;
        private String address;
    
         /* getter & setter */
    
        @Override
        public String toString() {
            return super.toString() + "  AccountUser{" +
                    "username='" + username + '\'' +
                    ", address='" + address + '\'' +
                    '}';
        }
    }
    ```

    - 进行相关配置和测试类的编写。

  - 通过建立实体类关系的方式查询。

    - 从表实体应该包含一个主表实体的对象引用。

      ```java
      public class Account implements Serializable {
          private Integer id;
          private Integer uid;
          private Double money;
      
          private User user;
      }
      ```

    - 在配置文件中定义封装account和user的resultMap：

      ```xml
      <resultMap id="accountUserMap" type="cn.iwehdio.Domin.Account">
              <id property="id" column="aid"></id>
              <result property="uid" column="uid"></result>
              <result property="money" column="money"></result>
      
              <association property="user" column="uid" javaType="cn.iwehdio.Domin.User">
                  <id property="id" column="id"></id>
                  <result property="username" column="username"></result>
                  <result property="address" column="address"></result>
                  <result property="sex" column="sex"></result>
                  <result property="birthday" column="birthday"></result>
              </association>
          </resultMap>
      ```

      - `<association>`标签表示了实体类之间的关系。
      - `javaType`属性表示了所要封装的实体类结果。

    - 指定所要使用的resultMap：

      ```xml
      <select id="findAll" resultMap="accountUserMap">
          select a.*, u.* from account a, user u where u.id = a.uid;
      </select>
      ```

    - 测试类：

      ```java
      @Test
      public void testfindall() {
      
          List<Account> accounts = accountDao.findAll();
          for(Account account : accounts) {
              System.out.println(account);
              System.out.println(account.getUser());
          }
      
      }
      ```

- 一对多的查询：

  - 需求：

    - 同时获取用户下所有账户的信息。

  - 一对多关系映射，主表实体应该包含从表实体的集合引用。`User`实体类中：

    ```java
    public class User implements Serializable {
        private Integer id;
        private String username;
        private Date birthday;
        private String sex;
        private String address;
        
        private List<Account> accountList;
    }
    ```

  - 在配置文件`UserDao.xml`中配置Accounts集合的映射：

    ```xml
    <resultMap id="userAccountMap" type="cn.iwehdio.Domin.User">
        <id property="id" column="id"></id>
        <result property="username" column="username"></result>
        <result property="address" column="address"></result>
        <result property="sex" column="sex"></result>
        <result property="birthday" column="birthday"></result>
        <collection property="accountList" ofType="cn.iwehdio.Domin.Account">
            <id property="id" column="aid"></id>
            <result property="uid" column="uid"></result>
            <result property="money" column="money"></result>
        </collection>
    
    </resultMap>
    
    <!-- 配置查询所有，id是UserDao中的方法名称, 其中包含的是sql语句 -->
    <select id="findAll" resultMap="userAccountMap">
        select * from user u left join account a on u.id = a.uid;
    </select>
    ```

  - 测试类：

    ```java
    @Test
    public void testfindall() {
    
        List<User> users = userDao.findAll();
        for(User user : users) {
            System.out.println(user);
            System.out.println(user.getAccountList());
        }
    
    }
    ```

- 多对多的查询：

  - 需要使用中间表，其中包含各自表的主键，在中间表中是外键。

  - 实体类中各自包含对方的一个集合引用。

  - 创建实体类`Role`：

    ```java
    public class Role implements Serializable {
        private Integer roleId;
        private String roleName;
        private String roleDesc;
        
    }
    ```

  - 配置映射`RoleDao.xml`：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="cn.iwehdio.Dao.RoleDao">
        <resultMap id="roleMap" type="cn.iwehdio.Domin.Role">
            <id property="roleId" column="id"></id>
            <result property="roleName" column="role_name"></result>
            <result property="roleDesc" column="role_desc"></result>
            <collection property="users" ofType="cn.iwehdio.Domin.User">
                <id property="id" column="aid"></id>
                <result property="username" column="username"></result>
                <result property="address" column="address"></result>
                <result property="sex" column="sex"></result>
                <result property="birthday" column="birthday"></result>
            </collection>
        </resultMap>
    
        <select id="findAll" resultMap="roleMap">
            select u.*,r.id as rid,r.role_name,r.role_desc from role r left join user_role ur on r.id = ur.rid
            left outer join user u on u.id = ur.uid;
        </select>
    </mapper>
    ```

  - 测试类：

    ```java
    @Test
    public void testfindall() {
    
        List<Role> roles = roleDao.findAll();
        for(Role role : roles) {
            System.out.println(role);
            System.out.println(role.getUsers());
        }
    
    }
    ```

  - 查询用户获取角色信息的SQL语句：

    ```mysql
    select u.*, r.id as rid, r.role_name,r.role_desc from user u left outer join user_role ur on u.id = ur.uid left outer join role r on r.id = ur.rid
    ```

