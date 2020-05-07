## 35.1 模板引擎的基本介绍
### 35.1.1 基本介绍
> 实际开发的时候，公司是明确的分工的，php程序员主要负责php代码（业务逻辑相关），web前端程序员主要负责（html、css、JavaScript）（让页面更加美观用户体验更好）
## 35.2 Smarty模板语法
### 35.2.1 smarty基本介绍
#### 介绍
> 目前模板引擎技术有很多，Laravel框架（blade模板引擎）、ThinkPHP框架（自己的模板引擎技术）、Smarty模板引擎技术，虽然有很多模板引擎技术，但是原理都是一样的，所以我们以smaty为例学习
#### 如何使用
1. 下载：http://www.smarty.net
2. 解压之后，libs目录就是smarty的核心代码，通常重命名为smarty再拷贝到我们的项目中
3. 使用的话，直接加载smarty里面的Smarty.class.php并实例化
4. 创建smarty.php
```php
    // 加载smarty的核心类文件
    require './libs/Smarty.class.php';
    $smarty = new Smarty();    
    // 真实的数据
    $name = "张三";    
    // 根据真实数据分配一下，并显示即可生成编译文件
    // 参数1：html模板中使用的变量的名字（占位符名字）
    // 参数2：真实数据
    // 就会生成一个编译文件，会使用真实的数据代替模板中的占位符
    $smarty -> assign('name',$name);
    $smarty -> display('./tpl/1.tpl.html');
```
5. 创建模板文件tpl.html
```html
  <!DOCTYPE html>
  <html>
  <head>
  <meta charset="UTF-8">
  <title>Insert title here</title>
  </head>
  <body>
    <h1>{$name}</h1>
  </body>
  </html>
```
6. 最终生成的编译文件会默认保存到templates_c这个目录（后期可以修改）
### 35.2.2 smarty模板中变量来源
#### 介绍
> Smarty中的模板中变量的来源，有如下几种：
1. smarty分配（assign）过来的真实数据
2. 配置文件数据---配置文件用来保存一些有规范的、可以在多个文件中公用的数据  例如：需要在html文件中显示图片：
+ img.conf
```conf
  img_url = img/
```
+ 其次，在html模板中想要使用配置文件，需要先加载文件 config.html
```html
  <!DOCTYPE html>
  <html>
  <head>
  <meta charset="UTF-8">
  <title>Insert title here</title>
  </head>
  <body>
    {config_load file="../config/img.conf"}
    <img src="{#img_url#}pic1.jpg">
    <img src="{#img_url#}pic2.jpg">
  </body>
  </html>
```
```php
    // 加载模板文件
    $smarty -> display('./tpl/2.config.html');
```
3. smarty保留的变量
> 这些变量是smarty保留的、内置的，不需要声明、加载，可以直接使用
+ {$smarty.now}，用来获得当前的时间戳
+ {$smarty.const.常量名}，用来获得PHP文件中定义的常量
+ {$smarty.config}，获得配置文件中的配置项
+ {$smarty.current_dir} ，当前文件所在的目录
```html
  <!DOCTYPE html>
  <html>
  <head>
  <meta charset="UTF-8">
  <title>Insert title here</title>
  </head>
  <body>
    <p>当前时间戳是：{$smarty.now}</p>
    <p>获得常量：{$smarty.const.ROOT}</p>
    {config_load file="config/img.conf"}
    <p>获得配置项：{$smarty.config.img_url}</p>
    <p>当前文件所在目录：{$smarty.current_dir}</p>
  </body>
  </html>
```
```php
    require './libs/Smarty.class.php';
    $smarty = new Smarty();    
    // 定义常量
    define('ROOT','http://www.liuvanl.com');    
    $smarty -> display('./tpl/3.smarty_inner.html');
```
4. php请求变量
> 在html模板中，还可以获得PHP提供的http请求是携带的变量、PHP的环境变量等
```php
    header('content-type:text/html;charset=utf8');
    require './libs/Smarty.class.php';
    $smarty = new Smarty();    
    // cookie数据
    setcookie('is_login','on',time()+60);
    // session数据
    session_start();
    $_SESSION['cart'] = '白说';    
    $smarty -> display('./tpl/4.smarty_http.html');
```
```html
  <!DOCTYPE html>
  <html>
  <head>
  <meta charset="UTF-8">
  <title>Insert title here</title>
  </head>
  <body>
    <p>接收URL地址栏中的参数：{$smarty.get.id}</p>
    <p>接收cookie数据：{$smarty.cookies.is_login}</p>
    <p>接收session数据：{$smarty.session.cart}</p>
    <p>接收$_SERVER环境变量：{$smarty.server.DOCUMENT_ROOT}</p>
  </body>
  </html>
```
### 35.2.3 smarty模板中变量调节器
#### 介绍
> 变量调节器，就是对模板中的变量进行调节、修饰作用,例如：模板中可以使用{$smarty.now}获得当前的时间戳，用户看不懂时间戳的，所以我们需要对变量进行修饰，将其转换成日期格式就好了
#### 如何使用
> 语法：{模板变量|修饰器:传递的参数}
#### 变量调节器
```php
    date_default_timezone_set('PRC');
    require './libs/Smarty.class.php';
    $smarty = new Smarty();
    
    $str = "hello world";
    
    // 传递一个js代码过去
    $script = <<<JS
        <script>
            for(var i = 0 ; i < 5;i++){
                alert(i);
            }
        </script>
JS;
    
    $book = '想和你在一起';
    
    $smarty -> assign('str',$str);    
    $smarty -> assign('script',$script);
    $smarty -> assign('book',$book);
    $smarty -> display('./tpl/5.smarty_modifier.html');
```
```html
  <!DOCTYPE html>
  <html>
  <head>
  <meta charset="UTF-8">
  <title>Insert title here</title>
  </head>
  <body>
    <p>当前时间是：{$smarty.now|date_format:'%Y-%m-%d %H:%M:%S'}</p>
    <p>首字母大写：{$str|capitalize}</p>
    <p>字符串整体大写：{$str|upper}</p>
    <p>统计字符个数：{$str|count_characters}</p>
    <p>字符缩进：{$str|indent:2:'&nbsp;'}</p>
    <p>进行实体转码：{$script|escape}</p>
    <p>字符串截取：{$book|truncate:5}</p>
    <a href="index.php?book_name={$book|escape:'url'}">URL转码</a>
  </body>
  </html>
```
### 35.2.4 smarty模板中流程控制语句
#### 介绍
> 在html模板中，可以使用PHP的流程控制语句
1. foreach
```php
    require './libs/Smarty.class.php';
    $smarty = new Smarty();
    
    $list = array(
        array('good_id'=>1,'good_name'=>'Nokia N97'),
        array('good_id'=>2,'good_name'=>'Huawei P40'),
        array('good_id'=>3,'good_name'=>'Xiaomi 10')
    );
    
    $smarty -> assign('list',$list);
    $smarty -> display('./tpl/6.smarty.html');
```
```html
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
    </head>
    <body>
        <table border=1 width="500" align="center">
            <tr>
                <th>id</th>
                <th>name</th>
            </tr>
            {foreach $list as $k=>$v}
            <tr>
                <td>{$v['good_id']}</td>
                <td>{$v['good_name']}</td>
            </tr>
            {/foreach}
        </table>
    </body>
    </html>
```
2. for
```html
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
    </head>
    <body>
        {for $i=0 to 6}
            <p>{$i}</p>
        {/for}

        {for $i=1 to 10 step=2}
            <p>hello world</p>
        {/for}
    </body>
    </html>
```
3. if elseif else
```html
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
    </head>
    <body>
        {if $money > 5000}
            <p>买个手机吧</p>
        {else if $money > 3000}
            <p>买个平板吧</p>
        {/if}
    </body>
    </html>
```
4. include
```html
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
    </head>
    <body>
        {include file="tpl/common/header.html"}
        <h2>这是主体</h2>
        {include file="tpl/common/footer.html"}
    </body>
    </html>
```
5. extends
6. block
> 模板继承实现的功能和include非常类似，只是模板继承比include更加强大include只是用来加载外部模板文件的，不能修改被加载的文件的代码，模板继承可以实现
+ 将公共的代码提取到公共的文件（DOCTYPE、head、css等），然后在公共的模板里面指定哪些地方可以被重写，通过{block}标签指定哪些地方可以被重写
```html
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
    </head>
    <body>
        {block name="amao"}1这是公共模板的内容{/block}
    </body>
    </html>
```
```html
    {extends file="tpl/common/layout.html"}
    {block name="amao" append}这是我们修改的内容{/block}
    {block name="amao" prepend}这是我们修改的内容{/block}
```
+ 我们的模板要继承该公共的模板（类似于include）如果想覆盖、重写公共的模板里面的内容的话，也是通过{block}重写注意：我们的模板继承公共模板之后，公共模板的内容会覆盖我们的内容
+ block标签还提供了2个参数：append表示追加在原来内容的后面，prepend追加到原来内容的前面
7. literal(新版本已经不需要这个了)
> 由于smarty的默认的占位符（标签）就是{}，一看到花括号就去解析了，由于这里不需要smarty解析，所以我们就可以使用{literal}标签包起来
```html
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
    </head>
    {literal}
    <style>
        h1 {
            font-size: 40px;
            color: red;
        }
    </style>
    {/literal}
    <body>
        {block name="title"}<h1>This is h1 title</h1>{/block}
    </body>
    </html>
```
## 35.2 smarty缓存
> 每次都要去数据库查询，所以这个效率是很低的,只要有一个人将商品的信息查询出来，就保存到服务器的一个文件中，其他用户的访问，直接让其访问该缓存的文件即可
### 35.2.1 smarty实现缓存
```php
    require 'pdo.class.php';
    $options = array(
        "host" => "localhost",
        "user" => "root",
        "pass" => "",
        "port" => 3306,
        "dbname" => "phpStudy",
        "charset" => "utf8"
    );    
    $pdo1 = Self_PDO::getSingleton($options);
    $sql1 = "SELECT * FROM `ecs_goods` order by goods_id";
    
    $good_list = $pdo1 -> fetchAll($sql1);
    // 加载smarty核心文件
    require 'libs/Smarty.class.php';
    $smarty = new Smarty();
    
    // 1. 开启缓存
    $smarty -> caching = true;
    // 2. 设置缓存文件保存的地址（通常会存到cache目录里面）
    $smarty -> setCacheDir('cache');
    // 3. 设置缓存的周期
    $smarty -> cache_lifetime = 60;
    
    // 传递数据
    $smarty -> assign('good_list',$good_list);
    $smarty -> display('./tpl/goods.html');
```
```html
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
    {literal}
    <style>
        .good_box {
            width: 1000px;
            margin: 100px auto;
        }
        .good_title {
            display: flex;
        }
        .good_title span {
            flex: 1;
            padding: 10px 0;
            font-weight: bold;
            text-align: center;
            border: 1px solid #ccc;		
        }
        .good_title span:nth-child(2) {
            border-right: 0;
            border-left: 0;
        }
        .good_items {
            display: flex;
            border: 1px solid #ccc;
            border-top: 0;
            border-right: 0;
        }
        .good_items span {
            flex: 1;
            padding: 10px 0;
            color: #666;
            text-align: center;
            border-right: 1px solid #ccc;
        }
    </style>
    {/literal}
    </head>
    <body>
        <div class="good_box">
            <div class="good_title">
                <span>序号</span>
                <span>商品名称</span>
                <span>商品价格</span>
            </div>
            <div class="good_list">
                {foreach $good_list as $v}
                <div class="good_items">
                    <span>{$v['goods_id']}</span>
                    <span>{$v['goods_name']}</span>
                    <span>{$v['shop_price']}</span>
                </div>
                {/foreach}
            </div>
        </div>
    </body>
    </html>
```
