# 26、MySQL基础

## 1、MySQL数据库

> 数据库：DataBase，简称 DB 。用于存储和管理数据的仓库。

- 数据库的特点：
  1. 持久化存储数据。其实数据库就是一个文件系统。
  2. 方便存储和管理数据。
  3. 使用了统一的方式来操作数据库（SQL）。
- MySQL 服务启动：
  - 使用管理员打开命令行 cmd 。
  - 使用命令 `net start mysql`：启动 mysql 服务。
  - 使用命令`net stop mysql`：关闭 mysql 服务。
- MySQL 的登录和退出：
  - 登录：
    1. `mysql -uroot -proot`：本地登陆，两个 root 分别是用户名和密码。
    2. `mysql -h127.0.0.1 -uroot -proot`：远程连接，127.0.0.1 是所要远程连接的 IP 地址。
    3. `mysql --host=127.0.0.1 --uesr==root --password=root`：远程连接。
  - 退出：`exit`和`quit`命令。
- MySQL 的目录结构：
  - 安装目录：
    - bin 下是二进制的可执行文件。
    - share 下是报错信息。
    - my.ini 是配置文件。
  - 数据目录：
    - data 下的文件夹对于数据库。
    - 文件夹下的 .frm 文件对应数据库中的表。
    - .frm 文件中的内容对应数据库中的数据记录。

## 2、SQL

> SQL是结构化查询语言（Structured Query Language），是定义了所有关系型数据库的语言。但是每一种具体的数据库操作方式也有差别。

- SQL 通用语法：

  - SQL 语句可以单行或多行书写，以分号结尾。
  - 可使用空格和缩进提高语句可读性。
  - MySQL 数据库不区分大小写，但是关键字建议大写。
  - 注释：
    - 单行注释1：`-- 注释内容`。（--后有一个空格）
    - 单行注释2：`# 注释内容`。
    - 多行注释：`/*注释内容*/`。

- SQL 分类：

  1. Data Definition Language (DDL数据定义语言) ，用来定义数据库对象：数据库、表、列表。
  2. Data Manipulation Language(DML数据操纵语言)，用来对表中的记录操作增删改。
  3. Data Query Language(DQL 数据查询语言)，用来查询表中的记录（数据）。
  4. Data Control Language(DCL 数据控制语言)，用来定义数据库的访问权限和安全级别。

- DDL：操作数据库和表。

  1. 操作数据库：CRUD。

     -  C（Creat）：创建。
       - `create database 数据库名`：创建数据库。
       - `create database if not exists 数据库名`：创建数据库前先判断该数据库名是否存在。
       - `creat database 数据库名 character set 字符集`：设置指定字符集的数据库。
     - R（Retrieve）：查询。
       - `show databases`：查询所有数据库的名称。
       - `show create database 数据库名`：查看数据库的创建语句（可以查询数据库的字符集）。
     - U（Update）：修改。
       - `alter database 数据库名 character set 字符集`：修改数据库的字符集。
     - D（Delete）：删除。
       - `drop database 数据库名`：删除数据库。
       - `drop database if exists 数据库名`：删除前判断数据库是否存在。
     - 使用数据库：
       - `select database()`：查询当前正在使用的数据库。
       - `use 数据库名`：使用数据库。

  2. 操作表：CRUD。

     -  C（Creat）：创建。

       - ```mysql
         create table 表名(
         	列名1 数据类型1,
         	列名n 数据类型n) ;
         # 列由逗号分隔，最后一列后不需要逗号
         ```

       - `create table 表名2 like 表名1`：创建一个表1的复制表2。

       - 数据类型：

         1. int 整数类型。`int` 。
         2. double 小数类型。`double(m, n)`，共 m 位数字，小数点后 n 位。
         3. date 日期，包含年月日。`yyyy-MM--dd`。
         4. datetime 日期，包含年月日时分秒。`yyyy-MM-dd HH:mmLss `。
         5. timestamp 时间戳类型，包含年月日时分秒。`yyyy-MM-dd HH:mmLss `。如果将来不给这个字段幅值，或者赋值为 null，则默认使用当前的系统时间自动赋值。
         6. varchar 字符串类型。`varchar(m)`，最大20个字符。

       - 例：创建一个表：

         ```mysql
         create table student(
             id int,
             name varchar(32),
             age int,
             score double(4, 1),
             birthday date,
             insert_time timestamp
         );
         ```

     - R（Retrieve）：查询。

       - `show tables`：查询某个数据库中所有表的名称（前提先要进入使用数据库）。
       - `desc 表名`：查询表结构。

     - U（Update）：修改。

       - `alter table 表名 rename to 新表名`：修改表的名称。
       - `show create table 表名`：查看表。
       - `alter table 表名 character set 字符集`：修改表的字符集。
       - `alter table 表名 add 列名 数据类型`：添加一列。
       - `alter table 表名 change 列名 新列名 新数据类型`或`alter table 表名 modify 列名 新数据类型`：修改列的名称和类型。
       - `alter table 表名 drop 列名`：删除一列。

     - D（Delete）：删除。

       - `drop table 表名`：删除表。
       - `drop table if exists 表名`：删除表前判断表是否存在。

- DML：增删改表中的数据。

  - 添加数据。
    - `insert into 表名(列名1, 列名2) values(值1, 值2)`：向列1、列2中分别添加数据。
    - 列名和值必须要按顺序一一对应，数据类型不能出错。
    - 如果表名后不定义列名，则默认给所有列添加数据，但是所有列都要有对应的数据。如`insert into values(值1, 值2)`。
    - 除了数字类型，其他的数据类型都要用引号引起来（单引号双引号都可以）。
  - 删除数据。
    - `delete from 表名 where 删除条件`：对于某个表，删除所有满足删除条件的数据（行）。
    - 如果不加条件，则删除表中的所有数据。
    - `truncate table 表名`：删除表，然后再创建一个完全相同的空表，可以用于删除表中的所有记录。
  - 修改数据。
    - `update 表名 set 列名1=值1,列名2=值2 where 修改条件`：对于某个表，修改所有满足修改条件的数据，按列进行赋值。
    - 如果不加条件，则会将表中所有记录都修改。

- DQL：查询表中的数据。
  
  - `select * from 表名`：查询表中的所有数据。