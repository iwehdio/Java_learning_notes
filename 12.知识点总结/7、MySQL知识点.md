### 1、SQL

 [MySQL（一）——CRUD语句](https://www.cnblogs.com/iwehdio/p/13893865.html)

- 查询：

  - select 查询列表 from 表名
  - select distinct 查询列表 from 表名
  - select 查询列表 from 表名 where 筛选条件
  - select 查询列表 from 表名 order by 排序列表 ASC/DESC
  - select 分组函数，分组字段 from 表名 group by 分组字段
  - select 查询列表 from 表1 inner join 表2 on 连接条件 where 筛选条件
  - select 查询列表 from 主表 left outer join 从表 on 连接条件 where 筛选条件
  - select 分组函数，分组字段 from 表名 group by 分组字段 having 筛选条件
  - 子查询：select后、from后、where/having后、exists后
  - select 查询列表 from 表 limit 起始索引,条目个数
  - 查询语句1 union 查询语句2

- 查询语句中的各个部分的执行顺序：

  ```sql
  select 查询列表    7
  from 表1 别名       1
  连接类型 join 表2   2
  on 连接条件         3
  where 筛选          4
  group by 分组列表   5
  having 筛选         6
  order by 排序列表    8
  limit 起始条目索引，条目数;  9
  ```

- 函数：
  - concat(字段1，字段2)
  - ifnull(字段,新值)
  - isnull(字段)
  - length(字段)
  - if(条件表达式，结果1，结果2)
  - case
  - count(字段)
  - sum(distinct 字段)
- 其他：
  - select 查询列名 As 别名 from 表名

- 增删改：
  - insert into 表名(列名,...) value(值,...)
  - insert into 表名 set 列名=值
  - update 表名 set 列=新值 where 筛选条件
  - update 表1 连接类型 join 表2 on 连接条件 set 列名=值 where 筛选条件
  - delete from 表名 where 筛选条件
  - delete 所要删掉的表 from 表1 连接类型 join 表2 on 连接条件 where 筛选条件

[MySQL（二）——其他基础功能](https://www.cnblogs.com/iwehdio/p/13899000.html)

- 库：

  - create database if not exist 库名
  - alter database 库名 character set 字符集
  - drop database if exist 库名

- 表：

  - ```sql
    create table 表名(
    	列名 类型 【约束】,
        列名 类型 【约束】
    );
    ```

  - alter table 表名 change column 旧列名 新列名 类型

  - alter table 表名 modify column 列名 新类型 约束

  - alter table 表名 add column 列名 类型

  - alter table 表名 drop colnum 列名

  - drop table if exist 表名

  - create table 新表名 like 旧表名

  - create table 新表名 select * from 旧表名

- 数据类型：

  - 整形：Tinyint、Smallint、Mediumint、int/Integer、Bigint
  - 小数：float(M，D)、double(M，D)、dec(M,D)/decimal(M,D)
  - 字符：char(M)、varchar(M)、binary/varbinary、enum、set
  - 日期：time、datetime、timestamp、date

- 约束：

  - not null、default、primary、unique、foreign key、auto_increament
  - constraint 外键名 foreign key(从表中的外键列) references 主表名(主表关联列)
  - alter table 表名 modify column 列名 类型 约束
  - alter table 表名 modify column 列名 类型
  - alter table 表名 add 约束(字段)
  - alter table 表名 drop 约束 约束名
  - alter table 从表 add constraint 外键名 foreign key(从表外键列) references 主表(主表关联列) on delete cascade
  - alter table 从表 add constraint 外键名 foreign key(从表外键列) references 主表(主表关联列) on delete set null

- 范式：

  - 第一范式：所有列应该不可再分
  - 第二范式：必须存在业务主键，且非主键字段应该依赖于全部业务主键（之所以写“全部”，因为可能存在复合主键）
  - 第三范式：非主键列不能依赖除主键列以外的其他列

- 事务：

  - 事务的属性：原子性、一致性、隔离性、持久性
  - 事务的并发问题：脏读、不可重复度、幻读
  - 事务隔离级别：读未提交、读已提交、可重复度、串行化

- 视图：

  - create view 视图名 as 查询语句
  - select * from 视图名
  - create or replace view 视图名 as 查询语句
  - alter view 视图名 as 查询语句
  - drop view 视图名
  - show create view 视图名

- 变量：

  - show global/session variables like 匹配值
  - set @用户变量名=值
  - select 字段 into @变量名 from 表
  - declare 变量名 类型 default 值

- 存储过程：

  ```sql
  delimiter 结束标记
  create procedure 存储过程名(参数列表)
  begin
  	存储过程体;
  end 结束标记
  ```

  - call 存储过程名(实参列表)结束标记
  - drop porcedure 存储过程名
  - show create porcedure 存储过程名

- 函数：

  ```sql
  delimiter 结束标记
  create function 函数名(参数列表) returns 返回类型
  begin
  	函数体;
  	return 值;
  end 结束标记
  ```

  - select 函数名(实参列表)
  - show create function 函数名
  - drop function 函数名

### 2、索引等

[MySQL（四）——索引使用等](https://www.cnblogs.com/iwehdio/p/13933847.html)

- 索引的操作：

  - create index 索引名 on 表名(索引列)
  - alter 表名 add index [索引名] on (索引列)
  - drop index [索引名] on 表名
  - show index from 表名

- 什么情况适合创建索引？

- 什么情况不适合创建索引？

- 创建索引的原则？

  - 索引并不是越多越好，联合索引应该优于多个单列索引
  - 索引应该建立在区分度高的字段上
  - 尽量给查询频繁的字段创建索引，避免为修改频繁的字段创建索引
  - 避免重复索引

- Explain：

  - 表的读取顺序：id
  - 数据读取操作的操作类型：select_type
  - 涉及到的表：table
  - 查询使用了何种类型：type
  - 哪些索引可以使用：key
  - 哪些索引被实际使用：possible_keys
  - 被选择的索引长度：key_len
  - 与索引比较的列：ref
  - 找到所需的记录所需要读取的行数：rows

- type的类型：

  | system          | 查询对象表只有一行数据，且只能用于 MyISAM 和 Memory 引擎的表，这是最好的情况 |
  | --------------- | ------------------------------------------------------------ |
  | const           | 基于主键或唯一索引查询，最多返回一条结果                     |
  | eq_ref          | 表连接时基于主键或非 NULL 的唯一索引完成扫描                 |
  | ref             | 基于普通索引的等值查询，或者表间等值连接                     |
  | fulltext        | 全文检索                                                     |
  | ref_or_null     | 表连接类型是 ref，但进行扫描的索引列中可能包含 NULL 值       |
  | index_merge     | 利用多个索引                                                 |
  | unique_subquery | 子查询中使用唯一索引                                         |
  | index_subquery  | 子查询中使用普通索引                                         |
  | range           | 利用索引进行范围查询                                         |
  | index           | 全索引扫描                                                   |
  | ALL             | 全表扫描                                                     |

- 什么是覆盖索引？

- 索引优化：

  - 全值匹配
  - 最佳左前缀法则
  - 对索引列做操作，会导致索引失效而转向全表扫描
  - 不能使用索引中范围条件右边的列
  - 尽量使用覆盖索引，减少select*
  - 使用不等于（！=或者<>）的时候无法使用索引会导致全表扫描
  - is null，is not nul也无法使用索引
  - like以通配符开头（%abc...），索引失效会变成全表扫描
  - 字符串不加单引号索引失效
  - 用or来连接时会索引失效

- 查询是如何使用索引的？

- order by排序是如何使用索引的？

- 什么是慢查询日志？

- MySQL主从复制过程分哪三步？

[MySQL索引原理和锁](https://www.cnblogs.com/iwehdio/p/13938763.html)

- 索引的优点和缺点？
- B-Tree和B+Tree的优劣比较？
- 联合索引的B+树结构？
- 索引的类型？
  - 主键索引
  - 二级索引：唯一索引、普通索引、前缀索引、全文索引
- 什么是聚集索引和非聚集索引？
- 表锁和行锁？
- MVCC如何实现读写不阻塞的？
- 如何通过锁实现事务隔离级别的？
- 什么是间隙锁？

