# 35、BootStrap

## 1、BootStrap

> 一个前端开发框架，基于HTML、CSS和JavaScript，使得Web开发更加快捷。

- 优点：

  - 定义了许多css样式和js插件。
  - 响应式布局，同一套页面可以兼容不同的分辨率设备。

- 使用前，先将BootStrap下的css、fonts和js文件夹引入到项目目录下。

- 基本模板：

  ```html
  <!DOCTYPE html>
  <html lang="zh-CN">
  <head>
      <meta charset="utf-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1">
      <!-- 上述3个meta标签*必须*放在最前面(分别表示编码、受IE支持、视口宽度为设备宽度且缩放比例为1)，任何其他内容都*必须*跟随其后！ -->
      <title>Bootstrap HelloWorld</title>
      
  	<!-- 引入资源文件 --->
      <!-- Bootstrap -->
      <link href="../css/bootstrap.min.css" rel="stylesheet">
  
      <!-- jQuery (Bootstrap 的所有 JavaScript 插件都依赖 jQuery，所以必须放在前边) -->
      <script src="../js/jquery-3.2.1.min.js"></script>
      <!-- 加载 Bootstrap 的所有 JavaScript 插件。你也可以根据需要只加载单个插件。 -->
      <script src="../js/bootstrap.min.js"></script>
  </head>
  <body>
  <h1>你好，世界！</h1>
  
  </body>
  </html>
  ```

- 响应式布局：原依赖于栅格系统。

  - 栅格系统：将一行平均分为12个格子，可以指定元素占几个格子。不同分辨率下，每个格子的大小也不同。
  - 步骤：
    1. 定义容器。相当于 html 中的 table 。
       - `container`：在不同分辨率设备上有固定宽度，有留白。
       - `container-fluid`：占满视口的宽度。
    2. 定义行。相当于 html 中的 tr 。
       - `row`：行属性。
    3. 定义元素。相当于 html 中的 td 。指定元素在不同设备上所占的格子数目。
       - 格式：`col-设备代号-格子数目`
       - 设备代号：xs （<768px超小屏幕，手机）；sm（>=768px小屏幕，平板）；md（>=992px中等屏幕，桌面显示器）；lg（>=1200px超大平面，大桌面显示器）。
  - 注意事项：
    1. 如果一行的定义的格子超出12个，则会自动换行。
    2. 栅格类属性向上兼容，即分辨率大的设备兼容分辨率小的。
    3. 如果设备宽度小于了设备代号所代表的最小值，会一个元素占满一行。

- 全局CSS样式示例：

  - 按钮：
    - `class="btn btn-default"`：默认按钮样式。
    - `class="btn btn-link"`：链接按钮样式。
    - 还可以为 primary、success、info、warning、danger等。
    
    ```html
    <a class="btn btn-default" href="#" role="button">Link</a>
    <button class="btn btn-default" type="submit">Button</button>
    <input class="btn btn-default" type="button" value="Input">
    ```
    
  - 图片：
    - `class="img-responsive"`：图片大小始终占满页面宽度。
    - `class="img-rounded"`：图片形状为方形。还可以为圆形circle和相框thumbnail。
    
    ```html
    <img src="..." class="img-responsive" alt="Responsive image">
    <img src="..." alt="..." class="img-rounded">
    <img src="..." alt="..." class="img-circle">
    <img src="..." alt="..." class="img-thumbnail">
    ```
    
  - 表格：
    - `class="table table-boedered"`：表格带边框。
    - `class="table table-hover"`：单行选中效果。
    
    ```html
    table class="table table-hover table-bordered">
        <header>
            <td>表头1</td>
            <td>表头2</td>
            <td>表头3</td>
        </header>
        <tr>
            <td>1</td>
            <td>2</td>
            <td>3</td>
        </tr>
        <tr>
            <td>1</td>
            <td>2</td>
            <td>3</td>
        </tr>
    </table>
    ```
    
  - 表单：
    - `class="form-control"`：对于每一个表单项。
    - `class="form-group"`：每一行。
    
    ```html
    <form class="form-inline">
      <div class="form-group">
        <label for="exampleInputName2">Name</label>
        <input type="text" class="form-control" id="exampleInputName2" placeholder="Jane Doe">
      </div>
      <div class="form-group">
        <label for="exampleInputEmail2">Email</label>
        <input type="email" class="form-control" id="exampleInputEmail2" placeholder="jane.doe@example.com">
      </div>
      <button type="submit" class="btn btn-default">Send invitation</button>
    </form>
    ```

- 组件示例：

  - 导航条：`class="navbar navbar-default"`。

    ```html
    <nav class="navbar navbar-default">
      <div class="container-fluid">
        <!-- Brand and toggle get grouped for better mobile display -->
        <div class="navbar-header">
          <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false">
            <span class="sr-only">Toggle navigation</span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
          </button>
          <a class="navbar-brand" href="#">Brand</a>
        </div>
    
        <!-- Collect the nav links, forms, and other content for toggling -->
        <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
          <ul class="nav navbar-nav">
            <li class="active"><a href="#">Link <span class="sr-only">(current)</span></a></li>
            <li><a href="#">Link</a></li>
            <li class="dropdown">
              <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">Dropdown <span class="caret"></span></a>
              <ul class="dropdown-menu">
                <li><a href="#">Action</a></li>
                <li><a href="#">Another action</a></li>
                <li><a href="#">Something else here</a></li>
                <li role="separator" class="divider"></li>
                <li><a href="#">Separated link</a></li>
                <li role="separator" class="divider"></li>
                <li><a href="#">One more separated link</a></li>
              </ul>
            </li>
          </ul>
          <form class="navbar-form navbar-left">
            <div class="form-group">
              <input type="text" class="form-control" placeholder="Search">
            </div>
            <button type="submit" class="btn btn-default">Submit</button>
          </form>
          <ul class="nav navbar-nav navbar-right">
            <li><a href="#">Link</a></li>
            <li class="dropdown">
              <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">Dropdown <span class="caret"></span></a>
              <ul class="dropdown-menu">
                <li><a href="#">Action</a></li>
                <li><a href="#">Another action</a></li>
                <li><a href="#">Something else here</a></li>
                <li role="separator" class="divider"></li>
                <li><a href="#">Separated link</a></li>
              </ul>
            </li>
          </ul>
        </div><!-- /.navbar-collapse -->
      </div><!-- /.container-fluid -->
    </nav>
    ```

  - 分页条：`class="pagination"`。

    ```html
    <nav aria-label="Page navigation">
      <ul class="pagination">
        <li>
          <a href="#" aria-label="Previous">
            <span aria-hidden="true">&laquo;</span>
          </a>
        </li>
        <li><a href="#">1</a></li>
        <li><a href="#">2</a></li>
        <li><a href="#">3</a></li>
        <li><a href="#">4</a></li>
        <li><a href="#">5</a></li>
        <li>
          <a href="#" aria-label="Next">
            <span aria-hidden="true">&raquo;</span>
          </a>
        </li>
      </ul>
    </nav>
    ```

- 插件示例：

  - 轮播图：`class="carousel slide"`。
  
    ```html
    <div id="carousel-example-generic" class="carousel slide" data-ride="carousel">
      <!-- Indicators -->
      <ol class="carousel-indicators">
        <li data-target="#carousel-example-generic" data-slide-to="0" class="active"></li>
        <li data-target="#carousel-example-generic" data-slide-to="1"></li>
        <li data-target="#carousel-example-generic" data-slide-to="2"></li>
      </ol>
    
      <!-- Wrapper for slides -->
      <div class="carousel-inner" role="listbox">
        <div class="item active">
          <img src="..." alt="...">
          <div class="carousel-caption">
            ...
          </div>
        </div>
        <div class="item">
          <img src="..." alt="...">
          <div class="carousel-caption">
            ...
          </div>
        </div>
        ...
      </div>
    
      <!-- Controls -->
      <a class="left carousel-control" href="#carousel-example-generic" role="button" data-slide="prev">
        <span class="glyphicon glyphicon-chevron-left" aria-hidden="true"></span>
        <span class="sr-only">Previous</span>
      </a>
      <a class="right carousel-control" href="#carousel-example-generic" role="button" data-slide="next">
        <span class="glyphicon glyphicon-chevron-right" aria-hidden="true"></span>
        <span class="sr-only">Next</span>
      </a>
    </div>
    ```

## 2、案例

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Bootstrap HelloWorld</title>
    <!-- Bootstrap -->
    <link href="css/bootstrap.min.css" rel="stylesheet">
    <script src="js/jquery-3.2.1.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
    <style>
        .paddtop{
            padding-top: 10px;
        }
        .search-btn{
            float: left;
            border:1px solid #ffc900;
            width: 90px;
            height: 35px;
            background-color:#ffc900 ;
            text-align: center;
            line-height: 35px;
            margin-top: 15px;
        }
        .search-input{
            float: left;
            border:2px solid #ffc900;
            width: 400px;
            height: 35px;
            padding-left: 5px;
            margin-top: 15px;
        }
        .jx{
            border-bottom: 2px solid #ffc900;
            padding: 5px;
        }
        .company{
            height: 40px;
            background-color: #ffc900;
            text-align: center;
            line-height:40px ;
            font-size: 8px;
        }
    </style>
</head>
<body>
   <!-- 1.页眉部分-->
   <header class="container-fluid">
       <div class="row">
           <img src="img/top_banner.jpg" class="img-responsive">
       </div>
       <div class="row paddtop">
           <div class="col-md-3">
               <img src="img/logo.jpg" class="img-responsive">
           </div>
           <div class="col-md-5">
               <input class="search-input" placeholder="请输入线路名称">
               <a class="search-btn" href="#">搜索</a>
           </div>
           <div class="col-md-4">
               <img src="img/hotel_tel.png" class="img-responsive">
           </div>
       </div>
       <!--导航栏-->
       <div class="row">
           <nav class="navbar navbar-default">
               <div class="container-fluid">
                   <!-- Brand and toggle get grouped for better mobile display -->
                   <div class="navbar-header">
                       <!-- 定义汉堡按钮 -->
                       <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false">
                           <span class="sr-only">Toggle navigation</span>
                           <span class="icon-bar"></span>
                           <span class="icon-bar"></span>
                           <span class="icon-bar"></span>
                       </button>
                       <a class="navbar-brand" href="#">首页</a>
                   </div>
                   <!-- Collect the nav links, forms, and other content for toggling -->
                   <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
                       <ul class="nav navbar-nav">
                           <li class="active"><a href="#">Link <span class="sr-only">(current)</span></a></li>
                           <li><a href="#">Link</a></li>
                           <li><a href="#">Link</a></li>
                           <li><a href="#">Link</a></li>
                           <li><a href="#">Link</a></li>
                           <li><a href="#">Link</a></li>
                       </ul>
                   </div><!-- /.navbar-collapse -->
               </div><!-- /.container-fluid -->
           </nav>
       </div>
       <!--轮播图-->
       <div class="row">
           <div id="carousel-example-generic" class="carousel slide" data-ride="carousel">
               <!-- Indicators -->
               <ol class="carousel-indicators">
                   <li data-target="#carousel-example-generic" data-slide-to="0" class="active"></li>
                   <li data-target="#carousel-example-generic" data-slide-to="1"></li>
                   <li data-target="#carousel-example-generic" data-slide-to="2"></li>
               </ol>
               <!-- Wrapper for slides -->
               <div class="carousel-inner" role="listbox">
                   <div class="item active">
                       <img src="img/banner_1.jpg" alt="...">
                   </div>
                   <div class="item">
                       <img src="img/banner_2.jpg" alt="...">
                   </div>
                   <div class="item">
                       <img src="img/banner_3.jpg" alt="...">
                   </div>
               </div>
               <!-- Controls -->
               <a class="left carousel-control" href="#carousel-example-generic" role="button" data-slide="prev">
                   <span class="glyphicon glyphicon-chevron-left" aria-hidden="true"></span>
                   <span class="sr-only">Previous</span>
               </a>
               <a class="right carousel-control" href="#carousel-example-generic" role="button" data-slide="next">
                   <span class="glyphicon glyphicon-chevron-right" aria-hidden="true"></span>
                   <span class="sr-only">Next</span>
               </a>
           </div>
       </div>
   </header>
   <!-- 2.主体部分-->
   <div class="container">
        <div class="row jx">
            <img src="img/icon_5.jpg">
            <span>精选</span>
        </div>
       <div class="row paddtop">
           <div class="col-md-3">
                <div class="thumbnail">
                    <img src="img/jiangxuan_3.jpg" alt="">
                    <p>项目内容</p>
                    <font color="red">&yen; 699</font>
                </div>
           </div>
           <div class="col-md-3">
               <div class="thumbnail">
                   <img src="img/jiangxuan_3.jpg" alt="">
                   <p>项目内容</p>
                   <font color="red">&yen; 699</font>
               </div>
           </div>
           <div class="col-md-3">
               <div class="thumbnail">
                   <img src="img/jiangxuan_3.jpg" alt="">
                   <p>项目内容</p>
                   <font color="red">&yen; 699</font>
               </div>
           </div>
           <div class="col-md-3">

               <div class="thumbnail">
                   <img src="img/jiangxuan_3.jpg" alt="">
                   <p>项目内容</p>
                   <font color="red">&yen; 699</font>
               </div>
           </div>
       </div>
       <div class="row jx">
           <img src="img/icon_6.jpg">
           <span>国内游</span>
       </div>
       <div class="row paddtop">
           <div class="col-md-4">
               <img src="img/guonei_1.jpg">
           </div>
           <div class="col-md-8">
               <div class="row">
                   <div class="col-md-4">
                       <div class="thumbnail">
                           <img src="img/jiangxuan_3.jpg" alt="">
                           <p>项目内容</p>
                           <font color="red">&yen; 699</font>
                       </div>
                   </div>
                   <div class="col-md-4">
                       <div class="thumbnail">
                           <img src="img/jiangxuan_3.jpg" alt="">
                           <p>项目内容</p>
                           <font color="red">&yen; 699</font>
                       </div>

                   </div>
                   <div class="col-md-4">

                       <div class="thumbnail">
                           <img src="img/jiangxuan_3.jpg" alt="">
                           <p>项目内容</p>
                           <font color="red">&yen; 699</font>
                       </div>
                   </div>
               </div>
               <div class="row">
                   <div class="col-md-4">
                       <div class="thumbnail">
                           <img src="img/jiangxuan_3.jpg" alt="">
                           <p>项目内容</p>
                           <font color="red">&yen; 699</font>
                       </div>
                   </div>
                   <div class="col-md-4">
                       <div class="thumbnail">
                           <img src="img/jiangxuan_3.jpg" alt="">
                           <p>项目内容</p>
                           <font color="red">&yen; 699</font>
                       </div>
                   </div>
                   <div class="col-md-4">
                       <div class="thumbnail">
                           <img src="img/jiangxuan_3.jpg" alt="">
                           <p>项目内容</p>
                           <font color="red">&yen; 699</font>
                       </div>
                   </div>
               </div>
           </div>
       </div>
   </div>
   <!-- 3.页脚部分-->
   <footer class="container-fluid">
       <div class="row">
           <img src="img/footer_service.png" class="img-responsive">
       </div>
       <div class="row company">
           版权信息
       </div>
   </footer>
</body>
</html>
```



