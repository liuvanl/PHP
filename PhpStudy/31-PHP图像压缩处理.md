# 第三十一章、PHP图像压缩处理
## 31.1 图像压缩处理介绍
> 我们之前学习过文件的上传（上传一个头像），但是上传的文件不能直接使用的，因为本地的文件可能会很大，在前端页面显示的时候，就会加载很长时间，所以我们通常会先压缩再使用  
> 通常压缩的时候，一般是按照等比例压缩，所谓的等比例压缩就是宽度、高度同时压缩相同的比例，在实际开发的时候，我们通常会根据具体的需要（前端页面的需求）在指定的范围内进行等比例压缩  
## 31.2 图像压缩入门案例
> 图像压缩非常简单，就是一个php的图像处理函数：imagecopyresampled
```php
<?php 
    // imagecopyresampled
    // 参数1----目标画布
    $dst_image = imagecreatetruecolor(100, 100);
    $color = imagecolorallocate($dst_image, 225,230, 246);
    imagefill($dst_image, 0, 0, $color);
    // 参数2----原图路径
    $src_image = imagecreatefrompng('bs.png');
    // 参数3 ---- 画布的x起始坐标
    // 参数4 ---- 画布的y起始坐标
    // 参数5 ---- 原图x起始坐标
    // 参数6 ---- 原图y起始坐标
    // 参数7 ---- 画布的宽度
    $dst_w = 100;   
    // 参数8 ---- 画布的高度
    $dst_h = 100;
    // 参数9 ---- 原图的宽度
    $src_w = imagesx($src_image);    
    // 参数10 --- 原图的高度    
    $src_h = imagesy($src_image);
    imagecopyresampled($dst_image, $src_image, 0, 0, 0, 0, $dst_w, $dst_h, $src_w, $src_h);    
    // 1. 输出到浏览器
    header('content-type:image/png');
    imagepng($dst_image); 
    
    // 2. 直接保存
    //imagepng($dst_image,'./thumb_bs.png');
?>
```
## 31.3 图像等比例压缩
+ 假设缩放10倍
```php
    // 参数7 ---- 画布的宽度
    $dst_w = (int)$src_w / 10;
    // 参数8 ---- 画布的高度
    $dst_h = (int)$src_h / 10;
```
> 但是上面的代码比例不是很灵活，我们要生成一个最优的压缩比例
```php
  if($src_w / 限定的宽度 >= $src_h / 限定的高度){
    $scale = $src_w / 限定的宽度;
  }else {
    $scale = $src_w / 限定的高度;
  }
```
```php
  <?php    
    makeThumb(150, 100);
    function makeThumb($area_w,$area_h){        
        
        $src_image = imagecreatefrompng('bs.png');
        
        $dst_x = 0;
        $dst_y = 0;
        $src_x = 0;
        $src_y = 0;
        
        $src_w = imagesx($src_image);
        $src_h = imagesy($src_image);
        
        // 计算缩放比例
        if($src_w / $area_w >= $src_h / $area_h){
            $scale = $src_w / $area_w;
        }else {
            $scale = $src_h / $area_h;
        }
        
        $dst_w = (int)$src_w / $scale;
        $dst_h = (int)$src_h / $scale;
        
        $dst_image = imagecreatetruecolor($dst_w, $dst_h);
        $color = imagecolorallocate($dst_image, 22, 139, 0);
        imagefill($dst_image, 0, 0, $color);
        
        imagecopyresampled($dst_image, $src_image, $dst_x, $dst_y, $src_x, $src_y, $dst_w, $dst_h, $src_w, $src_h);
        
        header('content-type:image/png');
        imagepng($dst_image);           
        
    }
?>
```
## 31.4 图像压缩处理类
> 因为主流的编程思想是：OOP面向对象编程，文件的最新单位是类  
> 思考：
+ 有哪些属性：	原图文件、压缩文件保存的地址
+ 有哪些成员方法：制作压缩图像的方法
## 完整代码
```php
<?php 
    class Thumb {
        // 成员熟悉
        private $file;  // 原图文件
        private $path;  // 保存文件的路径
        // 创建图像资源的函数
        private $create_func = array(
          'image/png' => 'imagecreatefrompng',
          'image/jpeg' => 'imagecreatefromjpeg',
          'image/gif' => 'imagecreatefromgif'
        );
        // 保存图像资源的函数
        private $save_func = array(
          'image/png' => 'imagepng',
          'image/jpeg' => 'imagejpeg',
          'image/gif' => 'imagegif'
        );
        // 图像的mime类型
        private $mime;
        
        // 构造函数用来初始化属性
        public function __construct($file){
            if(!file_exists($file)){
                echo '文件无效，请选择正确的文件';
                exit;
            }
            // 执行到这里说明文件有效
            $this -> file = $file;
            // getimagesize 用来获取文件的大小以及mime类型
            $this -> mime = getimagesize($file)['mime'];
        }
        
        public function __set($k,$v){
            if(property_exists($this, $k)){
                $this->$k = $v;
            }
        }
        
        public function __get($k){
            if(property_exists($this, $k)){
                return $this-> $k;
            }
        }        
        // 压缩图像的方法
        public function makeThumb($area_w,$area_h){
            $create_func = $this-> create_func;
            $src_image = $create_func[$this -> mime]($this -> file);
            $dst_x = 0;
            $dst_y = 0;
            $src_x = 0;
            $src_y = 0;
            
            $src_w = imagesx($src_image);
            $src_h = imagesy($src_image);
            
            // 缩放比例
            if($src_w / $area_w >= $src_h / $area_h){
                $scale = $src_w / $area_w;
            }else {
                $scale = $src_h / $area_h;
            }
            
            $dst_w = (int)$src_w / $scale;
            $dst_h = (int)$src_h / $scale;
            
            $dst_image = imagecreatetruecolor($dst_w, $dst_h);
            $color = imagecolorallocate($dst_image, 255, 255, 255);
            // 对png图片进行透明化处理
            $color = imagecolortransparent($dst_image,$color);
            imagefill($dst_image, 0, 0, $color);
            imagecopyresampled($dst_image, $src_image, $dst_x, $dst_y, $src_x, $src_y, $dst_w, $dst_h, $src_w, $src_h);
            
            // 通常我们会把压缩之后的图片保存到thumb子目录中，按照日期的格式进行保存
            $sub_path = date('Ymd').'/';
            $path = $this -> path;
            if(!is_dir($path.$sub_path)){
                @mkdir($path.$sub_path,0777,true);  // 创建目录
            }
            // 压缩的图像的文件名，在原基础上增加前缀：thumb_bs.png
            $origin_filename = basename($this -> file);
            $thumb_name = 'thum_'.$origin_filename;            
            // 保存文件
            $save_func = $this -> save_func;
            $save_path = $save_func[$this->mime]($dst_image,$path.$sub_path.$thumb_name); 
            // 把文件地址返回
            return $path.$sub_path.$thumb_name;
        } 
    }
    
    $thumb = new Thumb('bs.jpg');
    $thumb -> path = './thumb/';    
    $file_path = $thumb -> makeThumb(150,100);
    var_dump($file_path);
?>
```
