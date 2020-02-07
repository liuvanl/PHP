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
```php
  <?php
     // Movie(名称，导演，成本(protected)，票房[ticket_office](private))
    class Movie {
        public $name;
        public $director;
        protected $cost;
        private $ticketOffice;
        // 构造函数
        public function __construct($name,$director,$cost){
            $this -> name = $name;
            $this -> director = $director;
            $this -> cost = $cost;
        }
        // 显示电影信息
        public function showAllInfo(){
            echo '<br>电影信息如下：';
            echo '<br>name='.$this -> name;
            echo '<br>director='.$this -> director;
            echo '<br>cost='.$this -> cost;
            echo '<br>ticketOffice='.$this -> ticketOffice;
        }
        public function updateInfo($director,$cost,$ticketOffice){
            // setXxx来完成
            $this -> setDirector($director);
            $this -> setCost($cost);
            $this -> setTicketOffice($ticketOffice);
        }

        // 
        public function setDirector($director){
            $this -> director = $director;
        }

        public function getDirector(){
            return $this -> director;
        }

        public function setCost($cost){
            if(is_numeric($cost) && $cost > 0.0){
                $this -> cost = $cost;
            } else {
                echo '<br>输入的成本格式有问题';
            }
        }

        public function getCost($cost){
            return $this -> cost;
        }

        public function setTicketOffice($ticketOffice){
            if(is_numeric($ticketOffice) && $ticketOffice > 0.0){
                $this -> ticketOffice = $ticketOffice;
            }
        }

        public function getTicketOffice(){
            return $this -> ticketOffice;
        }
    }

    $m1 = new Movie('囧妈','徐峥',500000);
    $m1 -> showAllInfo();

    echo '<br>--------------------<br>';

    $m1 -> updateInfo('流凡',500,1000000);
    $m1 -> showAllInfo();
  ?>
```
## 18.6在开发中，如何选择操作方式
1. 如果我们希望直接通过 $对象名->属性名的方式来操作属性，则使用__set 和 __get
2. 如果我们希望对各个属性分别进行验证，则使用setXxx 和 getXxx
## 18.7封装的细节说明
1. 普通属性要定义为公有，受保护，私有之一。如果用 var 定义，则被视为公有。静态属性可以不指定访问修饰符，默认是public
2. 类中的方法可以被定义为公有，私有或受保护。如果没有设置这些关键字，则该方法默认为公有(不管这个方法是静态的，还是非静态都满足这个规则)
## 18.8对象运算符的连用现象
> 要求是 ： 通过一个学生对象，可以访问到该学生所在班级的信息
```php
  <?php
    // 学生类
    class Student {
        public $name;
        private $school;
        // 构造函数
        public function __construct($name,$school){
            $this -> name = $name;
            $this -> school = $school;            
        }

        public function setSchool($school){
            $this -> school = $school;
        }

        public function getSchool(){
            return $this -> school;
        }

    }

    // 学校类
    class School {
        public $name;
        public $address;
        private $my_class;
        // 构造函数
        public function __construct($name,$address,$my_class){
            $this -> name = $name;
            $this -> address = $address;
            $this -> my_class = $my_class;
        }       

        public function setMyClass($my_class){
            $this -> my_class = $my_class;
        }

        public function getMyClass(){
            return $this -> my_class;
        }
    }

    // 班级类
    class MyClass {
        protected $name;
        protected $stu_num;
        private $introduce;

        public function __construct($name,$stu_num,$introduce){
            $this -> name = $name;
            $this -> stu_num = $stu_num;
            $this -> introduce = $introduce;
        }

        public function getIntroduce(){
            return $this -> introduce;
        }
        public function setIntroduce($introduce){
            $this -> introduce = $introduce;
        }
    }

    // 创建班级对象
    $myClass = new MyClass('计算机编程07期',80,'07期是个牛逼的班级');
    var_dump($myClass);

    // 创建学校对象
    $school = new School('清华','北京',$myClass);
    var_dump($school);

    // 创建学生对象
    $student = new Student('刘凡',$school);
    var_dump($student);

    //通过一个学生对象，可以访问到该学生所在班级的信息.

    var_dump($student -> getSchool() -> getMyClass() -> getIntroduce() );
  ?>
```
