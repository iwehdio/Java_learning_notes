# 27、DQL、约束

## 1、DQL查询语句

- 基础查询：
  - `select 字段列表 from 表名列表 where 条件列表 groub by 分组字段 having 分组后的条件字段 order 排序字段 limit 分页限定`：DQL 的所有查询功能。
  - `select * from 表名`：查询表中的所有值。
  - `select 字段列表 from 表名`：字段列表是指逗号分隔的列名。
  - `select distinct 字段列表 from 表名`  ：distinct 字段去除结果集中的重复值。
  - `select 列名1 + 列名2 from 表名`：数值类型的两列按行相加。
  - 二者相加，如果有一个值为 null 则结果为 null 。使用 `ifnull(表达式1,表达式2)`，表示如果表达式1为 null 字符，则结果替换为表达式2，否则取表达式1中的原值。
  - 起别名：`列名 as 别名`或`列名 别名`。
- 条件查询：
  - `select * from 表名 where 条件列表`：查询所有符合条件的值。
  - 关系运算符：< , > , >= , <= ,  <> , != , =（没有 ==）。or  , and , between ... and ... （某列的值在二者之间）, in (...) （某列的值在列表中）。
  - null 值不能用等号等一般的关系运算符判断，要用 is 或 is not 来判断。
- 模糊查询：
  - `select * from 表名 where 条件列表 like 查询字段`：查询符合某个条件的值中，符合模糊查询字段的值。
  - 占位符：_ ：下划线表示单个任意字符；% ：百分号表示多个任意字符。如查询第二个字母是B的字段 ‘_B%’，如查询字段中包含B的 '%B%'。

- 排序查询：
  - `select * from 表名 order by 排序字段1 排序方式1,排序字段2 排序方式2`：排序语法。排序字段就是列名，默认升序。
  - 排序方式：ASC——升序；DESC——降序。
  - 如果有两个排序字段，则先满足排序字段1，对于字段1相同的再按排序字段2排序。有多个排序字段时同理。
- 聚合函数：
  - 将一列数据作为一个整体，进行纵向计算。运算结果都是单行单列。
  - `select 聚合函数(列名) from 表名`：聚合函数按列计算。
  - 聚合函数：count——计算个数；max——计算最大值；min——计算最小值；sum——计算总和；avg——计算平均值。
  - 聚合函数默认排除值为 null 的值（如n行数据中国有一个为null，则count返回的值为n-1）。解决方法有：
    1. 选择不包含 null 的列。
    2. 使用`ifnull(列名, 0)`。
    3. 使用表的主键。
- 分组查询：
  -  按照一定条件分组，计算分组后的按列聚合函数值。
  - `select 分组字段,聚合函数(列名) from 表名 group by 分组字段`：分组字段是按某个列的值进行分组，然后求分组后某个列的聚合函数。
  - `select 分组字段,聚合函数(列名) from 表名 where 条件字段group by 分组字段`：在分组之前限定，不满足条件则不参与分组。where 后的条件字段不能跟聚合函数。
  - `select 分组字段,聚合函数(列名) from 表名 group by 分组字段 having 条件字段`：在分组之后限定，不满足的结果不会被显示出来。having 后的条件字段可以跟聚合函数。
  - 在聚合函数后可以给其结果起别名：`聚合函数(列名) 别名`。
- 分页查询：
  - 将查询结果分为几页显示。
  - `select * from 表名 limit 开始的索引,每页查询的条数`：显示查询到的从开始索引开始的，与查询条数相等的结果。
  - 开始的索引 = ( 当前页码 - 1 ) × 每页显示的条数。
  - 这种分页操作语法是MySQL中特有的。

## 2、约束

> 对表中的数据进行限定，保证数据的正确性、有效性和完整性。

- 约束的分类：

  - 主键约束：primary key。
  - 非空约束：not null。
  - 唯一约束：unique。
  - 外键约束：foreign key。

- 非空约束：

  + 创建表时添加非空约束：

    ```mysql
    create table stu(	# 创建后加 not null
        id int not null,
        name varchar(20) not null
    );
    ```

  + 删除 id 的非空约束：`alter table stu modify id int`。

  + 创建表后添加 id 的非空约束：`alter table stu modify id int not null`。

- 唯一约束：

  - 创建表时添加唯一约束：

    ```mysql
    create table stu(	# 创建后加 unique
        id int unique,
        name varchar(20) unique
    );
    ```

  - 唯一约束对两个 null 不起作用。

  - 删除 id 的唯一约束：`alter table stu drop index id`。

  - 创建表后添加 id 的唯一约束：`alter table stu modify id int unique`。

- 主键约束：

  - 主键约束，表示非空且唯一。

  - 一张表只能有一个字段为主键，是表中记录的唯一标识。

  - 创建表时添加主键约束：

    ```mysql
    create table stu(	# 创建后加 primary key
        id int primary key ,   # 注意 key 后也有一个空格
        name varchar(20)
    );
    ```

  - 删除 id 的主键约束：`alter table drop primary key`。

  - 创建表后添加 id 的主键约束：`alter table stu modify id int primary key`。

- 自动增长：

  - 如果某一列是数值类型的，使用 auto_increment 可以来完成值的自动增长。一般和主键一起使用。

  - 创建表时添加自动增长：

    ```mysql
    create table stu(	# 创建后加 auto_increment
        id int primary key auto_increment,   
        name varchar(20)
    );
    ```

  - 自动增长的值只与上一条记录的值有关。

  - 如果有自动增长，添加值时可以指定 id 为 null，这样可以添加成功，id 的值为自动增长赋予的。

  - 即使有自动增长，也可以指定值，这时最终写入的值为所指定的值而不是自动增长的值。

  - 删除 id 的自动增长：`alter table stu modify id int`。

  - 创建表后添加 id 的自动增长：`alter table stu modify id int auto_increment`。

- 外键约束：

  - 将外键表与主表中的主表列关联，让表与表之间产生关系，保证数据的正确性。
  - 对于外键表中存在的冗余数据，可以通过外键列链接到另一个表（主表）中。外键列中存储的是主表中主表列的索引，根据外键列的值可以索引主表列，从而索引到所需要的数据行。

  - 创建表时添加外键约束：

    ```mysql
    create table 主表名称(
        ...,
    	主表列 # 一般为主键约束，起码要为唯一约束
    );
    create table 外键表名(
        ...,
        外键列,
        constraint 外键名称 foreign key (外键列名称) references 主表名称(主表列名称)
    );
    ```

  - 添加外键约束后，主表中的行不能随意删除，除非此行的主表列的值在外键表中无对应数据。

  - 外键列中外键列的值可以为 null ，但不能为主表列中不存在的值。

  - 删除外键：`alter table 外键表名 drop foreign key 外键名称`。

  - 创建表后添加外键：`alter table 外键表名 add constraint 外键名称 foreign key (外键列名称) references 主表名称(主表列名称) `。

- 级联操作：

  - 由于外键约束，导致修改外键列和主表列的操作比较麻烦，需要使用级联操作。
  - 级联更新：主表列中的值改变时，同时也改变外键列中所对应的值。
  - 设置级联更新：`alter table 表名 add constraint 外键名称 foreign key (外键列名称) references 主表名称(主表列名称) on update cascade` 。
  - 级联删除：主表列中的值所对应的行删除时，同时也删除外键列中值所对应的行。
  - 设置级联删除`alter table 表名 add constraint 外键名称 foreign key (外键列名称) references 主表名称(主表列名称) on delete cascade` 。

