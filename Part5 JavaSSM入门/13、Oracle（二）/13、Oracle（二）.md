## 1、视图和索引

- 视图：提供一个查询的窗口，所有数据来自原表。改变视图数据也会修改原表。
- 创建视图必须有dba权限。
- 跨用户查询创建表：`create table emp as select * from scott.emp`。
- 创建视图：`create view v_emp as select ename,job from emp`。
- 查询视图：`select * from v_emp`。
- 修改视图：`update v_emp set job='CLERK' where ename = 'ALLEN'`，+ commit。
- 视图的作用：
  - 屏蔽一些敏感的字段。
  - 保证总部和分部的数据及时统一。
- 索引：在表的列上构建一个二叉树，达到大幅增加查询效率的目的，但是会影响增删改的效率。
- 单列索引：
  - 创建：`create index idx_ename on emp(ename)`。
  - 单列索引触发规则：条件必须是索引列中的原始值。单行函数、模糊函数不会触发索引。
- 复合索引：
  - 创建：`create index idx_enamejob on emp(ename, job)`。
  - 复合索引中第一列为优先检索列，如果要触发复合索引，必须包含优先检索列中的原始值。用 or 做查询条件时不触发。
  - 又是单列索引又是复合索引，先触发单列索引。



## 2、PLSQL

- PLSQL语言是对sql语言的扩展，使得其具有过程化编程的特性。主要用来编写存储过程和存储函数等。

- 声明方法：

  ```plsql
  declare
  -- 定义变量
  begin
  -- 处理业务逻辑
  end;
  ```

- 赋值操作：

  - 定义变量必须在declare 下。

  - 赋值数字：`i number(2) := 10;`。

  - 赋值字符串：`s varchar2(10) := '名'`。

  - 输出：`dbms_output.put_line(i)`。

  - 输入：`i number(3) := &i`

  - 赋值指定表中列的类型（引用型变量）：

    ```plsql
    declare
           ena emp.ename%type;
    begin
           select ename into ena from emp where empno =7788;
           dbms_output.put_line(ena);
    end;
    ```

  - 赋值指定表中某一行的类型（记录性变量）：

    ```plsql
    declare
           emprow emp%rowtype;
    begin
           select * into emprow from emp where empno =7788;
           dbms_output.put_line(emprow.ename || '的工作是' || emprow.job);
    end;
    ```

- 流程控制语句：

  - 判断语句格式：

    ```plsql
    declare
    -- 定义变量
    begin
    	if i <18 then
    		dbms_output.put_line('a')
    	elsif i <60 then
    		dbms_output.put_line('b')
    	else
    		dbms_output.put_line('c')
    	end if;
    end;
    ```

  - 循环语句格式：

    ```plsql
    -- 方法1
    while i <11 loop
    	dbms_output.put_line(i)
    	i := i+1;
    end loop;
    -- 方法2
    loop
    	exit when i >10;
    	dbms_output.put_line(i)
    	i := i+1;
    end loop;
    -- 方法3
    for i in 1..10 loop
    	dbms_output.put_line(i);
    end loop;
    ```

- PLSQL中的游标：

  - 类似于集合，可以存储多行记录。

  - 输出emp表中所有员工的姓名：

    ```plsql
    declare
           cursor c1 is select * from emp;
           emprow emp%rowtype;
    begin
           open c1;
           loop
             fetch c1 into emprow;
             exit when c1%notfound;
             dbms_output.put_line(emprow.ename);
           end loop;
           close c1;
    end;
    ```

  - 更改指定部门员工的工资：

    ```plsql
    declare
           cursor c2(eno emp.deptno%type) is select empno from emp where deptno=eno;
           en emp.empno%type;
    begin
           open c2(10);
           loop
             fetch c2 into en;
             exit when c2%notfound;
             update emp set sal=sal+100 where empno=en;
             commit;
           end loop;
           close c2;
    end;
    ```

- 存储过程：

  - 就是提前已经编译好的一段PLSQL语言，放置在数据库端，可以直接被调用。一般都是固定步骤的业务。

  - 语法：

    ```plsql
    create [or replace] procedure 过程名[(参数名 in/out 数据类型)]
    as
    begin
    	-- 业务程序；
    end；
    ```

  - 给指定员工涨工资的存储过程：

    ```plsql
    create or replace procedure p1(eno emp.empno%type)
    is
    begin
           update emp set sal=sal+100 where empno = eno;
           commit;
    end;
    ```

  - 测试存储过程：

    ```plsql
    declare
    begin
      p1(7788);
    end;
    ```

  - out 类型参数：

    - 定义：`yearsal out number `。
    - 获取输出：`p1(7788,yearsal)`。
    - 涉及到 into 或 := 赋值的参数都用out 来修饰。

- 存储函数：

  - 一般来说，过程和函数的区别在于函数可以有一个返回值，而过程没有返回值。

  - 语法：

    ```plsql
    create or replace function 函数名(参数名 数据类型) return 数据类型 is
    	结果变量 数据类型;
    begin
    	--业务程序
    	return(结果变量);
    end 函数名;
    ```

  - 计算指定员工年薪的存储函数：

    ```plsql
    create or replace function f_year(eno emp.empno%type) return number
    is
           s number(10);
    begin
           select sal*12 + nvl(comm,0) into s from emp where empno=eno;
           return s;
    end;
    ```

  - 测试存储函数：

    ```plsql
    declare
      s number(10);
    begin
      s:=f_year(7788);
      dbms_output.put_line(s);
    end;
    ```

- 存储过程和存储函数的区别：
  - 语法区别：关键字不同。
  - 存储函数比存储过程多了两个return。
  - 本质区别：存储函数有返回值，而存储过程没有返回值。
  - 如果存储过程需要返回值，需要out 类型参数。但本质也不是真有返回值，而是在存储过程内部给out 参数赋值。
  - 存储函数可以利用有返回值的特性，来自定义函数（如聚合函数），而存储过程不能用来自定义函数。
  
- 触发器：

  - 制定一个规则，在进行增删改操作的时候，只要满足规则，自动触发，无需调用。
  - 分为语句级触发器和行级触发器。
  - 行级触发器中含有`for each row`，为了使用:oid 或 :new 对象。
  - 插入操作时，:old 所有字段都是空，:new 是将要插入的数据。
  - 更新操作时，:old 是更新前该行的值，:new 是更新后的值。
  - 删除操作时，:old 是删除前该行的值，:new 所有字段为空。

- 创建触发器：

  - 语句级触发器：插入一条记录，输出一个新员工入职。

    ```plsql
    create or replace trigger t1
    after		-- 在操作前还是操作后
    insert		-- 对应的操作
    on person	-- 对应的表
    declare
    
    begin
      dbms_output.put_line('入职');
    end;
    ```

  - 测试触发器：

    ```plsql
    insert into person values (2,'小绿');
    commit;
    select * from person;
    ```

  - 行级触发器：不能给员工降薪。

    ```plsql
    create or replace trigger t2
    before		-- 在操作之前
    update
    on emp
    for each row
    declare
    
    begin
      if :old.sal > :new.sal then
        raise_application_error(-20001,'不能降薪');
      end if;
    end;
    ```

  - 测试触发器：

    ```plsql
    update emp set sal = sal -100 where empno = 7788;
    commit;
    select * from emp where empno = 7788;
    ```

- 触发器实现主键自增：

  - 在用户插入操作之前，拿到即将插入的数据，给数据的主键列赋值。使用序列。

    ```plsql
    create or replace trigger auid
    before
    insert
    on person
    for each row
    declare
    
    begin
      select s_person.nextval into :new.pid from dual;
    end;
    ```

  - 测试触发器：

    ```plsql
    insert into person (pname) values ('a');
    commit;
    select * from person;
    ```



## 3、Java调用存储过程/函数

- Oracle10g的驱动包为ojdbc14.jar，Oracle11g的驱动包为ojdbc6.jar。

- maven 坐标：

  ```xml
  <dependencies>
      <dependency>
          <groupId>com.oracle</groupId>
          <artifactId>ojdbc14</artifactId>
          <version>10.2.0.4.0</version>
          <scope>runtime</scope>
      </dependency>
  
      <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <version>4.10</version>
          <scope>test</scope>
      </dependency>
  </dependencies>
  ```

- 测试查询：

  ```java
  @Test
  public void test1() throws Exception {
      //加载数据库驱动
      Class.forName("oracle.jdbc.driver.OracleDriver");
      //获取连接
      Connection connection = DriverManager.getConnection("jdbc:oracle:thin:@iwehdio:1521:orcl", "iwehdio0", "hhh");
      //得到预编译对象并赋值
      PreparedStatement preparedStatement = connection.prepareStatement("select * from person where pid = ?");
      preparedStatement.setObject(1, "21");
      ResultSet resultSet = preparedStatement.executeQuery();
      while (resultSet.next()) {
          System.out.println(resultSet.getString("pname"));
      }
      resultSet.close();
      preparedStatement.close();
      connection.close();
  }
  ```

- 调用存储过程：

  ```java
  @Test
  public void test2() throws Exception {
      //加载数据库驱动
      Class.forName("oracle.jdbc.driver.OracleDriver");
      //获取连接
      Connection connection = DriverManager.getConnection("jdbc:oracle:thin:@iwehdio:1521:orcl", "scott", "tiger");
      //得到对象并赋值
      CallableStatement callableStatement = connection.prepareCall("{call p1(?)}");
      callableStatement.setObject(1, "7788");
      callableStatement.execute();
      callableStatement.close();
      connection.close();
  
  }
  ```

- 调用存储函数：

  ```java
  @Test
  public void test3() throws Exception {
      //加载数据库驱动
      Class.forName("oracle.jdbc.driver.OracleDriver");
      //获取连接
      Connection connection = DriverManager.getConnection("jdbc:oracle:thin:@iwehdio:1521:orcl", "scott", "tiger");
      //得到对象并赋值
      CallableStatement callableStatement = connection.prepareCall("{? = call f_year(?)}");
      callableStatement.setObject(2, "7788");
      callableStatement.registerOutParameter(1, OracleTypes.NUMBER);
      callableStatement.execute();
      //获得第一个参数并输出
      System.out.println(callableStatement.getObject(1));
      callableStatement.close();
      connection.close();
  }
  ```

- 调用存储过程时，sql语句写`{call 过程名(参数)}`。

- 调用存储函数时，sql语句写`{? = call 函数名(参数)}`。

  - 存储函数的第一个？属于参数，需要注册指定为Oracle中的那个类型。
  - 使用`.getObject(第几个)`方法获得返回值。

