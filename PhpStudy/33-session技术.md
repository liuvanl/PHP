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
