# 第三十四章 pdo基本使用
## 34.1 pdo的基本介绍
### 说明
> pdo为我们访问数据库提供了轻量级、一致性的接口，也就是说通过pdo不仅可以访问mysql数据库，还可以访问其他数据库：oracle、mysql等，便于数据的移植、迁移
### 执行效率
> 封装性的技术，是用来提升开发效率，将重复性的代码提取封装了，所以并没有对执行效率有多大的提升
## 34.2 开启pdo扩展
> 在php.ini配置文件中开启 extension=php_pdo_mysql.dll  
> 通过phpinfo()打印一下，如果显示pdo就表示开启成功  
> PDO扩展给我们提供了3个类，分别是：PDO、PDOStatement、PDOException
### pdo类连接数据库
```php
    // 参数1：数据源
    // 参数2：用户名
    // 参数3：密码
    // 连接数据库
    $dsn = "mysql:host=localhost;dbname=phpStudy;port=3306;charset=utf8";
    $user = 'root';
    $pass = '';
    $pdo = new PDO($dsn,$user,$pass);    
    var_dump($pdo);
```
## 34.3 pdo的基本使用-增删改查
### 说明
> 通过pdo连接、操作数据库，分为两种格式：执行增删改查的操作，通过pdo对象的exec()方法实现：通常返回受影响的记录数
+ 增加一条记录
> 创建一张测试表user
```sql
  create table `user`(
	id int primary key auto_increment,
	name varchar(32),
	password varchar(64)
)charset=utf8 engine=MyISAM;
```
```php
    $dsn = "mysql:localhost=localhost;dbname=phpStudy;port=3306;charset=utf8";
    $user = 'root';
    $passworld = '';
    // 连接数据库
    $pdo = new PDO($dsn,$user,$passworld);
    
    // 增加一条记录
    $sql = "INSERT INTO `user` VALUES(null,'admin','admin123')";
    $row = $pdo -> exec($sql);
    var_dump($row);
```
+ 修改一条数据
```php
    // 连接数据库
    $dsn = "mysql:host=localhost;dbname=phpStudy;port=3306;charset=utf8";
    $user = 'root';
    $pass = '';
    $pdo = new PDO($dsn,$user,$pass);
    
    // 修改一条记录
    $sql = "UPDATE `user` SET name = 'zhangsan' WHERE id = 1";
    $row = $pdo -> exec($sql);
    var_dump($row);
```
+ 删除记录
```php
    // 连接数据库
    $dsn = "mysql:host=localhost;dbname=phpStudy;port=3306;charset=utf8";
    $user = 'root';
    $pass = '';
    $pdo = new PDO($dsn,$user,$pass);
    
    // 删除一条记录
    $sql = "DELETE FROM `user` WHERE name = 'zhangsan'";
    $row = $pdo -> exec($sql);
    var_dump($row);
```
+ 执行查询操作
> 通过pdo对象的query方法实现，返回的是PDOStatement类的对象，该对象代表一个预处理的语句对象，将sql语句的结构部分缓存了
