# 47、redis

## 1、redis概述

> 是一款高性能的NOSQL系列的非关系型数据库。

- 非关系型数据库：以键值对的形式存储，数据之间没有关联关系，数据存储在内存中。
- 是实现缓存的一种形式，如果缓存中有请求的数据则直接返回，否则再去数据库中查询。
- redis 下的文件：
  - redis.windows.conf：配置文件
  - redis-cli.exe：客户端。
  - redis-server.exe：服务器端。
  - 先启动服务器，再打开客户端。默认端口6379。
- redis 的数据结构：
  - redis 存储的是键值对格式的数据。键 key 都是字符串，value 值有五种不同的数据结构。
  - value 值的数据结构包括：字符串类型（string格式）、哈希类型（map格式）、列表类型（linkedlist格式）、集合类型（set格式）、有序集合类型（sortedset格式）。
  - 哈希类型（map格式）可以在 value 中可以再存储键值对类型的数据。
  - 列表、集合、有序集合可以存储序列数据，集合不允许重复，有序集合中元素有顺序。
- 命令操作：
  - 字符串：
    - 存储数据：`set key value`。
    - 获取数据：`get key`。
    - 删除数据：`del key`。
  - 哈希类型：
    - 存储数据：`hset key field value`。
    - 获取数据：`get key field`。
    - 删除数据：`del key field`。
    - 获取所有数据：`hgetall key`。
    - field 是指 map 集合中的键。
  - 列表类型：实际上是对队列的模拟。
    - 从左边添加：`lpush key value`。
    - 从右边添加：`rpush key value`。
    - 按索引获取部分数据：`lrange key start end`。所有数据可取 0 到 -1。
    - 删除最左边数据并返回：`lpop key`。
    - 删除最右边数据并返回：`rpop key`。
  - 集合类型：不允许重复操作。
    - 存储操作：`sadd key value`。
    - 获取所有数据：`smembers key`。
    - 删除单个数据：`srem key value`。
  - 有序集合类型：元素有顺序，按传入的分数 score(double) 大小进行排序。
    - 存储数据：`zadd key score value`。
    - 获取部分数据同时获取分数：`zrange key start end withscores` 。
    - 删除数据：`zren key value`。
- 通用命令：
  - `keys*`：查询所有的键， \* 可以被正则表达式替换。
  - `type key`：获取键对应的值的类型。
  - `del key`：删除指定的键值对。
- 持久化：redis 是内存数据库，服务器重启会将数据丢失，需要写入硬盘。
  - RDB 机制：默认机制，在一定间隔时间内检测 key 的变化情况，持久化数据。
    - 编辑 redis 的配置文件中的 save 配置，`save 时间长度 key值改变的数量`。
    - 启动服务器时需要指定配置文件名称，命令行下 `redis-server.exe redis.windows.conf `。
    - 会在目录下生成 rdb 文件。
  - AOF 机制：日志记录方式，可以记录每一条命令的操作，每一次命令操作后持久化数据。
    - 编辑 redis 的配置文件中的 appendonly ，为 no 时表示关闭。为 yes 表示开启。
    - 服务器启动同时要指定配置文件。
    - 配置`appendsync always`为每次操作都持久化、配置`appendsync everysec`为每秒持久化一次、配置`appendsync no`为不持久化。
    - 会在目录下生成 aof 文件。

## 2、Jedis

> Java 操作 redis 的工具。

- 使用步骤：

  1. 导入 jar 包。
  2. 获取连接，`Jedis jedis = new Jedis(ip地址, 端口号)`。如果是空参构造，默认 "localhost" 和 6379。
  3. 操作数据库，方法名与 redis 中的命令操作相同。
  4. 关闭连接，`jedis.close()`。

- 操作 String ：

  - `set(key, value)`：存储字符串。
  - `get(key)`：获取字符串。
  - `setex(key, time, value)`：存储指定过期时间的数据，单位为秒。

- 操作 hash ：

  - `hset(key, field, value)`：存储哈希表。
  - `hget(key, value)`：获取哈希中的数据。
  - `hgetAll(key)`：获取哈希中的所有数据，返回的是 map 集合。

- 操作 list ：

  - `lpush(key, value) / rpush(key, value)`：从列表左 / 右存储。可以一次按顺序存储多个值。
  - `lrange(key, start, end)`：获取索引范围内的所有值，返回的是 list 列表。
  - `lpop(key) / rpop(key)`：从列表左 / 右边弹出并返回数据。

- 操作 set ：

  - `sadd(key, value)`：存储集合。可以一次存储多个值。
  - `smembers(key)`：获取集合中的所有数据，返回的是 set 集合。

- 操作 sortedset ：

  - `zadd(key, score, value)`：获取有序列表。
  - `zrange(key, start, end)`：获取索引范围内的所有值，返回的是 set 集合。

- Jedis 连接池：

  1. 创建 JedisPool 连接池对象：`JedisPool jedisPool = new JedisPool(ip地址, 端口号)`。空参的含义同上。
     - 构造方法也可以传入配置对象。
     - 创建配置对象：`JedisPoolConfig config = new JedisPoolConfig() `。
     - 可以调用配置对象的方法，对配置对象进行设置。
     - 传入配置对象：`JedisPool jedisPool = new JedisPool(config, ip地址, 端口号)`。
  2. 获取连接：`Jedis jedis = jedisPool.getResource()`。
  3. 数据操作。
  4. 归还连接：`jedis.close()`。

- Jedis 连接池工具类：

  - 加载配置文件，配置连接池参数，提供获取连接的方法。

  - 实现：

    ```java
    public class JedisPoolUtil {
        private static JedisPool jedisPool;
        static {
            InputStream is = JedisPoolUtil.class.getClassLoader().getResourceAsStream("jedis.properties");
            Properties pro = new Properties();
            try {
                pro.load(is);
            } catch (IOException e) {
                e.printStackTrace();
            }
            JedisPoolConfig config = new JedisPoolConfig();
            config.setMaxTotal(Integer.parseInt(pro.getProperty("maxTotal")));
            config.setMaxIdle(Integer.parseInt(pro.getProperty("maxIdle")));
            jedisPool = new JedisPool(config, pro.getProperty("host"), Integer.parseInt(pro.getProperty("port")));
        }
    
        public static Jedis getJedis(){
            return jedisPool.getResource();
        }
    }
    ```



## 3、案例

- 从数据库查询下拉列表内容：

  - 创建数据库环境。
  - 创建下拉列表内容 province 的实体类，包括 id 和 name。
  - 创建 JDBC 和 Jedis 工具类。

- 使用缓存进行优化：

  - 缓存一般用于一些不经常发生变化的数据。
  - 数据库的数据一旦变化，则需要更新缓存。
  - 在 service 的增删改方法中，需要将对应的缓冲删除。

- Dao 包：

  ```java
  //接口
  public interface ProvinceDao {
      public List<Province> findAll();
  }
  //实现
  public class ProvinceDaoImpl implements ProvinceDao {
      private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());
      @Override
      public List<Province> findAll() {
          String sql = "select * from province";
          List<Province> list = template.query(sql, new BeanPropertyRowMapper<Province>(Province.class));
          return list;
      }
  }
  ```

- Service 包：

  ```java
  //接口
  public interface ProvinceService {
      public List<Province> findAll();
      public String findAllAsJSON();
  }
  //实现
  public class ProvinceServiceImpl implements ProvinceService {
      private ProvinceDao dao = new ProvinceDaoImpl();
      @Override
      public List<Province> findAll() {
          return dao.findAll();
      }
  	//使用缓存进行优化
      @Override
      public String findAllAsJSON() {
          Jedis jedis = JedisPoolUtil.getJedis();
          String provinceAsJSON = jedis.get("province");
          if(provinceAsJSON == null || provinceAsJSON.length() == 0) {
              //如果没有缓存则查询数据库并存入缓存
              List<Province> list = dao.findAll();
              ObjectMapper mapper = new ObjectMapper();
              try {
                  provinceAsJSON = mapper.writeValueAsString(list);
              } catch (JsonProcessingException e) {
                  e.printStackTrace();
              }
              jedis.set("province", provinceAsJSON);
              System.out.println("查询数据库");
          } else {
              System.out.println("查询缓存");
          }
          jedis.close();
          return provinceAsJSON;
      }
  }
  ```

- Servlet 包：

  ```java
  @WebServlet("/findProvinceServlet")
  public class FindProvinceServlet extends HttpServlet {
      protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          //1、获取数据
          ProvinceService service = new ProvinceServiceImpl();
          /*
          List<Province> list = service.findAll();
          //2、转换成json格式
          ObjectMapper mapper = new ObjectMapper();
          String json = mapper.writeValueAsString(list);
          */
          //使用缓存优化，代替了上方注释掉的代码
          String json = service.findAllAsJSON();
          //3、响应
          response.setContentType("application/json;charset=utf-8");
          response.getWriter().write(json);
      }
      protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          this.doPost(request, response);
      }
  }
  ```

- index.html：

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
      <script src="js/jquery-3.3.1.min.js"></script>
      <script>
          $(function () {
              $.get("findProvinceServlet", {}, function (data) {
                  //1、获取select
                  var province = $("#province")
                  //2、遍历json
                  $(data).each(function () {
                      var option = "<option name='"+this.id+"'>"+this.name+"</option>";
                      //3、添加option
                      province.append(option);
                  })
              })
          })
      </script>
  </head>
  <body>
      <select id="province">
          <option>---请选择---</option>
      </select>
  </body>
  </html>
  ```

  

