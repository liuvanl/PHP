# 第三十章、php图像处理技术应用
## 30.1 验证码的应用
> 验证码（CAPTCHA）是“Completely Automated Public Turing test to tell Computers and Humans Apart”（全自动区分计算机和人类的图灵测试）的缩写，是一种区分用户是计算机还是人的公共全自动程序。可以防止：恶意破解密码、注册、刷票、论坛灌水，有效防止某个黑客对某一个特定注册用户用特定程序暴力破解方式进行不断的登陆尝试
## 30.2 封装验证码类
1. 封装一个验证码类，先实现能够随机生成不同颜色的背景
```php
<?php
    class Captcha {
        // 成员属性
        private $width = 100;       // 画布的宽度
        private $height = 30;       // 画布的高度
        private $number = 4;        // 验证码的个数
        private $font_file = 'STHUPO.TTF';        // 验证码的字体文件
        private $font_size = '25px';                // 验证码的字体大小
        
        public function __set($pro_name,$pro_value){
            if(property_exists($this,$pro_name)){
                $this -> $pro_name = $pro_value;
            }
        }
        
        public function __get($pro_name){
            if(property_exists($this, $pro_name)){
                if(property_exists($this, $pro_name)){
                    return $this -> $pro_name;
                }
            }
        }
        
        // 开始绘制验证码
        public function makeImg(){
            // 创建画布
            $image = imagecreatetruecolor($this -> width, $this -> height);
            // 分配颜色
            $color = imagecolorallocate($image, mt_rand(100,255), mt_rand(100,255), mt_rand(100,255));
            imagefill($image, 0, 0, $color);
            // 输出到浏览器
            header('content-type:image/png');
            imagepng($image);
            imagedestroy($image);
        }
    }

    $c = new Captcha();
    $c -> makeImg();
    
?>
```
2. 在生成的图片上面随机的产生文字（字母、数字）
> 由于随机生成字符的代码肯定会很多，所以我们将生成字符的代码封装到函数中
```php
        // 开始生成随机的字符
        public function makeCode(){
            //建立一个包含指定范围单元的数组 
            $upper = range('A','Z');
            $lower = range('a','z');
            $number = range(0,9);
            // 把3个数组合并为一个数组
            $code = array_merge($upper,$lower,$number);
            // 打乱数组顺序
            shuffle($code);
            // 根据属性中指定的字符个数，创建字符
            $str = '';
            for($i=0;$i<$this -> number ; $i++){
                $str .= $code[$i];
            }
            return $str;
        }
```
3. 把随机生成的字符写入到画布中
> 使用imagettftext()写入，因为可以指定字体文件 如何实现让每个字符所在的位置不一样，倾斜的角度也不一样？  
> 思路：遍历这4个字符，指定每一个字符的位置、倾斜的角度
```php
<?php
    class Captcha {
        // 成员属性
        private $width = 100;       // 画布的宽度
        private $height = 30;       // 画布的高度
        private $number = 4;        // 验证码的个数
        private $font_file = 'STHUPO.TTF';        // 验证码的字体文件
        private $font_size = '25';                // 验证码的字体大小
        
        public function __set($pro_name,$pro_value){
            if(property_exists($this,$pro_name)){
                $this -> $pro_name = $pro_value;
            }
        }
        
        public function __get($pro_name){
            if(property_exists($this, $pro_name)){
                if(property_exists($this, $pro_name)){
                    return $this -> $pro_name;
                }
            }
        }
        
        // 开始绘制验证码
        public function makeImg(){
            // 创建画布
            $image = imagecreatetruecolor($this -> width, $this -> height);
            // 分配颜色
            $color = imagecolorallocate($image, mt_rand(100,255), mt_rand(100,255), mt_rand(100,255));
            imagefill($image, 0, 0, $color);
            // 开始绘制文字
            $code = $this -> makeCode();
            for($i=0;$i<strlen($code);$i++){
                imagettftext($image, $this -> font_size, mt_rand(-30,30), ($this -> width / $this -> number)*$i+5, 22, mt_rand(0,100), $this ->font_file, $code[$i]);
            }            
            for($i=0;$i<100;$i++){
                // 绘制干扰像素点
                imagesetpixel($image, mt_rand(0,$this -> width), mt_rand(0,$this -> height), mt_rand(0,100));               
            }
            for ($i=0;$i<10;$i++){
                // 绘制干扰线
                imageline($image, 0, mt_rand(0,$this -> height), mt_rand(0,$this -> width), mt_rand(0,$this -> height), $color);
            }
            // 输出到浏览器
            header('content-type:image/png');
            imagepng($image);
            imagedestroy($image);
        }
        
        // 开始生成随机的字符
        public function makeCode(){
            //建立一个包含指定范围单元的数组 
            $upper = range('A','Z');
            $lower = range('a','z');
            $number = range(0,9);
            // 把3个数组合并为一个数组
            $code = array_merge($upper,$lower,$number);
            // 打乱数组顺序
            shuffle($code);
            // 根据属性中指定的字符个数，创建字符
            $str = '';
            for($i=0;$i<$this -> number ; $i++){
                $str .= $code[$i];
            }
            return $str;
        }
    }

    $c = new Captcha();
    $c -> makeImg();    
?>
```
