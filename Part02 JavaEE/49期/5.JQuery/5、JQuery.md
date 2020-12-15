# 5、jQuery

## 1、jQuery

- JQuey：是一个JS库，优化 HTML 文档操作、动画设计、事件处理和Ajax交互。封装功能代码，将JS代码和HTML代码分离，提高工作效率。

- 因为jQuery 是一个库，首先需要导入：`<script type="text/javascript" src="../js/jquery-1.11.0.js"></script>`。

- 在文档载入完成时执行函数（.ready()方法）：

  ```javascript
  jQuery(document).ready(function(){
     //执行内容 
  });
  //jQuery 可以简写为 $ 符号
  $(document).ready(function(){
     //执行内容 
  });
  //对于.ready()方法还可写为
  $(function(){
     //执行内容 
  });
  ```

- JQ中根据ID查找元素：`$("ID")`。

- JQ实现按钮改变内容：

  ```javascript
  $(function(){
      //用ID为btnID的按钮修改ID为divID的内容
      $("#btnID").click(function(){   
          $("#divID").html("JQ修改内容")；
      })；
  })；
  ```

- JQ 和 JS 对象之间的转换：

  - JQ对象只能使用JQ的方法，JS对象只能使用JS的方法。

  - JS对象转JQ对象：

    ```javascript
    //获取JS对象
    var div = document.getElementById("123");
    //转成JQ对象
    var $div = $(div);
    ```

  - JQ对象转JS对象：

    ```javascript
    //获取JQ对象
    var $div = $("#divID1");
    //转成JS对象
    var jsdiv = $div.get(0);
    //或
    var jsdiv = $div[0];
    ```

- 