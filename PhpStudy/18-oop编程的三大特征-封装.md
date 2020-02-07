# 第十八章oop编程的三大特征-封装
## 18.1基本介绍
> oop编程的三大特征是：封装性，继承性，多态性，在php面向对象编程中，多态提的并不多，因为php本身就是天生的多态
## 18.2抽象
> 在面向对象编程中，将一类事物的共有属性（成员属性）和行为（成员方法）提取出来，形成一个模板（类），这就是解决问题的方法就是抽象
### 18.2.1抽象的实际案例
> 创建一个银行系统类（Account），有各种银行的卡，不管是哪种银行都有共有属性：账号号码，密码，余额；共有行为有：存款，取款，查询，转账
```php
  <?php
    /*
        共有属性
        账号号码, 密码, 
        余额

        共有行为
        存款，取款，
        查询，转账
    */

    class Account{
        // 属性
        public $accountNo;
        private $pwd;
        private $balance;

        // 构造函数
        public function __construct($accountNo,$pwd = '888888',$balance=0.0){
            $this -> accountNo = $accountNo;
            $this -> pwd = $pwd;
            $this -> balance = $balance;
        }
        // 存款
        public function saveMoney($pwd,$amout){
            if($pwd == $this -> pwd){
                if($amout > 0.0){
                    // 存钱
                    $this -> balance += $amout;
                    echo '<br>存款成功';
                } else {
                    echo '您输入的金额有误，请重新输入';
                }              
            } else {
                echo '您输入的密码不正确';
            }
        }
        // 取款
        public function getMoney($pwd,$gmout){
            if($pwd != $this -> pwd){
                echo '您输入的密码不正确';
                return;
            }
            if($this -> balance < $gmout){
                echo '余额不足';
                return;
            }
            $this -> balance -= $gmout;
            echo '<br>取款成功';
        }
        // 查询
        public function selectMoney($pwd){
            if($pwd == $this -> pwd){
                echo '<br>账户'.$this -> accountNo.'余额为：'.$this ->balance;                 
            } else {
                echo '您输入的密码不正确';
            }
            
        }
    }

    $a1 = new Account('张三--50002371997',123456,5000);
    // 查询
    $a1 -> selectMoney(123456);

    // 存钱
    $a1 -> saveMoney(123456,5000);
    $a1 -> selectMoney(123456);

    // 取款
    $a1 -> getMoney(123456,500);
    $a1 -> selectMoney(123456);
  ?>
```
## 18.3封装的基本概念
> 封装就是把抽象出来的数据和对数据操作封装在一起，数据被保护在内部，程序的其它部分只有通过被授权的操作（成员方法），才能对数据进行操作。
## 18.4封装的具体实现-访问控制符
+ 说明：在php中，提供了三种访问控制符public,protected和private，访问控制符的访问是：public>protected>private
+ public---------------类内部、外部和子类都可以访问
+ protected------------表示受保护的，只有本类或子类可以访问
+ private--------------表示私有的，只有本类内部可以访问
## 18.5如何访问Protected和private属性的三种形式
> 使用魔术方法__get和__set来实现对protected和private属性的操作
