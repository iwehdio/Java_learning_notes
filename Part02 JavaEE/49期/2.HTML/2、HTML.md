# 2、HTML

## 1、Web概念概述

-  JavaWeb：使用Java语言开发基于互联网的项目
- 软件架构：
   	1. C/S: Client/Server 客户端/服务器端：在用户本地有一个客户端程序，在远程有一个服务器端程序.
       - 优点：用户体验好.
       - 缺点：开发、安装，部署，维护 麻烦.
   	2. B/S: Browser/Server 浏览器/服务器端：只需要一个浏览器，用户通过不同的网址(URL)，客户访问不同的服务器端程序。
       * 优点：开发、安装，部署，维护 简单。
       * 缺点：如果应用过大，用户的体验可能会受到影响；对硬件要求过高。
- B/S架构资源分类：
  1. 静态资源：
     * 使用静态网页开发技术发布的资源。
     * 特点：
       * 所有用户访问，得到的结果是一样的。
       * 如：文本，图片，音频、视频, HTML,CSS,JavaScript
       * 如果用户请求的是静态资源，那么服务器会直接将静态资源发送给浏览器。浏览器中内置了静态资源的解析引擎，可以展示静态资源
  2. 动态资源：
     * 使用动态网页及时发布的资源。
     * 特点：
       * 所有用户访问，得到的结果可能不一样。
       * 如：jsp/servlet,php,asp...
       * 如果用户请求的是动态资源，那么服务器会执行动态资源，转换为静态资源，再发送给浏览器

## 2、HTML基础

> Hyper Text Markup Language：超文本标记语言。
>
> 是设计网页的基础。

- 超文本：比普通文本更加强大，可以添加各种样式。

- 标记语言：通过一组标签（<关键字>）来对内容进行描述，由浏览器解释执行。

- HTML  语法规范：

  ```html
  <!DOCTYPE html>
  <!--
  	1、<!DOCTYPE html>是文档声明，不同的html规范声明形式也不同。
  	2、<html></html>是根标签。
  	3、html文件包含两部分，头部分和体部分。
  	4、<head></head>头部分主要是用来放置页面信息。
  	5、<body></body>体部分主要是用来放置页面内容。
  	6、通过标签对内容进行描述，标签通常都是由开始标签和结束标签组成，标签不区分大小写。
  -->
  <html>
  	<head>
          <!--网站的配置信息，指定浏览器打开页面的编码方式-->
  		<meta charset="utf-8" />
           <!--指定网站标题-->
  		<title>HTMLDemo</title>
  	</head>
  	<body>
           <!--显示内容-->
  		HelloWorld
  	</body>
  </html>
  ```

- 标签：

  - 对于需要包裹内容的标签，分为头尾`<标签></标签>`。
  - 对于不包裹内容的标签，一般只有一部分`<标签>`。
  - 对于有属性的标签，`<标签 属性名称="属性的值"></标签>`。
  - 标签的嵌套：先开始的先结束。
  - 带语义的标签，比如带语义加粗会有加重语气，对浏览更友好。

- 标签种类：

  - 标题标签：`<h1>标题内容</h1>`。h1表示是一级标题。h 后面的数可取 1 ~ 6 。
- 换行标签：`<br\>`。表示一个换行。
  
  - 段落标签：`<p>段落内容</p>`。表示段前段后换行。
- 加粗标签：`<b>加粗内容</b>`，表示加粗。
  
  - 斜体标签：`<i>斜体内容</i>`，表示斜体。
- 水平分割线标签：`<hr />`。显示水平分割线。
  - 字体标签：`<font></font>`，常用属性：

    - `<font color="red"></font>`。改变字体颜色。
  - `<font size="1"></font>`。改变字体大小，数字可取 1 ~ 7。
    - `<font face="宋体"></font>`。改变字体类型。
- 带语义加粗：`<strong>加粗内容</strong>`，表示加粗。
  
  - 带语义斜体：`<em>斜体内容</em>`，表示斜体。
- 图片标签：`<img />`，常用属性：
  
    - `<img src="../img/图片名" />`：指定文件相对路径。
    - 路径问题：`./`代表当前位置，`../`代表上一级路径，`../../`代表上上一级路径。
  - `<img width="100px">`：指定图片宽度。
    - `<img height="100px">`：指定图片高度。
  - 只指定宽高中的一个时，保持原宽高比。
    - `<img alt="">`：文件加载失败时的提示信息。
  - 需要将图片存储到项目下的 img 文件夹。
  - 无序列表标签：`<ul>所有列表项</ul>`。

    - 子标签列表项：`<li>列表内容</li>`。
    - 指定样式属性：`<ul type="disc"><ul>`，disc 黑点（默认），circle 圆圈，square 方块。
  - 有序列表标签：`<ol>所有列表项</ol>`。
  
    - 子标签列表项：`<li>列表内容</li>`。
    - 列表项按数字顺序排列。
    - 指定样式属性：`<ol type="a"></ol>`，指定序号的类型，可以为 1、a、A、i、Ⅰ。
  - 指定开始位置：`<ol start="1"></ol>`，指定序号从几开始，必须是数字。
  - 超链接标签：`<a></a>`，常用属性：

    - 指定链接地址：`<a href="网址">/a>`，指定所要跳转到的网址。
    - 如果是网络地址需要加上http协议，如果访问的是本网站的 html 文件,可以直接写文件路径。
    - `<nav></nav>`：具有导航性质的链接。
  - 指定打开方式：`<a target="打开方式"></a>`，为 _self 时是默认打开方式，在当前窗口打开；为 _blank 时是在新标签页打开。
- 表格标签：`<table>表格内容</table>`。常用属性：
  
    - 指定边框：`<table border="1px"></table>`，数值表示边框属性。
    - 指定宽度：`<table width="100px"></table>`。
    - 指定高度：`<table height="100px"></table>`。
    - 指定背景颜色：`<table bgcolor="red"></table>`。
  - align：指定对齐方式。
  - cellpadding：定义内容和单元格的距离。
  - cellspacing：定义单元格之间的距离。如果指定为0，则单元格的线会合为一条、
  - 行标签：`<tr></tr>`，表示一行。
  - 列标签：`<td></td>`，表示一列，一般在行标签中包含列标签。
    - 跨列合并：`<td colspan="2"></td>`，表示从该单元格与同行不同列的下一个单元格合并，合并后的列数为 2 。同时需要将原来处于被合并的位置的单元格删除。
    - 跨行合并：`<td rowspan="2"></td>`，表示从该单元格与同列不同行的下一个单元格合并，合并后的行数为 2 。同时需要将原来处于被合并的位置的单元格删除。
    - 背景图片：`<td background=".jpg"></td>`，注意背景不会讲表格撑大，需要自行设置大小。
  - 表头标签：`<th></th>`，表示表头的单元格。
      - `<caption></caption>`：表格标题。
      - `<thead></thead>`：表示表格的头部分。
      - `<tbody></tbody>`：表示表格的体部分。
      - `<tfoot></tfoot>`：表示表格的脚部分。
  - 表格的嵌套：在之前表格的最小元素单元格即`<td></td>`标签中加入表格标签`<table>表格内容</table>`。可使用属性width为100%使其宽度充满单元格。
  - 语义化标签：html5 中为了提高程序的可读性，提供了一些标签。
      - 页眉：`<header></header>`。
      - 页脚：`<footer></footer>`。
  - 输入标签：`<input />`，基本属性：
  - 输入类型：`<input type="text"/>`。
      - text 指输入类型为文本。
      - password 指输入类型为密码。
      - file 指输入类型为文件。
      - radio 指单选圆点（如果要有单选效果，需要指定相同的 name 属性）。
      - chechbox 指多选方框。
    - button 指普通按钮 。
      - reset 指重置按钮。
      - submit 指提交按钮，同时要指定 value 为按钮文本，需要结合表单标签。
      - hidden 指隐藏域，可以用 value 来存放页面上的ID信息。
      - date 指日期。
      - number 指数字。
  - 指定参数名称： `<input name="A"/>`。
    - 给输入项取名：`<input id="A"/>`，便于找到并操作输入项。
    - 提示信息：`<input placeholder="输入提示"/>`。
  - 文本框：`<textarea></textarea>`，基本属性：
    - 文本框大小：`<textarea cols="20" rows="5"></textarea><br />`，列数（宽）为 cols ，行数（高）为 rows 。
  - 下拉菜单：`<select>菜单内容</select>`。
    - 菜单内容：`<option>选项内容</option>`。
  - 表单标签：`<form></form>`，其中需要包裹所有需要提交的内容。基本属性：
    - 提交的地址：`<form action="../首页/index.html"></form>`
    - 提交方式：`<form method="get"></form>`，可以为 get 或 post 方式，默认为 get。get 方式会将参数拼接在链接后面，有大小限制；post 方式会将参数封装在请求体中，没有大小限制。
  - 框架标签：`<frameset>框架内容</frameset>`，如果要使用框架标签，就要删除 body 标签。
    - 框架内容：`<frame src=""/>`，src 指定框架内容的 html 文件。
    - 页面框架划分：`<frameset cols="10%,30%,*"></frameset>`，cols 表示按列划分，rows 表示按行划分，* 表示占满剩余位置。
    - 框架标签可以嵌套，用于多重划分。
    - 可以用 name 属性为框架内容起名字，然后用超链接的 target 属性指定新页面在哪一个框架内容中打开。



## 3、案例

- 案例1：

  - 需求分析：

    1. 网站内容标题。
    2. 水平分割线。
    3. 四个段落。
    4. 第一个段落字体红色。

  - 实现：

    ```html
    <!DOCTYPE html>
    <html>
    	<head>
    		<meta charset="utf-8">
    		<title>信息通知</title>
    	</head>
    	<body>
    		<h1>西安交通大学关于调整研究生课程教学方式的通知</h1>
    		
    		<hr />
    		
    		<p><font color="blue">各学院（部、中心），有关教学单位</font>：
    为加强新型冠状病毒感染的肺炎疫情防控工作，，经学校研究，将2019-2020学年春季学期研究生课程教学方式进行调整。具体安排如下：</p>
    		<p>1.摸底动员。各学院和相关教学单位要高度重视，做好开学前准备工作和推迟开学时间的相关教学工作预案。</p>
    		<p>2.教师培训。2月11日-2月16日，研究生院与教发中心、网信中心将通过多种形式对教师、助教和修习线上课程的学生开展线上教学培训。</p>
    		<p>3.线上课表的发布（2月15日之前）。在摸底基础上，研究生院统筹规划，确定线上课程课表（校管课由研究生院负责，院管课由学院负责），并在研究生院、学院主页和思源学习空间上进行发布。</p>
    		<p>4.正式开课。2月17日（第1周）起，各任课教师按照在线授课课表安排，优先采用教学平台录播、教学实录直播等方式，配合思源学习空间线上辅导进行在线教学，学生依据个人课表按时学习，保障教学稳定高效运行。</p>
    	</body>
    </html>
    ```

  - 效果：

    ![HTML案例1](F:\Snipaste\HTML案例1.png)

- 案例2：

  - 使用表格的一些基本属性。

  - 实现：

    ```html
    <!DOCTYPE html>
    <html>
    	<head>
    		<meta charset="utf-8">
    		<title></title>
    	</head>
    	<body>
    		<table width="600px" border="1px">
    			<tr align="center">
    				<td align="center" bgcolor="red">1</td>
    				<td>2</td>
    				<td>3</td>
    				<td>4</td>
    			</tr>
    			<tr>
    				<td>1</td>
    				<td colspan="2" rowspan="3">
    					<table width="100%" border="2px">
    						<tr>
    							<td>11</td>
    							<td>12</td>
    							<td>13</td>
    						</tr>
    						<tr>
    							<td>21</td>
    							<td>22</td>
    							<td>23</td>
    						</tr>
    					</table>
    				</td>
    				<td>4</td>
    			</tr>
    			<tr>
    				<td>1</td>
    				<td>4</td>
    			</tr>
    			<tr>
    				<td>1</td>
    				<td>4</td>
    			</tr>
    		</table>
    	</body>
    </html>
    
    ```

  - 效果：![HTML案例2](F:\Snipaste\HTML案例2.png)

- 案例3：

  - 简单网站首页。

  - 实现：

    ```html
    <!DOCTYPE html>
    <html>
    	<head>
    		<meta charset="utf-8">
    		<title></title>
    	</head>
    	<body>
    		<table width="100%">
    			<!--第一部分：LOGO，一行三列-->
    			<tr align="center">
    				<td>
    					<table  width="100%">
    						<tr>
    							<td>
    								<img src="../img/logo2.png" />
    							</td>
    							<td>
    								<img src="../img/header.png" />
    							</td>
    							<td>
    								<!-- # 表示锚定不跳转，相当于占位符-->
    								<a href="#">登陆</a>
    								<a href="#">注册</a>
    								<a href="#">购物车</a>
    							</td>
    						</tr>
    					</table>
    				</td>
    			</tr>
    			<!--第二部分：导航栏，五个超链接-->
    			<tr bgcolor="black">
    				<td height="50px">
    					<a href="#"><font color="white">首页</font></a>
    					<a href="#"><font color="white">手机数码</font></a>
    					<a href="#"><font color="white">零食烟酒</font></a>
    					<a href="#"><font color="white">日用百货</font></a>
    				</td>
    			</tr>
    			<!--第三部分：图片-->
    			<tr>
    				<td>
    					<img src="../img/1.jpg" width="100%"/>
    				</td>
    			</tr>
    			<!--第四部分：三行七列表格-->
    			<tr>
    				<td>
    					<table width="100%" height="500px">
    						<tr>
    							<td colspan="7">
    								<h3>最新商品<img src="../img/title2.jpg" /></h3>
    							</td>
    						</tr>
    						<tr align="center">
    							<td rowspan="2" width="206px" height="480px">
    								<img src="../products/hao/big01.jpg" />
    							</td>
    							<td colspan="3"	height="240px">
    								<img src="../products/hao/middle01.jpg" />
    							</td>
    							<td>
    								<img src="../products/hao/small06.jpg" />
    								<p>洗衣机</p>
    								<p><font color="red">￥998</font></p>
    							</td>
    							<td>
    								<img src="../products/hao/small06.jpg" />
    								<p>洗衣机</p>
    								<p><font color="red">￥998</font></p>
    							</td>
    							<td>
    								<img src="../products/hao/small06.jpg" />
    								<p>洗衣机</p>
    								<p><font color="red">￥998</font></p>
    							</td>
    						</tr>
    						<tr align="center">
    							<td>
    								<img src="../products/hao/small06.jpg" />
    								<p>洗衣机</p>
    								<p><font color="red">￥998</font></p>
    							</td>
    							<td>
    								<img src="../products/hao/small06.jpg" />
    								<p>洗衣机</p>
    								<p><font color="red">￥998</font></p>
    							</td>
    							<td>
    								<img src="../products/hao/small06.jpg" />
    								<p>洗衣机</p>
    								<p><font color="red">￥998</font></p>
    							</td><td>
    								<img src="../products/hao/small06.jpg" />
    								<p>洗衣机</p>
    								<p><font color="red">￥998</font></p>
    							</td>
    							<td>
    								<img src="../products/hao/small06.jpg" />
    								<p>洗衣机</p>
    								<p><font color="red">￥998</font></p>
    							</td>
    							<td>
    								<img src="../products/hao/small06.jpg" />
    								<p>洗衣机</p>
    								<p><font color="red">￥998</font></p>
    							</td>
    						</tr>
    					</table>
    				</td>
    			</tr>
    			<!--第五部分：一张图片-->
    			<tr>
    				<td>
    					<img src="../products/hao/ad.jpg" width="100%"/>
    				</td>
    			</tr>
    			<!--第六部分：一张图片-->
    			<tr>
    				<td>
    					<img src="../img/footer.jpg" width="100%" />
    				</td>
    			</tr>
    			<!--第七部分：超链接-->
    			<tr>
    				<td align="center">
    					<a href="#">关于我们</a>
    					<a href="#">联系我们</a>
    					<a href="#">招贤纳士</a>
    					<a href="#">友情链接</a>
    					<a href="#">配送服务</a>
    					<br />
    				</td>
    			</tr>
    		</table>
    	</body>
    </html>
    
    ```

  - 效果：

    ![HTML案例3A](F:\Snipaste\HTML案例3A.png)

    ![HTML案例3B](F:\Snipaste\HTML案例3B.png)

- 案例4：

  - 注册页面。

  - 实现：

    ```html
    <!DOCTYPE html>
    <html>
    	<head>
    		<meta charset="utf-8">
    		<title></title>
    	</head>
    	<body>
    		<table width="100%">
    			<!--第一部分：LOGO，一行三列-->
    			<tr align="center">
    				<td>
    					<table  width="100%">
    						<tr>
    							<td>
    								<img src="../img/logo2.png" />
    							</td>
    							<td>
    								<img src="../img/header.png" />
    							</td>
    							<td>
    								<a href="#">登陆</a>
    								<a href="#">注册</a>
    								<a href="#">购物车</a>
    							</td>
    						</tr>
    					</table>
    				</td>
    			</tr>
    			<!--第二部分：导航栏，五个超链接-->
    			<tr bgcolor="black">
    				<td height="50px">
    					<a href="#"><font color="white">首页</font></a>
    					<a href="#"><font color="white">手机数码</font></a>
    					<a href="#"><font color="white">零食烟酒</font></a>
    					<a href="#"><font color="white">日用百货</font></a>
    				</td>
    			</tr>
    			<tr>
    				<td background="../image/regist_bg.jpg" height="500px">
    					<table bgcolor="white" align="center" width="60%" height="80%">
    						<tr>
    							<td>
    								<table width="60%" align="center">
    									<tr>
    										<td colspan="2"><font color="#1B6D85" size="5">会员注册</font> USER REGIST
    										</td>
    									</tr>
    									<tr height="20px"></tr>
    									<tr>
    										<td>用户名</td>
    										<td><input type="text" placeholder="请输入用户名"/></td>
    									</tr>
    									<tr>
    										<td>密码</td>
    										<td><input type="password" placeholder="请输入密码"/></td>
    									</tr>
    									<tr>
    										<td>Email</td>
    										<td><input type="email" placeholder="请输入邮箱"/></td>
    									</tr>
    									<tr>
    										<td>性别</td>
    										<td><input type="radio" name="gender"/>男<input type="radio" name="gender"/>女
    										</td>
    									</tr>
    									<tr>
    										<td>出生日期</td>
    										<td><input type="date" /></td>
    									</tr>
    									<tr>
    										<td>验证码</td>
    										<td><input type="text" placeholder="验证码"/></td>
    									</tr>
    									<tr>
    										<td></td>
    										<td><input type="submit" value="注册"/></td>
    									</tr>
    								</table>
    							</td>
    						</tr>
    					</table>
    				</td>
    			</tr>
    			<!--第六部分：一张图片-->
    			<tr>
    				<td>
    					<img src="../img/footer.jpg" width="100%" />
    				</td>
    			</tr>
    			<!--第七部分：超链接-->
    			<tr>
    				<td align="center">
    					<a href="#">关于我们</a>
    					<a href="#">联系我们</a>
    					<a href="#">招贤纳士</a>
    					<a href="#">友情链接</a>
    					<a href="#">配送服务</a>
    					<br />
    				</td>
    			</tr>
    		</table>
    	</body>
    </html>
    
    ```

  - 效果：![HTML案例4](F:\Snipaste\HTML案例4.png)

  


