# 第十九章oop编程的三大特征--继承
## 首先看一个需求
> 开发一个学生考试系统，学生可以考试并且测评分数，并显示考了多少分（小学生，大学生）
```php
  <?php
    // 小学生
    class Pupil {
        public $name;
        public $age;
        private $grade;
        public function __construct($name,$age){
            $this -> name = $name;
            $this -> age = $age;
        }
        // 考试函数
        public function testing(){
            echo '<br>小学生正在考试……';
        }
        // 评分
        public function setGrade($grade){
            if(is_numeric($grade) && $grade > 0.0){
                $this -> grade = $grade;
            } else {
                echo '<br>您输入的评分有误';
            }
        }
        // 显示分数
        public function showGrade(){
            echo '<br>'.$this -> name .'成绩是'.$this -> grade;
        }
    }
    
    $pupil = new Pupil('小花',8);
    $pupil -> testing();
    $pupil -> setGrade(80);
    $pupil -> showGrade();

    // 大学生
    class Graduate {
        public $name;
        public $age;
        private $grade;
        public function __construct($name,$age){
            $this -> name = $name;
            $this -> age = $age;
        }
        // 考试函数
        public function testing(){
            echo '<br>大学生正在考试……';
        }
        // 评分
        public function setGrade($grade){
            if(is_numeric($grade) && $grade > 0.0){
                $this -> grade = $grade;
            } else {
                echo '<br>您输入的评分有误';
            }
        }
        // 显示分数
        public function showGrade(){
            echo '<br>'.$this -> name .'成绩是'.$this -> grade;
        }
    }

    $graduate = new Graduate('小樊',18);
    $graduate -> testing();
    $graduate -> setGrade(90);
    $graduate -> showGrade();
  ?>
```
## 分析问题
1. Pupil类和Graduate类，相似度很高，很多相同的属性及方法
2. 这时已经出现了代码的冗余，这样不利于类的管理和维护
3. 解决的方法就是继承
## 使用继承的方式对上面的代码进行改写
```php
  <?php
     // 公共类
    class Student {
        public $name;
        public $age;
        private $grade;
        public function __construct($name,$age){
            $this -> name = $name;
            $this -> age = $age;
        }
        public function setGrade($grade){
            if(is_numeric($grade) && $grade > 0.0){
                $this -> grade = $grade;
            }
        }
        public function showGrade(){
            echo '<br>'.$this->name.'成绩是'.$this->grade;
        }
    }

    // 小学生
    // 使用关键字extends继承
    class Pupil extends Student {
        public function testing(){
            echo '<br>小学生正在考试……';
        }
    }

    $pupil = new Pupil('小花',8);
    $pupil -> testing();
    $pupil -> setGrade(80);
    $pupil -> showGrade();

    // 大学生
    class Graduate extends Student {
        public function testing(){
            echo '<br>大学生正在考试……';
        }
    }

    $graduate = new Graduate('小樊',18);
    $graduate -> testing();
    $graduate -> setGrade(90);
    $graduate -> showGrade();
  ?>
```
## 继承概念说明
1. 继承的根本作用就是解决代码的复用性，减少冗余度，同时利用类的维护和扩展
2. 继承时的关键字是extends，这个是固定的，不能修改
## 继承的细节讨论
1. 子类最多能继承一个父类（指直接继承）
2. 子类可以继承其父类（或者超类）的public，protected修饰的变量（属性）和函数（方法）
3. 在创建某个子类对象时，默认情况下会自动调用其父类的构造函数
4. 如果在子类中需要访问其父类的方法（构造方法/成员方法，方法的访问修饰符是public/protected，可以使用父类::方法名或者parent::方法名来完成）
5. 如果子类（扩展类）中的方法和父类方法相同，我们称为方法重写
```php
  <?php
    // 子类最多只能继承一个父类
    class A {
        public $name = 'a';
    }
    class B extends A {
        public $age = 90;
    }
    class C extends A {
        public $salary = 900;
    }
    $c = new C();
    var_dump($c);



    class Person {
        public $name;
        public function __construct($name){
            $this -> name = $name;
            echo '<br>Person __construct()';
        }
        public function sayHello(){
            echo '<br>Person sayHello()';
        }
        protected function sayAbc(){
            echo '<br>Person sayAbc()';
        }
        private function sayHaha(){
            echo '<br>Person sayHaha()';
        }
    }

    class Student extends Person{
        public $money;
        public function test(){
            // 调用方式
            parent::sayHello();
            parent::sayAbc();
            parent::sayHaha();
        }
        //sutdent有一个构造函数，我希望在student的构造方法中调用父类的构造方法
        public function __construct($name,$money){
            $this -> money = $money;
            echo '<br>Student __construct()';
            parent::__construct($name);
        }
        public function showInfo(){
            echo '<br>信息='.$this -> name.'  '.$this -> money;
        }
    }

    $student1 = new Student('蜘蛛精',2000);
    $student1 -> showInfo();
  ?>
```
## 对象的比较
+ 基本的原则
> 当使用比较运算符（==）比较两个对象变量时，比较的原则是：如果两个对象的属性和属性值都相等，而且两个对象是同一个类的实例，那么这两个对象变量相等。  
> 而如果使用全等运算符（===），这两个对象变量一定要指向某个类的同一个实例（同一个对象）
```php
  <?php
    class Person {
        public $name;
        public function __construct($name){
            $this -> name = $name;
        }
    }
    class Dog {
        public $name;
        public function __construct($name){
            $this -> name = $name;
        }
    }

    $dog1 = new Dog('abc');
    $p1 = new Person('abc');
    $p2 = new Person('abc');

    if($p1 == $p2){
        echo '<br>$p1 == $p2';          //true
    }

    if($p1 == $dog1){
        echo '<br>$p1 == $dog1]';       // false
    }

    if($p1 === $p2){
        echo '<br>$p1 === $p2';         // false
    }
    $p3 = &$p1;
    if($p1 === $p3){
        echo '<br>$p1 === $p3';         // true
    }
  ?>
```
