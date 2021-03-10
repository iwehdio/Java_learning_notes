## 1、概述

- DB数据库（database）：存储数据的“仓库”。它保存了一系列有组织的数据。
- DBMS数据库管理系统（Database Management System）。数据库是通过DBMS创建操作的容器。
- SQL结构化查询语言（Structure Query Language）：专门用来与数据库通信的语言。
- 数据库存储数据的特点：
  - 数据存放到表中，然后表再放到库中
  - 一个库中可以有多张表，每张表具有唯一的表名用来标识自己
  - 表中有一个或多个列，列又称为“字段”，相当于java中“属性”
  - 表中的每一行数据，相当于java中“对象”
- `show databases`：查看所有数据库。
- `use 库名`：使用某个数据库。
- `show tables from 库名`：查看数据库中的所有表。
- `select database()`：查看当前所在的数据库。
- `desc 表名`：查看表的结构。
- `select version()`：查看当前版本。



## 2、DQL查询

- 数据的查询。
  
- 基础查询：
  
  - `select 查询列表 from 表名`：从表中查询。
    - 查询列表可以是表中的字段、常量、表达式和函数等。
    - 查询的结果是一个虚拟的表格。
    - 查询多个字段，在查询列表中用逗号隔开。
    - `*`可以表示所有字段。
  - `select distinct 查询列表 from 表名`：去除查询结果中的重复部分。
    - 所有字段都完全相同才算是重复。
  - 例：`SELECT DISTINCT salary FROM employees`。
  
- 为字段起别名：
  - `select 查询列名 As 别名 from 表名`。
  - `select 查询列名 别名 from 表名`。
  - 如果要查询的字段有重名的情况，使用别名可以进行区分。
  - 在from后为表起别名也是同理，但是为表起别名后，其原有的表名就无法使用了。
  
- `+`号的作用：
  
  - 在MySQL中，+号的作用是作为运算符，而无法作为连接符。
  - 两个数值类型，则作加法运算。
  - 一个数值一个字符，则试图将字符型数值转换为数值型。如果转换失败，则字符型看作0。
  - 如果有一个是null，则结果也为null。
  
- 连接符的作用由`concat(字段1，字段2)`函数实现。

  - 例：`SELECT CONCAT(first_name,last_name) FROM employees`。

- 判断是否为null：

  - `ifnull(字段,新值)`：对字段进行判断，如果不为null则返回字段原本的值，如果为null则返回新值。
  - `isnull(字段)`：对字段进行判断，如果不为null则返回1，如果为null则返回0。

- 条件查询：
  - `select 查询列表 from 表名 where 筛选条件`：查询符合条件的结果。
  - 执行过程：先定位到表名，再按条件进行筛选，最后进行查询。
  - 筛选条件：
    - 条件表达式：使用条件运算符`<`、`>`、`<>`、`=`、`!=`、`>=`、`<=`。
    - 逻辑表达式：使用逻辑运算符`&&`、`||`、`!`、and、or、not。
    - 例：`SELECT * FROM employees WHERE salary>10000 AND salary<20000`。
    - 使用模糊查询：like、between and、in、is null/is not null。
      - like：使用`%`（任意多个字符）、`_`（任意单个字符）通配符。通配符无法匹配null，但是也可以对数值型进行通配。
      - 例：`SELECT * FROM employees WHERE first_name LIKE '_a%'`。
      - 通配符转义一般使用`\`，也可以用escape指定。
      - between and：包含临界值，但两个临界值不能颠倒（等价于大于等于左侧值，小于等于右侧值）。
      - in：判断某字段的值是否与列表中的某一项匹配（等价于使用多个=号，不支持通配符）。
      - 例：`SELECT * FROM employees WHERE job_id IN('IT_PROG','FI_MGR')`。
      - is null/is not null：使用=/<>号不能匹配null值。
    - 安全等于`<=>`：可以匹配普通值和null值。
  
- 排序查询：

  - `select 查询列表 from 表名 order by 排序列表 ASC/DESC`：根据排序列表排序，并且指定或升序降序。排序列表还支持表达式、函数和别名。
  - 例：`SELECT * FROM employees WHERE department_id>=90 ORDER BY hiredate ASC`。
  - `length(字段)`：获取某个字段对应的各行值的长度。
  - 排序列表也可以包含多个字段，先按先写的字段排序。
  - order by字句一般在语句的最后，但在limit字句之前。

- 常见函数：

  - `select 函数名(实参) from 表名`。

  - 单行函数：

    - 字符函数：

      - `length(字段)`：获取字段对应的字节个数。
      - `concat(字段1，字段2)`：拼接字符串。
      - `upper(字段)`：全部变为大写。
      - `lower(字段)`：全部变为消息。
      - `substr(字段，起始索引，截取长度)`：截取字符串，索引从1开始，长度指的是字符长度。
      - `instr(字段1，字段2)`：返回字段2在字段1中的第一次出现的起始索引。找不到返回0。
      - `trim(去除字符 from 字段)`：去除字段中前后为去除字符的内容，不指定去除字符则默认为去除空格。
      - `lpad(字段，总长度，填充字符)`：用指定的字符实现左填充到指定长度。`rpad`同理。
      - `replace(字段，被替换字符，新字符)`：替换字段中的指定字符。

    - 数学函数：

      - `round(字段，小数点保留位数)`：四舍五入。
      - `ceil(字段)`：向上取整。
      - `floor(字段)`：向下取整。
      - `truncate(字段，小数点保留位数)`：截断小数。
      - `mod(字段，被除数)`：取余。
      - `rand()`：获取0到1之间的随机数。

    - 日期函数：

      - `now()`：返回当前系统日期和时间。
      - `curdate()`：返回当前日期。
      - `curtime()`：返回当前时间。
      - `year(日期字段)`：返回日期的年。同理还有月、日等。
      - `str_to_date(字符串，格式)`：将字符串按指定格式转换为日期。
      - `date_format(日期字段，格式)`：将日期转换为指定格式的字符串。
      - `datediff(日期字段1，日期字段2)`：两个日期相差的天数。

    - 流程控制函数：

      - `if(条件表达式，结果1，结果2)`：根据条件表达式，true返回结果1，false返回结果2。

      - `case`类似switch case的结构，相当于多个where条件查询：

        ```mysql
        case 要判断的字段或表达式
        when 常量1 then 要显示的值1
        when 常量2 then 要显示的值2
        else 要显示的值3
        end
        ```

      - `case`类似多重if else语句：

        ```mysql
        case
        when 条件1 then 要显示的值1
        when 条件2 then 要显示的值2
        else 要显示的值3
        end
        ```

  - 分组函数（聚合函数）：

    - `sum(字段)`：求和。
    - `avg(字段)`：平均值。
    - `max(字段)`：最大值。
    - `min(字段)`：最小值。
    - `count(字段)`：个数。字段为`*`可以统计行数。字段为1，也有同样的效果。
    - 以上分组函数都忽略null值。
    - `sum(distinct 字段)`：去重后再求和。

- 分组查询：

  - `select 分组函数，分组字段 from 表名 group by 分组字段`。
  - 例：`SELECT MAX(salary),job_id FROM employees GROUP BY job_id`。
  - 如果`where 筛选条件`在`group by`之前，是先筛选再分组查询。
  - 如果`having 筛选条件`在`group by`之后，是先分组查询再筛选。
  - group by后可以放多个字段，就可以按多个字段分组。
  - group by、having和order by后可以用别名，但是where后不行。

- 连接查询（多表查询）：

  - 可以用`表名.字段名`区分不同表中的同名字段。
  - sql92内连接：
    - 等值连接：
      - `select 查询列表 from 表1，表2 where 连接条件`。
      - 在where后的连接条件后，可以再用and连接其他筛选条件。
      - 多表等值连接的结果为多表的交集部分。
      - n表连接至少需要n-1个连接条件。
    - 非等值连接：
      - 与等值连接不同的是，等值连接中连接条件用等号=，非等值连接使用等号以外的条件运算符。
    - 自连接：
      - 所要进行连接查询的两张表都是同一张表。
      - 需要在from后边写两次这张表，并且起不同的别名。
  - sql99内连接：
    - 等值连接：
      - `select 查询列表 from 表1 inner join 表2 on 连接条件 where 筛选条件`。
      - 将on后的连接条件和where后的筛选条件分开了。
      - 等值连接中两个表的顺序无关。  但是有两个表以上时，连接的两个表要有直接关联（而不是由中间表关联的）。
      - inner关键字可以省略，与sql92中的等值连接效果相同。
    - 非等值连接和自连接类型。
  - 外连接：
    - 外连接是分主从表的，查询结果会将主表中的数据全部显示出来，即使其在从表中没有匹配。
    - 即结果为内连接结果+主表中有而从表中没有的记录。
    - 主表中有而从表中没有的记录，从表部分用null填充
    - 左外连接：
      - `select 查询列表 from 主表 left outer join 从表 on 连接条件 where 筛选条件`。
    - 右外连接：
      - `select 查询列表 from 从表 right outer join 主表 on 连接条件 where 筛选条件`。
    - 左外连接和右外连接的区别就是主表和从表的位置不同。
    - 全外连接：
      - `select 查询列表 from 表1 full outer join 表2 on 连接条件 where 筛选条件`。
      - 相当于两个表都分别做了一次主表和从表的相对角色，就是除了交集部分，表1和表2中独有的部分也会被查询出来。
      - 相当于表1和表2的并集。但在MySQL中不支持。
  - 交叉连接：
    - `select 查询列表 from 表1 cross join 表2 on 连接条件 where 筛选条件`。
    - 相当于表1和表2的笛卡尔乘积。

- 子查询：

  - 出现在其他语句中的select语句。

  - 分类：

    - 按子查询出现的位置：
      - select后面：支持标量子查询。
      - from后面：支持表子查询。
      - where或having后面：支持标量子查询、列子查询、行子查询。
      - exists后面（相关子查询）：支持表子查询。
    - 按结果集的行列数不同：
      - 标量子查询（结果集只有一行一列）
      - 列子查询（结果集只有一列多行）
      - 行子查询（结果集有一行多列）
      - 表子查询（结果集一般为多行多列）

  - where或having后面：

    - 特点：
      ①子查询放在小括号内
      
      ②子查询一般放在条件的右侧
      
      ③标量子查询，一般搭配着单行操作符使用：>、<、>=、<=、=、<>。
      
      ④列子查询，一般搭配着多行操作符使用in、any/some、all。
      
      ⑤子查询的执行优先于主查询的执行（exist后除外）。
      
    - 标量子查询，例：
    
      ```mysql
      # 找出部门最低工资低于50号部门最低工资的部门
      SELECT MIN(salary) 
      FROM employees 
      GROUP BY department_id 
      HAVING MIN(salary)>
      (SELECT MIN(salary) 
       FROM employees 
       WHERE department_id=50
      );
      ```
    
    - 列子查询：
    
      - 多行比较操作符：
    
        - IN/NOT IN：等于子查询结果中的任意一个。
        - ANY/SOME：子查询中的结果有一个符合即可。
        - ALL：子查询中的结果需要全部符合。
    
      - 例：
    
        ```mysql
        # 找出其他工种中比'IT_PROG'工种中任一工资都低的人的数据
        SELECT employee_id,last_name,job_id,salary 
        FROM employees 
        WHERE salary<ANY(
        	SELECT DISTINCT salary
            FROM employees 
            WHERE job_id='IT_PROG'
        ) 
        AND job_id<>'IT_PROG';
        ```
    
    - 行子查询，例：
    
      ```mysql
      # 查询员工编号最小并且工资最高的员工
      SELECT * 
      FROM employees 
      WHERE (employee_id,salary)=(
      	SELECT MIN(employee_id),MAX(salary) 	FROM employees 
      );
      ```
    
      - 有局限性，一般只用等于，不等于只看第一项。
    
  - select后面：

    - 例：

      ```mysql
      # 查询每个部门的员工个数
      SELECT d.*,(
          SELECT COUNT(*) 
          FROM employees e 
          WHERE e.department_id=d.department_id)
      FROM departments d;
      ```

    - 相当于遍历d的每一行时，都将d.department_id传入子查询并查询出数量。

  - from后面：

    - 例：

      ```mysql
      # 查询每个部门的平均工资的工资等级
      SELECT ag_dep.*,g.grade_level
      FROM (
          SELECT AVG(salary) ag,department_id 	FROM employees 
          GROUP BY department_id) ag_dep
      INNER JOIN job_grades g 
      ON ag_dep.ag BETWEEN lowest_sal AND highest_sal;
      ```

    - 子查询生成的表和聚合函数生成的列，想要再使用就要起别名。

  - exist后面：

    - exist就是看子查询的结果是否有结果，有就是1，没有就是0。

    - exist前也可以写NOT取反。

    - 例：

      ```mysql
      # 查询有员工的部门名
      SELECT department_name 
      FROM departments d 
      WHERE EXISTS(
      	SELECT * 
          FROM employees e 
          WHERE e.department_id=d.department_id);
      ```

- 分页查询：

  - `select 查询列表 from 表 limit 起始索引,条目个数`。
  - 起始索引从0开始，如果省略默认为0。
  - 无论在语法上还是执行顺序上都是最后。

- 联合查询：

  - 将多条查询语句的结果合并成一个结果，即取并集。
  - 比如用于要查询的结果来自多个表，但是表之间没有直接的连接关系。
  - `查询语句1 union 查询语句2`。
  - 联合查询中查询列表中查询的列数必须一致。
  - 每一列的类型最好是一致的。
  - 使用联合查询是默认去重的，取消去重需要使用union all。

- 查询语句中的各个部分的执行顺序：

  ```mysql
  select 查询列表    ⑦
  from 表1 别名       ①
  连接类型 join 表2   ②
  on 连接条件         ③
  where 筛选          ④
  group by 分组列表   ⑤
  having 筛选         ⑥
  order by 排序列表    ⑧
  limit 起始条目索引，条目数;  ⑨
  ```

  - select语句的执行顺序可以参考查询列表中有聚合函数时。



## 2、DML

- 数据的插入、修改、删除。
- 插入语句：
  - `insert into 表名(列名,...) value(值,...)`。
  - 插入的值的类型要与列的类型一致或兼容。
  - 表名后的列名可以省略，默认是所有列按顺序插入。
  - value后可以有多个`()`进行插入。
  - 也可以用一个子查询替换`value()`进行插入。
  - `insert into 表名 set 列名=值`。
- 修改语句：
  - 修改单表：
    - `update 表名 set 列=新值 where 筛选条件`。
  - 修改多表：
    - `update 表1 连接类型 join 表2 on 连接条件 set 列名=值 where 筛选条件`。
- 删除语句：
  - 删除单表：
    - `delete from 表名 where 筛选条件`。
    - `truncate table 表名`。
    - truncate table会删除整个表，不能接where。
  - 删除多表：
    - `delete 所要删掉的表 from 表1 连接类型 join 表2 on 连接条件 where 筛选条件`。



