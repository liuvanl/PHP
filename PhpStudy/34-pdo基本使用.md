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
## 34.6 pdo对象-事务处理
### 说明
> 一组DML语句的集合，事务有4个特点，原子性、一致性、隔离性、持久性事务就是逻辑上的一组操作，这组操作的各个单元要么全部成功、那么全部失败，使用事务时候，数据表的存储引擎必须是INNODB类型的
### 使用事务的步骤
> 开启事务：begin   ->  如果各个单元全部成功：commit   -> 如果任何一个单元失败：rollback
### 案例
> 以转账为例子，有一方执行错误，就会回滚，两条sql语句都执行完成，就commit
```php
    // 实例化pdo对象
    $dsn = "mysql:host=localhost;dbname=phpStudy;port=3306;charset=utf8";
    $user = "root";
    $pass = "";
    $pdo = new PDO($dsn,$user,$pass);
    
    // 开启事务
    $pdo -> beginTransaction();
    
    // 准备sql语句
    $sql1 = "UPDATE `cash` SET money = money - 1000 WHERE name='宋江'";
    $sql2 = "UPDATE `cash` SET money = money + 1000 WHERE name = '李逵'";
    
    $res1 = $pdo -> exec($sql1);
    $res2 = $pdo -> exec($sql2);
    
    if(!$res1 || !$res2){
        // 执行回滚
        $pdo -> rollBack();
    }else {
        $pdo -> commit();
    }
```
## 34.7 pdo对象-预处理
### 说明
> 预处理，提前、预先处理sql，预处理执行的结果：先把sql语句的结构部分固定住，后期结构不会再变化，只能结果变化
### 实现步骤
1. 先使用占位符代替id的值
2. 再预处理，也就是就是固定sql语句的结构
3. 再绑定真实的数据（使用真实的数据替换占位符）
4. 执行sql
### 使用预处理的优势
1. 对数据库的操作更加安全，将外部用户传递的数据使用占位符代替，不影响sql语句的结构
2. 预处理会将sql语句的结构部分固定住，如果后期在执行类似的操作时，就直接使用上次编译好的（预处理）的语句，从而提升效率
```php
    $dsn = "mysql:host=localhost;dbname=phpStudy;port=3306;charset=utf8";
    $user = "root";
    $pass = "";
    $pdo = new PDO($dsn,$user,$pass);
    
    // 预处理
    // 1.先使用占位符代替值部分
    $sql = "DELETE FROM user WHERE id=?";
    // 2. 固定sql语句的结构，使用pdo对象的prepare方法固定，返回PDOstatement对象
    $pdo_statement = $pdo -> prepare($sql);
    // 3. 使用真实的值替换占位符
    $pdo_statement -> bindValue(1, '3 || 1=1');
    // 4. 执行
    $pdo_statement -> execute();
    echo '删除成功';
```
## 34.8 pdo对象-lastInsertId
### 说明
> 该方法用来获得上次执行插入操作时产生主键的值
### 案例演示
```php
    $dsn = "mysql:host=localhost;dbname=phpStudy;port=3306;charset=utf8";
    $user = "root";
    $pass = "";
    $pdo = new PDO($dsn,$user,$pass);
    
    // 插入一条数据
    $sql = "INSERT INTO user VALUES(null,'wangwu','admin123')";
    $pdo -> exec($sql);
    // 获取刚刚插入的这条记录主键的值
    $id = $pdo -> lastInsertId();
    var_dump($id);
```
## 34.9 PDOStatement对象
### 说明
> 获得PDOStatement对象的方法：query()、prepare()
### PDOStatement对象提供的方法
1. fetch（）查询1条记录
2. fetchAll()  查询所有记录
3. fetchColumn() 查询一个字段的值
4. bindValue()  绑定参数（替换占位符）
5. execute()    执行预编译的sql语句
6. closeCursor()  关闭游标、指针（查询数据之后，将游标初始化，便于下次查询）
7. errorCode()	获得预编译sql语句中的错误代码
8. errorInfo()	获得预编译的sql语句中的错误信息
## 34.40 封装pdo类
```php
    class Self_PDO {
        // 私有属性
        private static $instance;   //dao类的单利对象
        private $pdo;   // pdo对象
        
        private function __construct(array $options){
            // 初始化属性
            $host = isset($options['host']) ? $options['host'] : '';
            $user = isset($options['user']) ? $options['user'] : '';
            $pass = isset($options['pass']) ? $options['pass'] : '';
            $port = isset($options['port']) ? $options['port'] : '';
            $dbname = isset($options['dbname']) ? $options['dbname'] : '';
            $charset = isset($options['charset']) ? $options['charset'] : '';            
            $dsn = "mysql:host=$host;dbname=$dbname;port=$port;charset=$charset";
            $this->pdo = new PDO($dsn,$user,$pass);
        }
        // 防止克隆
        private function __clone(){}
        // 单利模式实现的方法
        public static function getSingleton($options){
            if(!self::$instance instanceof self){
                self::$instance = new self($options);
            }
            return self::$instance;
        }
        
        // 查询一条数据
        public function fetchOne($sql){
            $pdo_statement = $this->pdo->query($sql);
            if($pdo_statement == false){
                // 说明sql语句有误
                $error = $this->pdo->errorInfo();
                $err_str = "SQL语句有误，详细信息如下：<br>".$error[2];
                echo $err_str;
                return false;
            }
            return $pdo_statement -> fetch(PDO::FETCH_ASSOC);
        }
        // 查询所有数据
        public function fetchAll($sql){
            $pdo_statement = $this->pdo->query($sql);
            if($pdo_statement == false){
                $error = $this->pdo->errorInfo();
                $err_str = "SQL语句有误，详细信息如下：<br>".$error[2];
                echo $err_str;
                return false;
            }
            return $pdo_statement -> fetchAll(PDO::FETCH_ASSOC);
        }
        // 查询一个字段的值
        public function fetchColumn($sql){
            $pdo_statement = $this->pdo->query($sql);
            if($pdo_statement == false){
                $error = $this->pdo->errorInfo();
                $err_str = "SQL语句有误，详细信息如下：<br>".$error[2];
                echo $err_str;
                return false;
            }
            return $pdo_statement -> fetchColumn();
        }
        // 执行增删改的操作，返回的是执行增删改受影响的记录数
        public function exec($sql){
            return $this->pdo->exec($sql);            
        }
        // 引导转义包裹的方法
        public function quote($data){
            return $this->pdo->quote($data);
        }
        // 查询刚刚插入的主键
        public function lastInsertId(){
            return $this->pdo->lastInsertId();
        }
    }
    
    $options = array(
        "host" => "localhost",
        "user" => "root",
        "pass" => "",
        "port" => 3306,
        "dbname" => "phpStudy",
        "charset" => "utf8"
    );
    
    $pdo1 = Self_PDO::getSingleton($options);
    $sql1 = "DELETE FROM user WHERE id = 6";
    $res = $pdo1 -> exec($sql1);
    var_dump($res);
```
