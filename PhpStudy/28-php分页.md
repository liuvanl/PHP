# 第二十八章、php分页
## 28.1 分页基本介绍
> 所有的内容都显示在一页上，用户体验不好，内容太多看的眼花缭乱，程序一次性读取所有的内容，效率比较低，所以我们要采用分页显示，也就是将上面的内容分为多个页面来显示，每页显示几条内容
## 28.2 具体实现
1. 先创建一个分页导航条，可以点击
```php
<!DOCTYPE html>
<html lang="zh-CN">
<head>
	<meta charset="utf-8">
	<title>document</title>
	<style type="text/css">
        * {marign:0;padding:0}
        ul,ol,li {
	       list-style: none;
        }
        .paging li {
	       display: inline-block;
           width: 40px;
        	height: 40px;
        	background: #eee;
        	border: 1px solid #333;
        	text-align: center;
        	line-height: 40px;
        	margin-left: -3px; 
        	font-size: 14px;
        }
        .paging li a {
	       display: block;
        }
        .paging li.active {
	       background: blue;
        }
        .paging li.active a {
	       color: #fff;
        }
    </style>
</head>
<body>
<ul>
  <li><a href="">首页</a></li>
  <li><a href="">1</a></li>
  <li><a href="">2</a></li>
  <li><a href="">3</a></li>
  <li><a href="">4</a></li>
  <li><a href="">尾页</a></li>
</ul>
</body>
</html>
```
2. 动态创建分页导航条，直接采用面向对象思想实现
+ 成员属性：保存数据的，保存将来可能会变化的数据
+ 创建多少页，将来会变化，所以我们将其保存到属性上
+ 总的页数 == 查询的数据库的记录数 / 每页显示的条数
```php
<?php
    header('content-type:text/html;charset=utf-8');
    class Page {
        private $page_count = 100;
        private $page_size = 5;
        private $now_page = 5;
        private $url = '';
        
        
        public function __set($pro_name,$pro_val){
            if(property_exists($this, $pro_name)){
                $this -> $pro_name = $pro_val;
            }
        }
        
        public function __get($pro_name){
            if(property_exists($this, $pro_name)){
                return $this -> $pro_name;
            }
        }
        
        public function create(){
            // 创建首页
            $page_str = <<<HTML
                <ul class="pagin">
                    <li><a href="">首页</a></li>
HTML;
            // 创建1、2、3……页
            // 计算出多少页
            $page_count = ceil($this -> page_count / $this-> page_size);
            for($i=2;$i<$page_count;$i++){
                $page_str .= <<<HTML
                    <li><a href="">$i</a></li>
HTML;
            }
            // 创建尾页
            $page_str .= <<<HTML
                <li><a href="">尾页</a></li>
            <ul>
HTML;
        return $page_str;
        }
    }
    // 演示
    $page = new Page();
    $page_str = $page -> create();
    echo $page_str;
?>    
```
3. 分页导航只显示当前页的前3页、后3页 例如：当前页是第5页，显示：2 3 4 5 6 7 8
+ 思路：
> 首先创建多少个li标签，取决于当前页，创建前3页：就是从当前页-3开始，后3页就是当前页+3结束  
> 如果遇到这样的情况：当前页是第1页的时候，前3页：0 -1 -2，如果当前页是17最后一页的时候，后面就是：18 19 20，显然没有上面的3页
```php
     if($i<2 || $i > $page_count-1){
         continue;
      }
```
4. 给当前页增加 active 这个类，让当前页高亮显示
+ 思路：
> 首页，只有当前页$this->now_page ==1 的时候才高亮显示  
> 尾页，只有当前页是最后一页的时候，$this->now_page = $page_count才高亮显示
> 当我们点击哪一页的时候，该页才是当前页面，通过给每个超链接增加一个参数，表示是第几页
```php
// 创建首页
            $first = 1;
            $first_active = $this -> now_page == 1 ? 'active':'';
            $page_str = <<<HTML
                <ul class="pagin">
                    <li class="$first_active"><a href="">首页</a></li>
HTML;
            // 创建1、2、3……页
            // 计算出多少页
            $page_count = ceil($this -> page_count / $this-> page_size);            
            for($i=$this->now_page-3;$i<=$this -> now_page+3;$i++){
                $actve = $this -> now_page == $i ? 'active' : '';
                if($i<2 || $i > $page_count-1){
                    continue;
                }
                $page_str .= <<<HTML
                    <li class="$actve"><a href="">$i</a></li>
HTML;
            }
            // 创建尾页
            $last_active = $this -> now_page == $page_count ? 'active' : '';
            $page_str .= <<<HTML
                <li class="$last_active"><a href="">尾页</a></li>
            <ul>
HTML;
```
+ 完整Page.class.php代码
```php
<?php
    header('content-type:text/html;charset=utf-8');
    class Page {
        private $page_count = 100;
        private $page_size = 5;
        private $now_page = 5;
        private $url = '';                
        public function __set($pro_name,$pro_val){
            if(property_exists($this, $pro_name)){
                $this -> $pro_name = $pro_val;
            }
        }        
        public function __get($pro_name){
            if(property_exists($this, $pro_name)){
                return $this -> $pro_name;
            }
        }        
        public function create(){
            // 创建首页
            $first = 1;
            $first_active = $this -> now_page == 1 ? 'active':'';
            $page_str = <<<HTML
                <ul class="pagin">
                    <li class="$first_active"><a href="">首页</a></li>
HTML;
            // 创建1、2、3……页
            // 计算出多少页
            $page_count = ceil($this -> page_count / $this-> page_size);            
            for($i=$this->now_page-3;$i<=$this -> now_page+3;$i++){
                $actve = $this -> now_page == $i ? 'active' : '';
                if($i<2 || $i > $page_count-1){
                    continue;
                }
                $page_str .= <<<HTML
                    <li class="$actve"><a href="">$i</a></li>
HTML;
            }
            // 创建尾页
            $last_active = $this -> now_page == $page_count ? 'active' : '';
            $page_str .= <<<HTML
                <li class="$last_active"><a href="">尾页</a></li>
            <ul>
HTML;
        return $page_str;
        }
    }    
?>      
```
