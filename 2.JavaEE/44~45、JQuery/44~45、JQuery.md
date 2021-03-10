## 44~45、JQuery

## 1、JQuery基础

> JQuery是一个 JavaScript 框架，用于封装 JavaScript 的常用功能。

- JQuery 对象与 JS 对象区别：以获取div标签为例。
  - JS 对象获取：`var divs = document.getElementsByTageName("div")`。
  - JQuery 对象获取：`var $divs = $("div")`。
  - JS 获取到的是一个集合，JQ 获取到的是一个对象。但都可以当作数组来使用。
  - JQuery 对象在操作时更加方便，但和 JS 的方法是不通用的。
- JQuery 对象与 JS 对象转换：
  - JS 转 JQuery：`$(js对象)`。
  - JQuery 转 JS ：`jq对象[索引]`或`jq对象.get(索引)`。
- 事件绑定：
  1. 用选择器获取元素。
  2. `jq对象.事件(function(){函数体})`，例如为 id 为 id 的按钮绑定单击事件：`$("#id").click(function(){})`。
- 入口函数：
  - dom 文档加载完成后执行该函数中的代码，用于`<script>`标签在`<body>`标签体前时。
  - 格式：`$(function(){函数体})`。
  - 入口函数与`windows.onload`的区别：后者只能定义一次，多次定义会覆盖。而前者可以定义多次，会按序执行。
- 样式控制：
  - 设置样式：`jq对象.css("css样式键", "css样式值")`。
  - 获取样式：`jq对象.css("css样式键")`。

- 选择器：筛选有相似特征的元素。
  - 基本选择器：
    - 元素选择器：`$("html标签名")`。
    - id 选择器：`$("#id属性")`。
    - 类选择器：`$(".类名")`。
    - 并集选择器：`$("属性值1, 属性值2")`。
  - 层级选择器：
    - 后代选择器：`$("A B")`，选择A元素下的B元素。
    - 子代选择器：`$("A > B")`，选择A元素下的直接子代B元素（不包括子代的子代）。
  - 属性选择器：
    - 属性名称选择器：`$("元素[属性名]")`。
    - 属性选择器：`$("元素[属性名='值']")`。
      - `!=`表示没有该属性，或者值不匹配。
      - `^=`表示属性值以该值开始的。
      - `$=`表示属性值以该值结束的。
      - `*=`表示属性值中包含该值的。
    - 复合属性选择器：`$("元素[属性名1='值1'][属性名2='值2']")`。同时满足两个属性要求的元素。
  - 过滤选择器
    - 首元素选择器 ：`元素:first`，获得选择的元素中的第一个元素。
    - 尾元素选择器：`元素:last`，获得选择的元素中的最后一个元素。
    - 非元素选择器：`元素:not(selector)`，不包括指定内容的元素。selector 中可以为其他选择器语法。
    - 偶数选择器：`元素:even`，索引为偶数的元素，从 0 开始计数。
    - 奇数选择器：`元素:odd，索引为奇数的元素，从 0 开始计数。
    - 等于索引选择器：`元素:eq(index)`，获得指定索引的元素。
    - 大于索引选择器：`元素:gt(index)`，获得大于指定索引的元素。
    - 小于索引选择器：`元素:lt(index)`，获得小于指定索引的元素。
    - 标题选择器：`:header`，获得标题（h1~h6）元素，固定写法。
  - 表单过滤选择器：
    - 可用元素选择器：`元素:enabled`，获得可用元素。
    - 不可用元素选择器：`元素:disabled`获得不可用元素。
    - 选中选择器：`元素:checked`，获得单选/复选框选中的元素。
    - 选中选择器：`下拉列表 > option:selected`，获得下拉框选中的元素。
- DOM 操作：对元素进行操作。
  - 内容操作：
    - `html(参数)`：获取（无参）/ 设置元素（参数）的标签体内容。
    - `text(参数)`：获取（无参）/ 设置元素（参数）的标签体纯文本内容。设置时会抹除格式标签。
    - `val(参数)`：获取（无参）/ 设置元素（参数）的 value 属性。
  - 属性操作：
    - 通用属性操作：
      - `attr(参数)`：获取（无参） / 设置元素（键值对）的属性。
      - `removeAttr(参数)`：删除属性（键）。
      - `prop(参数)`：获取 （无参）/ 设置元素（键值对）的属性。
      - `removeProp(参数)`：删除属性（键）。
      - 如果操作的是元素的固有属性，则使用 prop；如果操作的是自定义属性，则使用 attr 。
    - 类属性操作：
      - `addClass(参数)`：添加 class 属性（值）。
      - `removeClass(参数)`：移除 class 属性（值）。
      - `toggleClass(参数)`：切换 class 属性（值）。如果存在该属性就删除，如果不存在该属性则就添加。
    - CRUD 操作：
      - `append(参数)`：父元素将子元素追加到末尾。
        - 例：`元素1.append(元素2)`：将元素2添加到元素1内部，并且在末尾。
      - `prepend(参数)`：父元素将子元素追加到开头。
        - 例：`元素1.prepend(元素2)`：将元素2添加到元素1内部，并且在开头。
      - `appendTo(参数)`：与 append 方法中父子元素位置互换。
      - `prependTo(参数)`：与 prepend 方法中父子元素位置互换。
      - `after(参数)`：添加元素到元素后边。
        - 例：`元素1.after(元素2)`：将元素2添加到元素1后边。二者是兄弟关系。
      - `before(参数)`：添加元素到对象前边。
      - `insertAfter(参数)`：与 after 方法中兄弟元素位置互换。
      - `insertBefore(参数)`：与 before 方法中兄弟元素位置互换。
      - `remove()`：移除元素。
      - `empty()`：清空元素的所有后代元素。但是保留当前对象及其属性节点。



## 2、JQuery高级

- 显示和隐藏元素的动画效果：
  - 默认（折叠）显示和隐藏方式：
    1. `show([speed], [easing], [fn])`：显示。
    2. `hide([speed], [easing], [fn])`：隐藏。
    3. `toggle([speed], [easing], [fn])`：切换。
    4. speed：三种预定速度之一，"slow"、 "normal"、 "fast" 或传入毫秒值。
    5. easing：用来指定切换效果。默认是 "swing"，可用参数 "linear" 。swing 表示动画运行起末慢中间快， linear 表示动画匀速。
    6. fn：动画每完成一次，运行一次此函数，可不传入。
  - 滑动显示和隐藏效果：
    1. `slidDown([speed], [easing], [fn])`：显示。
    2. `slideUp([speed], [easing], [fn])`：隐藏。
    3. `slideToggle([speed], [easing], [fn])`：切换。
  - 淡入淡出显示和隐藏效果：
    1. `fadeIn([speed], [easing], [fn])`：显示。
    2. `fadeOut([speed], [easing], [fn])`：隐藏。
    3. `fadeToggle([speed], [easing], [fn])`：切换。
- JQuery 的遍历：
  1. `jq对象.each(callback)`：
     - callback 是定义的回调函数 function(){} 。会对 jq 对象中的每个元素执行回调函数，在回调函数中，用 this 表示当前遍历到的元素的 js 对象。
     - `jq对象.each(function(index, element){})`：传入 index 和 element ，可以获取元素中的索引和元素的 js 对象。
     - 在回调函数的语法中，不能使用 break 和 continue，但是可以通过 return false 或 return true 实现相同的效果。
  2. `$.each(object, [callback])`：
     - 与方法1基本类型，其中的一个区别是，传入的对象可以为 js 对象数组。
  3. `for(子元素 of jq对象)`：3.0 版本后的方式，类似于 Java 中的增强 for 。
- JQuery 的事件绑定：
  1. 标准绑定方式：`jq对象.事件方法(callback)`。
     - 如果不传入回调函数，则 jq 对象会执行所设置的事件。
  2. on 绑定事件 / off 解绑事件：
     - `jq对象.on("事件名称", callback)`：绑定事件。
     - `jq对象.off("事件名称")`：解绑事件。不传入参数则为全部解绑。
  3. 事件切换：
     - `jq对象.toggle(fn1, fn2)`：第一次点击执行 fn1， 第二次点击执行 fn2 ，可传入多个值，会循环执行。
     - 1.9 版本以上，该方法被删除。可以通过插件恢复。

- JQuery 插件机制：增强扩展功能。

  - 实现插件的方式：

    - `$.fn.extend(object)`：增强通过JQuery获取的对象的功能，对象级的插件。即`$(选择器)`获得的对象。
    - `$.extend(object)`：增强JQuery对象自身的功能，全局性的插件。即`$`对象。

  - 定义格式：

    ```js
    $.fn.extend({
        方法名:function(传入参数){
            /* 功能实现 */
        }
    });
    ```

  - 可以用 this 获取调用该方法的对象。

## 3、案例

- 表格的隔行换色：

  ```html
  <script>
  	$(function () {
  		$("tr:gt(1):odd").css("backgroundColor", "yellow");
  		$("tr:gt(1):even").css("backgroundColor", "pink");
  	})
  </script>
```
  
- 全选与全不选：

  ```html
  <script>
  	function selectAll(obj) {
  		$("input[type='checkbox']").prop("checked", obj.checked);
  	}
  </script>
  ```

  - 先为全选复选框绑定 onclick="selectAll(this)” 单击事件。

- 将表情添加到发言框：

  ```html
  <script>
      $(function () {
          $("li > img").click(function () {
              $(".word").append($(this).clone());
          })
      });
  </script>
  ```

  - 为所有表情添加单击事件。
  - 如果不同 clone() 克隆方法，添加到发言框后，上方的选择框表情会消失。

- 多选列表的选中移动：

  ```html
  <script>
  	$(function () {
  		$("#toRight").click(function () {
  			$("#rightName").append($("#leftName > option:selected"));
  		});
  		$("#toLeft").click(function () {
  			$("#rightName > option:selected").appendTo($("#leftName"));
  		});
  	});
  </script>
  ```

- 广告显示与隐藏：

  - JQuery 提供的显示与隐藏动画，其实就是控制 style 中的 display 为 none，实现隐藏。
  
  ```html
  <script>
      $(function () {
          setTimeout(adShow, 1000);
          setTimeout(adHide, 2000);
      })
      function adShow() {
          $("#ad").slideDown("normal");
      }
      function adHide() {
          $("#ad").slideUp("normal");
      }
  </script>
  ```



- 随机选取图片：

  - 点击开始后在小相框里快速循环候选图片，点击结束后将选中图片显示到大相框里。

  ```html
  <!DOCTYPE html>
  <html>
  <head>
      <meta charset="UTF-8">
      <title>jquery案例</title>
      <script type="text/javascript" src="../js/jquery-3.3.1.min.js"></script>
      <script language='javascript' type='text/javascript'>
          var imgs = ["./img/man00.jpg",
                      "./img/man01.jpg",
                      "./img/man02.jpg",
                      "./img/man03.jpg",
                      "./img/man04.jpg",
                      "./img/man05.jpg",
                      "./img/man06.jpg",
                      ];
          var TimeoutId;//定时器的id
          var index;//随机角标
          $(function () {
              $("#startID").click(function () {
  
                  $("#startID").prop("disabled", true);
                  $("#stopID").prop("disabled", false);
                  TimeoutId = setInterval(function () {
                      index = Math.floor(Math.random() * 7);
                      $("#img1ID").prop("src", imgs[index]);
                  }, 20)
              });
              $("#stopID").click(function () {
                  $("#startID").prop("disabled", false);
                  $("#stopID").prop("disabled", true);
                  clearInterval(TimeoutId);
                  $("#img2ID").prop("src", imgs[index]);
                  $("#img2ID").hide();
                  $("#img2ID").show(1000);
              });
          });
      </script>
  </head>
  <body>
  <!-- 小像框 -->
  <div style="border-style:dotted;width:160px;height:100px">
      <img id="img1ID" src="../img/man00.jpg" style="width:160px;height:100px"/>
  </div>
  <!-- 大像框 -->
  <div
          style="border-style:double;width:800px;height:500px;position:absolute;left:500px;top:10px">
      <img id="img2ID" src="../img/man00.jpg" width="800px" height="500px"/>
  </div>
  <!-- 开始按钮 -->
  <input
          id="startID"
          type="button"
          value="点击开始"
          style="width:150px;height:150px;font-size:22px">
  <!-- 停止按钮 -->
  <input
          id="stopID"
          type="button"
          value="点击停止"
          style="width:150px;height:150px;font-size:22px">
  </body>
  </html>
  ```

  