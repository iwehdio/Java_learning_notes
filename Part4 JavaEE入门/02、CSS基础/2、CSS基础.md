## 1、CSS基础

- HTML 表格布局的缺陷：

  1. 嵌套层级太多，容易出现嵌套顺序错乱。
  2. 页面不够灵活，想更改某一部分就需要改变整个表格布局。

  - 可以使用 DIV + CSS 来解决这些问题。

- HTML 的块标签：div 和 span ：

  - div 标签：`<div></div>`，默认占一行，自动换行。
  - span 标签：`<span></span>，`内容显示在同一行。

- CSS：层叠样式表（Cascading Style Sheets），用来美化 HTML 页面，提高代码的复用性。（HTML决定网页骨架，CSS优化网页表现效果）

- CSS简单语法：

  - 在一个 style 标签中，去编写 CSS 内容，最好在 style 标签写在 head 标签中。语法结构：

    ```css
    <style>
    选择器{
        属性名称1：属性的值1；
        属性名称2：属性的值2；
    }
    </style>
    ```

- 选择器帮助找到所要修饰的标签或者元素。

  - 元素选择器：

    ```html
    元素的名称{
        属性名称1：属性的值1；
        属性名称2：属性的值2；
    }
    
    <head>
    	<meta charset="utf-8">
    	<title></title>
    	<style>
    		div{
    			color: aquamarine;
    			font-size: 50px;
    		}
    	</style>
    </head>
    <body>
    	<div>AAAA</div>
    	<div>BBBB</div>
    </body>
    ```

  - ID 选择器：以 # 开头。ID 是指定的块标签的标识。ID 必须是唯一的，不然一般会默认设置第一个。

    ```html
    #ID{
        属性名称1：属性的值1；
        属性名称2：属性的值2；
    }
    
    <head>
    	<meta charset="utf-8">
    	<title></title>
        <style>
            #div1{
                color: aquamarine;
                font-size: 50px;
            }
        </style>
    </head>
    <body>
        <div id="div1">AAAA</div>
        <div>BBBB</div>
    </body>
    ```

  - 类选择器：以 . 开头。class 是指定的块标签的类别，可以将一类的块标签的 class 属性指定为相同。

    ```html
    . 类名{
        属性名称1：属性的值1；
        属性名称2：属性的值2；
    }
    
    <head>
    	<meta charset="utf-8">
    	<title></title>
    	<style>
    		.part1{
    			color: aquamarine;
    			font-size: 50px;
    		}
    	</style>
    </head>
    <body>
    	<div class="part1">AAAA</div>
    	<div>BBBB</div>
    	<div class="part1">CCCC</div>
    	<div>DDDD</div>
    </body>
    ```

  - CSS 选择器分组：多个选择器用 逗号 ，分隔。

  - 属性选择器：以标签类型开头，用 [ ] 中填写所指定的属性。然后样式会应用于具有这些属性的标签，同时也可以对这些属性指定值。

    ```html
    标签[属性]{
    	属性名称1：属性的值1；
        属性名称2：属性的值2；
    }
    
    <head>
    	<meta charset="utf-8">
    	<title></title>
    	<style>	/* 只会改变AAA的颜色 */
    		a[href][title='a']{
    			color: crimson;
    		}
    	</style>
    </head>
    <body>
    	<a href="#" title="a">AAA</a>
    	<a href="#" title="b">BBB</a>
    </body>
    ```

  - 后代选择器：标签1和标签2由空格分隔，选择标签1下的标签2的后代的样式，标签2是标签1的后代，也就是说对于标签1下包含的所有标签2都会改变样式。如果是标签1 > 标签2，则只改变父代的直接子代，不改变其子代的子代。

    ```html
    祖先 子代{
    	属性名称1：属性的值1；
        属性名称2：属性的值2；
    }
    父代 > 子代{
    	属性名称1：属性的值1；
        属性名称2：属性的值2；
    }
    <head>
    	<meta charset="utf-8">
    	<title></title>
    	<style>
    		h1 em{
    			color: crimson;
    		}
    	</style>
    </head>
    <body>
    	<h1>
    		<p>AAAAA</p>
    		<p>BBBBB</p>
    		<em>CCCC</em>
    		<p>
    			<em>DDDDD</em>	<!--如果是 > 则此项不变色-->
    		</p>
    	</h1>
    </body>
    ```
  
- 伪类选择器：主要用于超链接 a 标签。
  
  ```html
    <head>
    	<meta charset="utf-8">
    	<title></title>
    	<style>
    		a:link {color: black}	/*未访问的链接*/
    		a:visited {color: pink}	/*已访问的链接*/
    		a:hover {color: green}	/*鼠标移动到链接上*/
    		a:active {color: blue}	/*选定的链接*/
    	</style>
    </head>
    <body>
    	<a href="#">BBBB</a>
    </body>
  ```
  
- CSS 的引入方式：

  - 外部样式引入：创建一个 .css 文件，将 style 标签中的内容粘贴到 .css文件中。然后再在原 style 标签的位置，用`<link>`标签引入。

    rel 表示引入的类型，stylesheet 表示引入的是样式表。href 指向引入的 .css 文件的相对路径。

    ```css
    .part1{
    	color: aquamarine;
    	font-size: 50px;
    }
    ```

    ```html
    <head>
    	<meta charset="utf-8">
    	<title></title>
    	<link rel="stylesheet" href="style1.css"/>
    </head>
    <body>
    	<div class="part1">AAAA</div>
    	<div>BBBB</div>
    	<div class="part1">CCCC</div>
    	<div>DDDD</div>
    </body>
    ```

  - 内部样式：直接在 style 标签内编写 CSS 样式。

  - 行内样式：直接在标签中添加 style 属性，编写 CSS 样式。

    ```html
    <div style="color: aqua;">AAAA</div>
    ```

- CSS 的浮动：

  - 实现流式布局。不添加时会自动换行，添加后会自适应的显示在一行。

  - 浮动的元素会脱离正常的文档流，在正常的文档流中不占空间。

  - 通过指定 style 属性中的 float 属性，可以指定为 left 或 rigth。

    ```html
    <div style="float:left;width: 200px;height: 200px;background-color: aqua;"></div>
    		<div style="float:left;width: 200px;height: 200px;background-color: red;"></div>
    		<div style="float:left;width: 200px;height: 200px;background-color: blue;"></div>
    	</body>
    </html>
    ```

  - 使用 clear 属性，可以清除浮动。指定为 both 是两边都不允许浮动，left 是左边不允许浮动，right 是右边不允许浮动。

  - 如果想让下方的 div 不受上方浮动的影响，需要先添加一个空的 div 并指定 clear 属性，来清除浮动。
  
- 行高：指定 style 的 line-height 和 height 一致，就可以实现文字上下居中的效果。

- CSS 选择器的优先级：

  - 行内样式 > ID 选择器 > 类选择器 > 元素选择器。
  - 优先级高的会覆盖优先级低的设置。
  - 对于一个标签可以设置多个类，具体的效果遵循就近原则。也就是说与标签内的两个类顺序无关，选择距离近的类选择器效果。

- 盒子模型：外容器（也就是 boder 显示的边界）和容器中的内容之间存在内边距 padding 。外容器与其他盒子也存在外边距 margin 。可以通过修改边距改变盒子和内容的位置。

  - 内边距：padding-top， padding-right， padding-bottom， padding-left。
  - 外边距：margin-top， margin-right， margin-bottom， margin-left。
  - 简易设置：padding 和 margin 属性。
    - padding：10px； 上下左右都是10px。
    - padding：10px 20px； 上下是10px，左右是20px。
    - padding：10px 20px 30px； 上10px，右20px，下30px，左20px（默认与右边相同）。
    - 总之默认从上开始顺时针方向。 margin 属性同理。

- CSS 绝对定位：指定属性 position：absolute，开启绝对位置，然后设置 top、left 属性控制到顶部和左边的位置。



## 2、案例

- 案例1：网站首页的优化。
  
  - 用类选择器优化首页的实现.
  
  - 实现：
  
    ```html
    <head>
    	<meta charset="utf-8">
    	<title></title>
    	<style>
            /* logo、菜单、产品部分的样式 */
    		.logo{
    			float: left;
    			width: 33%;
    			height: 60px;
    			line-height: 60px;	/*行高*/
    		}
    		
    		.amenu{
    			color: white;
    			text-decoration: none; /*去掉下划线*/
    			line-height: 50px;
    		}
    		.product{
    			float:left;
    			text-align: center;
    			width: 16%;
    			height: 240px;
    		}
    	</style>
    </head>
    <body>
    	<div>
    		<!--第一部分 logo -->
    		<div>
    			<div class="logo">
    				<img src="../img/logo2.png" />
    			</div>
    			<div class="logo">
    				<img src="../img/header.png" />
    			</div>
    			<div class="logo">
    				<a href="#">登陆</a>
    				<a href="#">注册</a>
    			</div>
    		</div>
    		<div style="clear: both;"></div>
            <!--第二部分 菜单 -->
    		<div style="background-color: black;height: 50px;">
    			<a href="#" class="amenu">首页</a>
    			<a href="#" class="amenu">手机数码</a>
    			<a href="#" class="amenu">零食烟酒</a>
    			<a href="#" class="amenu">日用百货</a>
    		</div>
    		<!--第三部分 产品 -->
    		<div>
    			<img src="../img/1.jpg" width="100%"/>
    		</div>
    		<div>
    			<div><h2>最新商品<img src="../img/title2.jpg" /></h2></div>
    			<div style="float:left;width: 15%;height: 480px;">
    				<img src="../products/hao/big01.jpg" width="100%" height="100%"/>
    			</div>
    			<div style="float:left;width: 84%;height: 480px;">
    				<div style="height: 240px;width: 50%;float:left;">
    					<img src="../products/hao/middle01.jpg" width="100%" height="100%"/>
    				</div>
    				<div class="product">
    					<img src="../products/hao/small01.jpg" />
    					<p>榨汁机</p>
    					<p style="color: red;">￥998</p>
    				</div>
                 </div>
            </div>
        </div>
    </body>
    ```
