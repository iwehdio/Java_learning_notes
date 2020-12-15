## 1、Set接口

- Set 集合特点：

  1. 无序，存储和读取的顺序有可能不一样。
  2. 不允许重复，要求元素是唯一的。
  3. 没有索引。

- HashSet ：Set 的一个子类实现。

  - 使用 HashSet存储自定义对象并遍历。

  - 流程：

    1. 创建集合对象。
    2. 创建元素对象。
    3. 添加元素对象。
    4. 遍历集合方法。
       - 转型为数组。
       - 迭代器。
       - 增强 for 。

  - 例：

    ```java
    HashSet<Student> hs = new HashSet<Student>();
    Student s = new Student("A", 12);
    hs.add(s);
    for(Student s : hs){
        System.out.println(s);
    }
    ```

- HashSet 的 add() 方法会添加重复元素的原因：

  - 该方法首先比较的是 hash 值比较，如果不一样则认为没有重复，添加。
  - 如果 hash 值一样，则再比较地址值或使用 equals 方法比较。
  - 如果没有重写 hashCode() 方法，重复元素的 hash 值就是地址值，认为不重复。

  - 重写 hashCode() 和 equals() 方法：

    - 重写 equals() 方法：比较对象的每个元素是否相等，

      ```java
      @Override
      public boolean equals(Object obj){
          Student s = (Student)obj;
          if(!this.name.equals(s.name)){
              return false;
          }
          if(this.age != s.age){
              return false;
          }
          return true;
      }
      ```

    - 重写 hashCode() 方法：永远返回一个相同的值。

      ```java
      @Override
      public int hashCode(){
          return 1;
      }
      ```

- 优化重写的 hashCode()  方法：

  - 对于完全不同的成员变量，返回不同的值。

  - 让 hashCode() 的返回值与成员变量有关，例如返回所有的成员变量之和。基本数据类型直接相加，引用数据类型获取 hashCode() 方法返回后再相加（不包括bool类型）。

  - 例：

    ```java
    @Override
    public int hashCode(){
        //调用 String 的 hashCode() 方法
        return age + name.hashCode();
    }
    ```

- 优化重写的 equals()  方法：

  - 先比较地址值。
  - 用 `.getClass()`方法，判断是否属于同一个类。

- 自动生成：右键 > source > Generate  hashCode() and equals()  。

- Collection ：集合体系的最顶层，包含了集合体系的共性。

- Collections ：是一个工具类，方法就是用于操作 Collection。

  - `static int binarySearch(List list, Object key)`：使用二分查找，查找指定元素。

    ```java
    List<Integer> list = new ArrayList<Integer>();
    /*添加元素*/
    int index = Collections.binarySearch(list, 4);
    ```

  - `static void copy(List dext, Lit src)`：把源列表中的数组覆盖到目标列表（目标列表长度要大于等于源列表）。

  - `static void fill(List list, Object obj)`：使用指定对象填充指定列表的所有元素。

  - `static void reverse(List list)`：反转列表。

  - `static void shuffle(List list)`：列表随机置换（顺序打乱。）

  - `static void sort(List list)`：按自然顺序排序。

  - `static void swap(List list,int i,int j)`：互换列表中两个索引的数据。

- 模拟发牌：

  ```java
  ArrayList<String> box = new ArrayList<String>();
  //创建牌池		
  String[] arr = {"B", "R", "F", "M"};
  String[] arr2 = {"1","2","3","4","5","6","7","8","9","10","11","12","13"};
  for(int i=0;i<arr.length;i++){
      for(int j=0;j<arr2.length;j++){
          box.add(arr[i]+arr2[j]);
      }
  }
  box.add("Big");
  box.add("Small");
  //打乱
  Collections.shuffle(box);
  System.out.println(box);
  //创建容器
  ArrayList<String> Q = new ArrayList<String>();
  ArrayList<String> W = new ArrayList<String>();
  ArrayList<String> E = new ArrayList<String>();
  //发牌
  for(int i=0;i<box.size()-3;i++){
      switch(i%3){
          case 0:
              Q.add(box.get(i));
              break;
          case 1:
              W.add(box.get(i));
              break;
          case 2:
              E.add(box.get(i));
              break;
      }
  }
  System.out.println(Q);
  System.out.println(W);
  System.out.println(E);
  for(int i=box.size()-3;i<box.size();i++){
      System.out.println(box.get(i));
  }
  ```

## 2、Map接口

> 体现有对应关系的数据，是一个将键映射到值的对象。

- 不能包含重复的键；每个键最多映射一个值。

- Map 是一个双列集合，不是 Collection 的子接口，是另一种顶层结构。

- HashMap ：Map的一个子类实现。

  - `V put(K key, V value)`：将 key 映射到 value，如果 key 存在则覆盖 value 并返回被覆盖的原 value，实现添加和修改。
  - `boolean containsKey(Object key)`：判断指定的 key 是否存在。
  - `boolean containsValue(Object Value)`：判断指定的 value 是否存在。
  - `boolean isEmpty()`：判断是否为空。
  - `void clear()`：清空所有键值对。
  - `V remove(Object key)`：按输入的键删除键值对，返回 key 对应的值。没有删除成功则返回 null 。
  - `int size()`：返回键值对的个数。
  - `V get(Object key)`：按输入的键获取对应的值。

- 创建 Map<K,V> 对象，需要给定 key 和 value 的泛型。

  ```java
  Map<String,String> map = new HashMap<String,String>();
  ```

- `Set<K> keySet()`：获取所有的键。返回类型为 Set ，泛型为 key 的类型 K 。

- `Collection<V> values()`：获取所有的值。返回类型为 Collection ，泛型为 value 的类型 V 。

- Map 的遍历方式：

  1. 首先获取所有的键；遍历所有的键，获取所有键对应的值。

     ```java
     Map<String,String> map = new HashMap<String,String>();
     map.put("key1","value1");
     map.put("key2","value2");
     
     Set<String> keys = mao.keySet();
     for(String key : keys){
         String value = map.get(key);
         System.out.println(key + ": " + value);
     }
     ```

  2.  通过创建一个类，将每个键值对都通过这个类实例化为一个对象。

     - Map 中有一个这样的方法：`Set<Map.Entry<K,V>>  entrySet()`。
     - 返回类型是 Set ，元素类型为 Map.entry 对象，泛型为 key 和 value 的泛型。
     - 每个 Map.entry 对象就是一个键值对，可以通过 `.getKey()` 和 `.getValue()` 方法获取键和值。

     ```java
     Map<String,String> map = new HashMap<String,String>();
     map.put("key1","value1");
     map.put("key2","value2");
     
     Set<Map.entry<String,String>> entrys = map.entrySet();
     for(Map.Entry<String,String> entry : entrys){
         String key = entry.getKey();
         String value = entry.getValue();
         System.out.println(key + ": " + value);
     }
     ```

- 使用 HashMap 存储数据并遍历（key为自定义对象时）：

  ```java
  HashMap<Student,String> hm =new HashMap<Student,String>();
  Student s = new Student("A", 12);
  
  hm.put(s,"value");
  // 遍历方法1
  Set<Student> keys = hm.keySet();
  for(Student key : keys){
      String value = hm.get(key);
  }
  // 遍历方法2
  Set<Map.Entry<Student,String>> entrys = hm.entrySet();
  for(Map.Entry<Student,String> entry : entrys){
      String key = entry.getKey();
      String value = entry.getValue();
  }
  ```

- 这样还是会添加重复的 Student 对象 ：需要 Student 类重写 HashCode() 和 equals() 方法。

