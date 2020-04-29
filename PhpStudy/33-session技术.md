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
