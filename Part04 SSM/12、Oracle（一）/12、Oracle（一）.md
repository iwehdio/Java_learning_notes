## 1、Oracle入门

- Oracle体系结构：

  - 数据库：Oracle数据库的物理存储。
  - 实例：一个实例由一系列的后台进程和内存结构组成。一个数据库可以有n个实例。
  - 用户：用户是在实例下建立的，不同实例可以建相同名字的用户。用户是管理表的基本单位。
  - 表空间：一个数据库在逻辑上被划分为一个到若干个表空间。一个表空间中可以有多个用户。
  - 数据文件：是数据库的物理存储单位，数据库的数据存储在表空间，真正是存在数据文件中。

- 创建表空间：

  - 必须有超级管理员的权限。

  ```sql
  -- 创建表空间,名为iwehdio
  create tablespace iwehdio
  -- 数据文件的位置
  datafile 'd:\oracle\oracle.dbf'
  -- 大小100m，不足时自动增长，每次增长10m
  size 100m
  autoextend on
  next 10m;
  ```

- 删除表空间：`drop tablespace iwehdio`。

- 创建用户：

  ```sql
  -- 创建用户
  create user iwehdio0
  -- 密码
  identified by hhh
  -- 关联表空间
  default tablespace iwehdio;
  ```

- 给用户授权：

  - 常用角色：
    - connect ：连接角色，基本角色。
    - resource ：开发者角色。
    - dba ：超级管理员角色。
  - 给用户授权：`grant dba to iwehdio0`。
  - 切换用户：在Session下先 log off 再 log on。



## 2、建表和增删改

- Oracle数据类型：

  - Varchar / varchar2 ：表示一个字符串。后边写`()`中表示长度。常用的是varchar2。
  - NUMBER ：`NUMBER(n)`表示一个长度为n的整数，`NUMBER(m，n)`表示一个小数，总长度m，小数n。
  - DATA ：表示日期类型。
  - CLOB：大对象文本数据类型。
  - BLOB：大对虾二进制数据。

- 建表：

  ```sql
  -- 创建表，名称在前类型在后
  create table person(
         pid number(20),
         pname varchar2(10)
  );
  ```

- 修改表结构：

  - 添加一列：`alter table person add (gender number(1))`，添加多列时在括号中由逗号分隔。
  - 修改列的类型：`alter table person modify (gender char(1))`，修改多列时在括号中由逗号分隔。
  - 修改列的名称：`alter table person rename column gender to sex`。
  - 删除一列：`alter table person drop column sex`。

- 数据的增删改：

  - 添加数据：`insert into person (pid,pname) values (1,'小明')`。
  - 修改数据：`update person set pname = 'ming' where pid = 1`。
  - 删除数据：
    - `delete from person`：删除表中所有记录。
    - `drop table person`：删除表结构。
    - `truncate table person`：先删除表，再次创建表。
  - 增删改之后，都要用`commit`提交数据。
  
- 序列的使用：

  - 默认从1开始，依次递增，用来给主键赋值使用。
  - 序列不是真的属于任何一张表，但是可以逻辑和表做绑定。
  - 创建序列：`create sequence s_person`。
  - 查询序列：`select s_person.nextval from dual`。 `dual`代表虚表，只是为了补全语法，没有任何意义。
  - 一次增加几：`increment by n`。
  - 从几开始：`start with n`。

- scott 用户：

  - 用户名 scott，密码 tiger。

  - 解锁scott用户：

    - 解锁用户：`alter user scott account unlock`。
    - 解锁密码：`alter user scott identified by tiger`。


## 3、查询

  - 单行函数：作用于一行，返回一个值。

    - 字符函数：
      - 把小写转换为大写：`select upper('yes') from dual`。
      - 把大写转换为小写：`select lower('YEs') from dual`。
    - 数值函数：
      - 四舍五入，参数为保留的位数：`select round(26.18,1) from dual`。
      - 直接截取：`select trunc(26.18,1) from dual`。
      - 求余：`select mod(10,3) from dual`。
    - 日期函数：
      - 查询与今天距离的天数：`select sysdate-e.hiredate from emp e`。
      - 查询明天此刻：`select sysdate+1 from dual`。
      - 查询与今天距离的月数：`select months_between(sysdate,e.hiredate) from emp e`。
    - 转换函数：
      - 日期转换为字符串：`select to_char(sysdate,'yyyy-mm-dd hh:mi:ss')from dual`。24小时格式为hh24。
      - 字符串转日期：`select to_date('2020-06-27 07:17:44','yyyy-mm-dd hh:mi:ss')from dual`。
    - 通用函数：
      - nvl函数，如果为null则选取第二个值：`select e.sal*12+nvl(e.comm,0) from emp e`。

  - 多行函数：作用于多行，返回一个值。

    - 查询总数量：`select count(1) from emp`。
    - 查询总和：`select sum(sal) from emp`。
    - 查询最大：`select max(sal) from emp`。
    - 查询最低：`select min(sal) from emp`。
    - 查询平均：`select avg(sal) from emp`。

  - 条件表达式：

    - 为emp表中的人取别名（相等判断时）：

      ```sql
      select e.ename,
             case e.ename
               when 'SMITH' then '曹'
                 when 'ALLEN' then '孙'
                   else '刘'
                     end
      from emp e
      ```

    - case 是所要比对的字段，else 可以没有，但end 必须有。

    - 为emp表中的人划分收入（不等判断时）：

      ```sql
      select e.sal,
             case 
               when e.sal>3000 then '高'
                 when e.sal>1500 then '中'
                   else '低'
                     end
      from emp e
      ```

    - case 后不写字段。

    - Oracle专用条件表达式：

      ```sql
      select e.ename,
             decode(e.ename,
                'SMITH' , '曹',
                  'ALLEN' , '孙',
                    '刘') "别名"
      from emp e
      ```

    - Oracle中除了起别名，都用单引号。起别名可以用双引号或者不用引号。

  - 分组查询：

    - 查询每个部门的平均工资：

      ```sql
      select e.deptno,avg(e.sal)
      from emp e
      group by e.deptno
      ```

    - 分组查询中，出现在 group by 后面的原始列，才能出现在 select 后。其他的列要出现在 select 后必须加上多行函数。

    - 查询高于2000的工资的部门：

      ```sql
      select e.deptno,avg(e.sal)
      from emp e
      group by e.deptno
      having avg(e.sal)>2000
      ```

    - 在条件字段中，不能使用别名。

    - 查询每个部门工资高于800的员工的平均工资：

      ```sql
      select e.deptno,avg(e.sal)
      from emp e
      where e.sal >800
      group by e.deptno
      ```

    - where 条件是在  group by之前，having 条件是在 group by 之后。 

- 多表查询中的概念：

  - 笛卡尔积：两张表中的所有记录匹配相乘。

  - 等值连接（内连接）：在笛卡尔积的基础上，使用where 进行相等条件匹配。

  - 外连接：按那个表中的主键作为主列。

  - Oracle中专用外连接：

    ```sql
    select *
    from emp e,dept d
    where e.deptno(+) = d.deptno
    ```

  - (+) 在左边就是右外连接，在右边就是左外连接。

- 自连接：

  - 查询出员工姓名和其领导姓名：

    ```sql
    select e1.ename, e2.ename
    from emp e1, emp e2
    where e1.mgr = e2.empno
    ```

  - e1是员工表，e2是领导表。

  - 自连接就是站在不同角度，把一张表看作多张表。

  - 查询出员工姓名及其所属部门和其领导姓名及其所属部门：

    ```sql
    select e1.ename, d1.dname, e2.ename, d2.dname
    from emp e1, emp e2, dept d1, dept d2
    where e1.mgr = e2.empno
    and e1.deptno = d1.deptno
    and e2.deptno = d2.deptno
    ```

- 子查询：

  - 子查询返回一个值：

    - 查询出工资和scott一样的员工信息：

      ```sql
      select * from emp where sal = 
      (select sal from emp where ename = 'SCOTT')
      ```

  - 子查询返回一个集合：

    - 查询出工资和10号部门任意员工一样的员工信息：

      ```sql
      select * from emp where sal in
      (select sal from emp where deptno = 10)
      ```

  - 子查询返回一张表：

    - 查询出每个部门最低工资和最低工资员工姓名和所在部门名称：

      ```sql
      select  t.deptno,t.msal,e.ename,d.dname
      from emp e, dept d,(select deptno,min(sal) msal 
                          from emp e 
                          group by deptno) t
      where t.deptno = e.deptno
      and t.msal = e.sal
      and e.deptno = d.deptno
      ```

- 分页查询：

  -  mp表工资倒序排列：

    ```sql
    select rownum, t.* from (
    select * from emp e order by e.sal desc) t
    ```

  - emp表工资倒序排列后，每页五条记录，查询第二页：

    ```sql
    select * from(
      select rownum rn, t.* from (
      select * from emp e order by e.sal desc) t
      where rownum < 11
    ) where rn > 5
    ```

  - rownum是行号，每次查询出一条记录就加一个行号。

  - 排序操作会影响rownum的顺序。

  - rownum 不能写大于一个正数，因为其从1开始无法满足。

