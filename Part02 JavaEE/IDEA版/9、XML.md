# 36、XML

## 1、XML概述与语法

> 可扩展标记语言。可扩展表示标签都是自定义的。

- 功能：存储数据，作为配置文件或在网络中传输。

- xml 与 html 的区别：

  1. xml标签都是自定义的，html标签都是预定义的。
  2. xml语法严格，html语法松散。
  3. xml用于存储数据，html用于展示数据。

- 基本语法：

  - 必须在第一行定义文档声明。
  - 文档中有且仅有一个根标签。
  - 属性的值必须用引号引起来。
  - 标签必须正确关闭，或自闭合。
  - 标签名称区分大小写。

- 组成部分：

  1. 文档声明。
     - 格式：`<?xml 属性列表 ?>`。
     - 属性：version：版本号（必须有）；encoding：编码方式（告知解析引擎当前文档使用的字符集，默认ISO-8859-1）；standalone：是否独立（yes/no，是否依赖其他文件）。
  2. 指令。引入css进行显示。
  3. 标签。标签名称，数字不能开头，不能以xml开始，名称不能包含空格。
  4. 属性。id 属性值是唯一的。
  5. 文本。
     - CDATA区，在该区域中的文本会被原样展示，不会被转义。
     - CDATA区格式：`<![CDATA[数据]]>`。

- 约束：

  - 编写xml的是软件框架的使用者，解析xml的是软件框架。约束就是软件框架提供给使用者的说明文档，规定了xml文档的书写规则。

  - DTD：简单的约束技术。

    - 引入dtd文档到xml中：

      - 外部本地dtd：`<!DOCTYPE 根标签名 SYSTEM "dtd文件的位置">`。
      - 外部网络dtd：`<!DOCTYPE 根标签名 PUBLIC "dtd文件的名字" "dtd文件的位置URL">`。
      - 内部dtd：在xml文档中定义。

    - 示例：

      ```dtd
      <!ELEMENT students(student+)>		
      <!ELEMENT student(name,age,sex)>
      <!ELEMTNT name (#PCDATA)>
      <!ELEMTNT age (#PCDATA)>
      <!ELEMTNT sex (#PCDATA)>
      <!ATTLIST student number ID #REQUIRED>
      ```

      - 第一行表示students根标签下有student标签，+表示student标签出现一个或多个。
      - 第二行表示student标签下必须按顺序有name、age、sex三个标签。
      - 第三到五行指定这三个标签的值为字符串类型。
      - 最后一行是指定属性列表，student标签有number属性，ID表示该属性是唯一的，#REQUIRED表示该属性是必须赋值的。

  - Schema：复杂的约束技术，对内容可以进行更详细的限定。

    - 引入.xsd文件：在xml的根目录下，引入xsi前缀，向xsi引入xsd文件的命名空间，为每个xsd约束声明一个前缀作为标识。

    - 示例：

      ```xml
      <?xml version="1.0"?>
      <xsd:schema xmlns="http://www.itcast.cn/xml"
              xmlns:xsd="http://www.w3.org/2001/XMLSchema"
              targetNamespace="http://www.itcast.cn/xml" elementFormDefault="qualified">
          <xsd:element name="students" type="studentsType"/>
          <xsd:complexType name="studentsType">			<!--声明students类型--->
              <xsd:sequence>
                  <xsd:element name="student" type="studentType" minOccurs="0" maxOccurs="unbounded"/>
              </xsd:sequence>
          </xsd:complexType>
          <xsd:complexType name="studentType">			<!--声明student类型--->
              <xsd:sequence>
                  <xsd:element name="name" type="xsd:string"/>
                  <xsd:element name="age" type="ageType" />
                  <xsd:element name="sex" type="sexType" />
              </xsd:sequence>
              <xsd:attribute name="number" type="numberType" use="required"/>	<!--student的属性--->
          </xsd:complexType>
          <xsd:simpleType name="sexType">					<!--sex标签必须为枚举的二者之一--->
              <xsd:restriction base="xsd:string">
                  <xsd:enumeration value="male"/>
                  <xsd:enumeration value="female"/>
              </xsd:restriction>
          </xsd:simpleType>
          <xsd:simpleType name="ageType">					<!--age标签必须在设定的最大最小值之间--->
              <xsd:restriction base="xsd:integer">
                  <xsd:minInclusive value="0"/>
                  <xsd:maxInclusive value="256"/>
              </xsd:restriction>
          </xsd:simpleType>
          <xsd:simpleType name="numberType">				<!--number属性必符合规定的正则表达式--->
              <xsd:restriction base="xsd:string">
                  <xsd:pattern value="heima_\d{4}"/>
              </xsd:restriction>
          </xsd:simpleType>
      </xsd:schema> 
      ```

      Scheme约束对每个标签和属性的具体值的类型和取值都做了限定。

  - 符合Scheme约束的xml：

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    
    <students   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    			xmlns="http://www.itcast.cn/xml"
    			xsi:schemaLocation="http://www.itcast.cn/xml  student.xsd"
    >
    	<student number="heima_0001">
    		<name>tom</name>
    		<age>18</age>
    		<sex>male</sex>
    	</student>
    
    </students>
    ```

## 2、XML的解析

> 操作xml文档，将文档中的数据读取到内存中。

- 解析xml的方式：

  1. DOM思想：将标记语言文档一次性加载进内存，在内存中生成DOM树。

     - 优点：操作方便，可以对文档进行CRUD的所有操作。
     - 缺点：消耗内存。

  2. SAX思想：逐行读取，基于事件驱动。

     - 每个开始/结束标签或标签内的内容都算一行。会根据读取到的上一个标签触发事件，对之后读取的内容进行处理。

     - 优点：内存占用很小。
     - 缺点：只能进行读取操作，不能增删改。

- 常见的解析器：

  1. JAXP：支持DOM和SAX。
  2. DOM4J：支持DOM。
  3. Jsoup：基于Java的html解析器，支持DOM。
  4. PULL：Android内置，支持SAX。

- Jsoup的使用：

  - 使用步骤：
    1. 导入依赖 jar 包。
    2. 获取 Document 对象。
    3. 获取对应标签 Element 对象。
    4. 获取数据。

- Jsoup使用中的对象：

  - Jsoup：工具类，可以解析html或xml文档，返回Document。

    - parse方法，解析html或xml文档，返回Document对象。

    - `parse(File in, String charestName)`：解析xml或html文档。
    - `parse(String html)`：解析xml或html字符串。
    - `parse(URL url, int timeoutMillis)`：通过网络路径获取并解析xml或html文档。

  - Document：文档对象，继承自Element对象，代表内存中的DOM树。

    - `getElementsByTag(Stirng tagName)`：通过标签名获取元素对象集合。
    - `getElementsByAttribute(Stirng key)`：通过属性名获取元素对象集合。
    - `getElementsByAttributeValue(Stirng key, Stirng value)`：通过属性名和属性值获取元素对象集合。
    - `getElementById(Stirng id)`：通过属性名为id的属性值获取元素对象集合。

  - Elements：元素Element对象的集合，可以当做 `ArrayList<Element>`使用。

  - Element：元素对象，继承自Node。

    - 获取子元素对象，同Document对象中的方法。
    - 获取属性值：
      - `String attr(String key)`：根据属性名称获取属性值。
    - 获取文本内容：
      - `String test()`：获取文本内容。
      - `String html()`：获取标签体的所有内容，包括字标签。

  - Node：节点对象，是document和Element对象的父类。

  - 示例：

    ```java
    //获取xml的path
    String path = JsoupDemo.class.getClassLoader().getResource("student.xml").getPath();
    //获取Document对象
    Document document = Jsoup.parse(new File(path),'utf-8');
    //获取标签名为name的Elements对象
    Elements elements = document.getElementsByTag("name");
    //获取第一个元素并获取数据
    Element element = elements.get(0);
    String name = element.text();
    ```

- Jsoup选择器：

  - `Elements select(String cssQuery)`：cssQuery选择字符串参考selector类中定义的语法。

  - 标签选择器：字符串为标签名。

  - id选择器：字符串为 #id 。

  - 示例：获取student标签且number属性值为01下的age子标签。

    ```java
    Elements elements = document.select("student[number='01'] > age");
    ```

- XPath：是xml路径语言，用于在xml中查找信息。

  - 需要额外导入 jar 包。

  - 根据Document对象获取JXDocument对象：

    `JXDocument jxDocument = new JXDocument(document)`。

  - 使用XPath语法查询：

    `List<JXNode> jxNodes = jxDocument.selN(查询字符串)`。

  - 按标签名获取所有元素：`//标签名`。

  - 获取父标签下的子标签元素：`//父标签名/子标签名`。

  - 获取属性为值的标签：`//标签名[@属性='值']`。

  - JXNode 对象有 getElement方法获取元素。

