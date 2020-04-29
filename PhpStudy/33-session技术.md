# 第三十三章 session技术
## 33.1 session的基本介绍
> session是服务器端技术，session保存的数据存储到服务器端，由于cookie每次都携带数据到服务器，这样的话，效率低、安全性较低
### session文件保存的地址：
> session文件的存储路径取决于PHP的配置文件：php.ini
```
  session.save_path = "路径";
```
+ 在php中使用session
```php
  session_start();
```
> 就会在服务器端创建一个session文件
## 33.2 session文件可以用来干什么
+ 应用场景
> 多个服务器文件之间共享数据
+ 购物车
> goods_list.php  点击商品购买（存储到session文件中去）  
> show_cart.php 查询购买的商品（从session文件读取）
+ 防跳墙操作---防止未经登录，直接进入到服务器首页
> check.class.php  登录验证操作(登录成功之后，在session文件中写入一个数据)  
> check.php     验证验证码的表单（从session文件存储的数据）
## 33.3 session的增删改查操作
+ session创建的基本使用
```php
  <?php
    session_start();
    $_SESSION['name'] = array('zhangsan','lisi');
  ?>
```
+ session的读取基本使用
```php
  <?php
    session_start();
    var_dump($_SESSION['name']);
  ?>
```
+ session的修改操作
> 修改session也是通过$_SESSION数组进行操作，如果数组的下标已经存在，则表示修改的意思，并且会把原来的值覆盖掉
```php
  session_start();
  $_SESSION['name'] = 'wangwu';
```
+ session的删除
1. 删除单个session
```php
  session_start();
  unset($_SESSION['name']);
```
2. 将所有的session全部删除
```php
  session_start();
  foreach($_SESSION as $k=>$v){
    unset($_SESSION[$k]);
  }
```
3. 将session文件删除和数据
```php
  session_start();
  session_destroy();    // 删除保存的session文件
```
## 33.4 session应用案例1
+ 完成一个session版的购物车
> 搭建商品列表页面：goods_list.html
```html
  <!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<style>
	li {
		list-style: none;
		border: 1px solid #666;
		width: 300px;
		height: 100px;
		text-align: center;
		margin-top: -1px;
		line-height: 100px;
	}
	
</style>
</head>
<body>
	<ul>
		<li>
			<span>追风筝的人</span>
			<a href="./buy.php?name=追风筝的人">点击购买</a>
		</li>
		<li>
			<span>白说</span>
			<a href="./buy.php?name=白说">点击购买</a>
		</li>
		<li>
			<span>小炸弹联盟</span>
			<a href="./buy.php?name=小炸弹联盟">点击购买</a>
		</li>
		<li>
			<span>时间简史</span>
			<a href="./buy.php?name=时间简史">点击购买</a>
		</li>
	</ul>	
</body>
</html>
```
> 创建buy.php页面进行计算：如果商品已经购买了，提示该商品的数量，如果没有购买该商品的数量就是1
```php
<?php
    header('content-type:text/html;charset=utf-8');
    // 1.接收数据
    $book = isset($_GET['name']) ? $_GET['name'] : '';
    // 将商品信息存储到session文件中
    session_start();    
    if(isset($_SESSION['cart'][$book])){
        // 说明已经购买了，数量加1
        $_SESSION['cart'][$book]++;    
    }else {
        // 说明是第一次购买，数量就等于1
        $_SESSION['cart'][$book] = 1;        
    }
    echo '购买成功';
```
> 购物车
```php
  <?php
    header('content-type:text/html;charset=utf-8');
    // 读取session文件中的信息
    session_start();    
    foreach ($_SESSION['cart'] as $k=>$v){
       echo '您购买了的书是：'.$k.'数量是：'.$v."<a href='./del.php?name=$k'>点击删除</a>".'<br>';
    }
```
## 33.5 session应用实例2-防跳墙
+ 分析
> 防跳墙就是防止用户未经登录直接访问后台程序
+ 创建登录页面:login.html
```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<style>
	input[type="submit"],
	.form_item {
		margin: 20px 0;
	}
</style>
</head>
<body>
	<form action="./check.php" method="post">
		<div class="form_item">
			<span>用户名：</span>
			<input type="text" name="user" autocomplete="off">
		</div>
		<div class="form_item">
			<span>密码：</span>
			<input type="password" name="pass" autocomplete="off">
		</div>
		<input type="submit" value="登录">
	</form>
</body>
</html>
```
+ 创建check.php
> 点击登录的时候，在check.php中进行用户名、密码的验证，姑且固定正确的用户名、密码分别是：zhangsan、admin123
```php
<?php
    header('content-type:text/html;charset=utf-8');
    // 用来接收表单提交数据的用户名和密码
    // 暂时指定账号和密码：zhangsan  admin123
    $user = isset($_POST['user'])?$_POST['user']:'';
    $pass = isset($_POST['pass'])?$_POST['pass']:'';
    
    if($user == 'zhangsan' && $pass = 'admin123'){
        echo '恭喜你，登录成功，3秒后自动进入首页';
        header("Refresh:3;url=./index.php");
        die;
    }else {
        echo '账号密码错误，请重新输入';
        header("Refresh:3;url=./login.html");
        die;
    }    
```
> 上面虽然做了验证，但是如果我不经过check.php，现在依然可以进入index.php如何实现，防止用户未经登录直接进入index.php  
+ 如何实现，防止用户未经登录直接进入index.php
1. 只有账号密码成功的时候，我们在session文件中存储一个标记
```php
	session_start();
	$_SESSION['user'] = $user;
```
2. 由于是session数据，可以在多个PHP文件中共享，所以将来只需要在index.php中判断session文件中是否有登录成功的标记即可
```php
    session_start();
    if(!isset($_SESSION['user'])){
        echo '严禁非法访问网站后台';
        header("Refresh:3;url=./login.html");
        die;
    }
    echo '欢迎来到网站后台';
```
## 33.6 应用案例3-用户登录时验证输入的验证码是否正确
+ 创建登录页面，并显示验证码：login.html
```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<form action="check.php" method="post">
		<label for="capt">请输入验证码</label>
		<input type="text" name="captcha" autocomplete="off">
		<img src="./Captcha.class.php">
		<div class="submit">
			<input type="submit" value="提交">
		</div>
	</form>
</body>
</html>
```
+ 分析思路
1. 生成验证码的时候，将其保存到session文件中在Captcha.class.php类中生成之后，就将其保存
2. 提交表单的时候，拿用户输入的验证码和session文件中的验证码进行比较（说明：比较的时候，通常不区分大小写的）
> Captcha.class.php
```php
	// 将验证码存储到session中
       session_start();
       $_SESSION['code'] = $code;
```
> check.php
```php
	session_start();		// 开启session
	if(strtolower($_POST['captcha']) != strtolower($_SESSION['code'])){
		echo '验证码错误，请重新输入';
		header('Refresh:3;url=./login.html');
		die;
	}
	echo '验证码正确';
```
## 33.7 session使用的注意事项
+ 如何开启session机制
1. 使用session_start();
2. 在php配置文件中自动开，但是不建议自动开启，哪里用就在哪里开启
```ini
	session.auto_start = 1;
```
+ 关于session的几个安全配置的地方
1. session.save_path  用来设置session文件保存的路径
2. session.cookie_secure 是否采用安全传输，仅在https协议下进行传输
3. session.cookie_httponly 是否自在http协议下进行传输，用来访问被其它语言劫持到
4. session.cookie_domain   cookie在哪个域名下是有效的
+ 通过ini_set函数动态修改ini的配置项
> 我们通常就会在PHP文件中，使用ini_set()设置PHP的配置项，但是通过该方法设置的配置，只能在当前php脚本文件生效
```php
	ini_set('session.save_path','D:/tmp');
	session_start();
```
## 33.8 session的存储机制
+ session_start()开启阶段
> 确定浏览器身上是否携带了session_id（session文件名称），如果携带了，就使用浏览器身上的sess_id，如果没有携带session_id，则创建session文件，并把session文件名称（作为session_id）回应给浏览器  
> 初始化$_SESSION这个变量，先读取session文件里面的内容，再将内容反序列化之后，赋值给$_SESSION这个变量  
> 在这个阶段还会判断哪些session文件已经过期了，然后再触发垃圾回收机制  
+ 脚本运行周期内session_start()之后
> php只是对$_SESSION这个变量进行增删改查的操作，需要注意：这个阶段并没有影响到session文件里面的内容，除非你在这个阶段session_destroy()了，除此之外，该阶段不会对session文件有任何影响  
+ 脚本执行结束阶段
> 在这个阶段才会对session文件进行操作，也就是这个阶段才会把$_SESSION数组中的数据序列化然后存储到session文件  
## 33.9 经典的session面试题
+ 请问下面的运行结果是什么？
```php
	// 脚本运行周期，只是对$_session这个数组进行操作
	// 这些数据都是在内存中运行的
	$_SESSION['name'] = 'lisi';
	$_SESSION['age'] = 30;
	
	// 遇到session_destroy()就会把文件删除
	session_destroy();
	var_dump($_SESSION);
```
+ 如何删除和当前session相关的所有数据
1. 内存中运行的session数据
2. 删除session文件中保存的数据
3. cookie中存储的session文件名称
```php
	session_start();
	// 清空内存中所有session数据
	$_SESSION = array();
	// 删除session文件
	session_destroy();
	// 删除cookie文件中保存的session文件名称
	setcookie(session_name(),'',time()-1);
```
## 33.10 cookie和session比较
1. 存储位置
> cookie存储在客户端（浏览器），每次请求是随身携带，在setcookie时创建的 session存储在服务器端，session_start的时候创建的
2. 安全性
> cookie每次随身携带，安全性不如session高
3. 生命周期
> Cookie的生命周期在setcookie时设置的  
> Session的生命周期在配置文件中的gc_maxlifetime设置，但是通过cookie里面携带的PHPSESSID找到的，这个cookie的有效期默认是0秒，也就是说一旦关闭了浏览器，cookie就失效了，下次访问会重新分配新的cookie  
> cookie的生命周期是累计的，session的生命周期是间隔的，以20分钟为例  
> cookie：1  2   3  4   .....  20分钟  
> session：1   2   3   4   ..... 19,如果从创建到第19分钟，重新访问了，会重新计算，也就是重新分配20分钟  
