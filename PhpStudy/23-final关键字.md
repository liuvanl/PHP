# 第二十三章final关键字
## 23.1 基本介绍
> 当程序员不希望某个成员方法被子类重写时，我们可以将该方法修饰为final方法，当程序员不希望某个类被继承，我们可以将该类修饰为final类
## 23.2 基本语法
```php
  <?php
    final class 类名 {
      final 访问修饰符 function 方法名(){函数体}
    }
  ?>
```
## 23.3 入门案例
```php
  <?php
    // 如果我们不需要子类来继承superman，则使用final修饰该类即可
    final class Superman {
        public $name;
        public function __construct($name){
            $this -> name = $name;
        }
        // 不希望子类去重写attack方法
        final public function attack(){
            echo '<br>超人的攻击方法是枪';
        }
    }
    // 这里会报一个致命错误
    class Spiderman extends Superman {}
  ?>
```
## 23.4 final的细节
1. final不能够修饰成员属性
```php
  class A {
    // final不能修饰成员属性
    final public $name = 'hello'  // 这是错误的写法
  }
```
2. final 方法不能被重写，但可以被继承
```php
  class A {
    // 子类不能重写方法，但是可以继承
    final public function sayOk(){
      echo '<br>say ok';
    }
  }
  class B extends A {}
  $b = new b();
  $b -> sayOk();
```
3. 一般来说，final类中不会出现final方法，因为final类都不能被继承，也就不会被重写override final类的方法了
4. final类时可以被实例化的
