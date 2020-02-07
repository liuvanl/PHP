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
