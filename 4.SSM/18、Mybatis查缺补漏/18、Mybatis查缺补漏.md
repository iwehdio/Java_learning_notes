## 1、Mybatis

- 增删改方法不用写返回值类型，返回是是影响多少行。如果是int、long或boolean会自动封装。
- `@Alias`注解可以加在实体类上，当指定为包下起别名时，其中写自定义别名。
- Mybatis四大对象：
  - Executor：执行器。
  - Paramterhandler：参数处理器。
  - ResultSetHandler：结果集处理器。
  - StatementHandler：SQL语句处理器。
- 主配置文件下：
  - `<typeHandlers>`标签：类型处理器。
    - Mybatis在预处理语句中设置参数或从结果集中取出一个返回值时，都会用类型处理器以合适的方式转换为Java类型。
    - 自定义类型处理器需要实现Typehandler接口。并在该标签下注册。
  - `<databaseIdProvider>`标签：数据库移植。
    - 使用property属性配置数据库厂商和别名。
    - 在sql语句的配置处用databaseId属性指定不同数据库。
- SQL映射文件：
  - 增删改标签：
    - userGeneratedKeys属性：使用自动生成的主键。
    - keyProperty属性：将自增主键的值赋值给传入对象的哪个属性。
  - 查询标签：
    - 查询多条记录如果返回的是List，resultType写的是List中元素的类型。
    - 查询单条记录如果返回的是map，默认将列名作为key值作为value，resultType写的是map。
    - 查询多条记录如果返回的是map，需要在接口方法上指定`@MapKey`作为key，封装的对象作为value，resultType写的是map中元素的类型。
  - 传入的参数：
    - 如果传入单个参数，基本类型直接写`#{任意名称}`。
    - 如果传入多个参数：写参数的索引。
      - 如两个参数写`#{0}`或`#{param1}`和`#{1}`或`#{param2}`。
      - 因为在底层，多个参数被封装到一个map中。
      - 在dao接口内用`@Param`注解指定map中封装时的键。
    - 传入Javabean：直接写`#{属性值}`。
    - 传入map：直接写`#{键名}`。
    - `${}`和`#{}`取值的不同：
      - 后者是参数预编译的方式参数用？替代，前者是直接拼接的方式。
      - 前者可用于动态取出表名等。
  - resultMap下的联合查询：
    - 一个Javabean A中还有一个属性是Javabean B或集合C。
    - 在数据库中的体现就是外键关系。
    - column中指定的是数据库列名或SQL语句中起的别名。
    - 级联属性封装联合查询的结果：使用`B.属性名`指定property。
    - 可以用`<association>`定义对Javabean B进行联合查询。
      - 也可以不用级联操作，用select属性指定分步查询，column指定一个查询的id，调用指定的SQL查询并封装。
      - 如果要传入多个参数，使用`{key1=column1,key2=column2}`。
    - 可以用`<collection>`定义对集合C进行联合查询。
  - XML中的转义字符：
    - `""`：`&quot;`。
    - `''`：`&apos;`。
    - `&`：`&amp;`。
    - `<`：`&lt;`。
    - `>`：`&gt;`。
  - 动态SQL：
    - `<if>`标签：判断。
    - `<trim>`标签：截取字符串。指定添加（perfix、suffix）、去除（perfixOverrides、suffixOverrides）前缀或后缀。
    - `<foreach>`标签：遍历输入集合。
    - `<choose>`标签：相当于switch。下有标签`<when>`和`<otherwise>`。
    - `<where>`标签和`<set>`标签：结合if使用，处理多余的and和逗号。
    - `<bind>`标签：将一个表达式绑定到变量上。
    - `<include>`标签：引用外部`<sql>`定义的可重用SQL语句。
    - 可以使用的两个可用参数：`_parameter`（代表整个参数，多个参数时为map）和`_databseId`（数据库移植）。
- 二级缓存在SqlSession关闭或提交后才会生效。
- 缓存的查询顺序：
  - 一级缓存和二级缓存中有同一个数据的情况是不会存在的。
  - 一级缓存关闭了，二级缓存才有数据。
  - 二级缓存中有就直接拿，没有就看一级缓存，没有就查数据库，查完放入一级缓存。
  - 任何时候都是先看二级缓存再看一级缓存。



## 2、SSM整合

- 配置文件：

  - web.xml：
    - 注册监听器，指定spring配置文件。
    - 注册springMVC前端控制器，指定springMVC配置文件。
    - 指定字符编码的过滤器。
    - 指定Rest风格过滤器。
  - SpringMVC配置文件：
    - 包扫描注解，只扫描控制器。
    - 开启默认Servlet和注解驱动。
    - 注册视图解析器。
  - Spring配置文件：
    - 包扫描注解，不扫描控制器。
    - 配置数据源。
    - 配置Mybatis操作数据库。
      - 配置SqlSessionFactory。
      - 配置mapperScanner。
    - 配置事务管理器。
    - 配置事务，指定切入点表达式和事务增强的对应关系。
    - 配置事务增强，配置事务属性对应的方法。
  - Mybatis配置文件：
    - 配置SQL语句。
- MBG：Mybatis代码生成器。
  - 逆向分析数据库中的表，自动生成Javabean、Dao和SQL映射文件。







