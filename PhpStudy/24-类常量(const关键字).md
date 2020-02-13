# 第二十四章类常量
## 24.1为什么要是使用类常量
> 当某些情况下，程序员可能有这样的需求，当不希望一个成员变量被修改，希望该变量的值是固定不变的。这时可以用const去修饰成员属性，这样这个属性就自动成为常量，比如所得率，圆周率等
## 24.2类常量的基本语法
```php
  class 类名 {
    const 常量名 = 初始值;
  }
```
### 说明
1. const是关键字，规定好的而不能修改
2. 常量名的规范是XXX_YYY，全部大写，然后使用下划线间隔
3. 类常量都是Public，但是我们不需要使用public去修饰
## 24.3快速入门案例
```php
  <?php
    header('content-type:text/html;charset=utf-8');
    // 计算公司所得税率
    class Clerk {
        const TAX_RATE = 0.08;
        public function getTax($salary){
            echo '<br>该交的税率是'.($salary*self::TAX_RATE).'元';
        }
    }

    $clerk = new Clerk();
    $clerk -> getTax(30000);
?>
```
## 24.4关于类常量的使用细节说明
1. 常量名一般字母全部大写，TAX_RATE中间可以有下划线
2. 在定义常量的同时，必须赋初值，比如 const TAX_RATE = 0.08
3. const关键字前不能用publi/protected/private修饰，默认是public
4. 访问常量：在类的内部访问---类名::常量名/self::常量名/接口::常量名;在类的外部访问---类名::常量名/接口名::常量名
5. 常量的值在定义的时候就初始化，以后就不能修改
6. 常量可以被子类继承
7. 一个常量是属于一个类的，而不是某个对象的
8. 常量可以是基本数据类型(int,float,bool,string)，还可以是array，不能是对象
9. 类常量可以在类中，类的外部和其它普通函数中使用
