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
> PDOStatement对象提供了3个方法查询数据：
1. fetch()，获取一条数据
2. fetchAll()，获取所有数据
3. fetchColumn()，获取一列数据（一个字段的数据）
```PHP
    // 连接数据库
    $dsn = "mysql:host=localhost;dbname=phpStudy;port=3306;charset=utf8";
    $user = "root";
    $pass = "";
    $pdo = new PDO($dsn,$user,$pass);
    
    $sql = "SELECT * FROM `user`";
    $pdo_statement = $pdo -> query($sql);
    
    /*
     * 通常常量约束返回的数据的格式
     * PDO::FETCH_ASSOC  表示关联类型的数据
     * PDO::FETCH_NUM   表示索引数据
     * PDO::FETCH_BOTH  表示关联、索引数据
     */
    
    // 查询一条数据 fetch()    
    $row = $pdo_statement -> fetch(PDO::FETCH_ASSOC);    
    var_dump($row);
    
    // 查询全部数据
    //$row = $pdo_statement -> fetchAll();
    //var_dump($row);
    
    // 获取一个字段的值（一列的值）：fetchColumn(index)
    //$row = $pdo_statement -> fetchColumn(0);
    //var_dump($row);
```
## 34.4 pdo对象错误信息
### 说明
> 通过PDO对象的errorInfo、errorCode方法分别获得sql语句的错误信息、错误的代码号
### 方法
> errorInfo、errorCode
```php
    // 连接数据库
    $dsn = "mysql:host=localhost;dbname=phpStudy;port=3306;charset=utf8";
    $user = "root";
    $pass = "";
    $pdo = new PDO($dsn,$user,$pass);
    
    // sql语句
    /*
     * 这里的from故意写错的，让pdo抛出错误
     */
    $sql = "SELECT FRPM `user`";
    $pdo -> query($sql);
    
    // 获取错误信息
    $result = $pdo -> errorInfo();
    $code = $pdo -> errorCode();
    var_dump($result);
    var_dump($code);
```
## 34.5 引号转义并包裹（应用场景：防止sql注入）
### 说明
> 引号转义并包裹，先将字符串中的引号转义（不再具有原来的意义），然后在外面再包裹一层引号
```php
    $dsn = "mysql:host=localhost;dbname=phpStudy;port=3306;charset=utf8";
    $user = "root";
    $pass = "";    
    $pdo = new PDO($dsn,$user,$pass);    
    $str = "hello 'zhangsan'";
    // 对字符的内容进行转义并包裹
    $resulte = $pdo -> quote($str);    
    var_dump($resulte);
```
