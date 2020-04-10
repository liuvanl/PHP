# 第二十九章、php图像处理技术
## 29.1 图像处理基本介绍
> 所谓的PHP图像处理技术，就是通过php的函数进行绘制图像，然后可以输出到浏览器，也可以保存到本地
+ 该绘图技术，需要开启php的一个扩展：GD2，该扩展提供了很多绘制图像的方法
+ PHP的图像处理技术的应用场景：
> 验证码（在图像上面绘制一些文字，人类很容易辨别出来，但是计算机脚本以目前的技术辨别图像中的字符是有很大的难度）  
> 图像的压缩（；例如上传头像时，本地的图片很大，但是上传到服务器之后，会针对大图进行压缩处理）  
## 29.2 PHP绘图的基本步骤
1. 先开启php的gd扩展，该扩展里面提供了很多绘图函数
+ 打开php.ini配置文件 去掉该函数的分号
> extension=php_gd2.php
+ 重启apache服务器
+ 写一个phpinfo()测试一下
2. 开始绘制图像
+ 先在内存中，创建图像资源（理解成画布）：imagecreatetruecolor
+ 给画布分配颜色（默认画布的颜色是真空的黑色）:imagecolorallocate()
+ 给画笔分配颜色	：imagecolorallocate
+ 开始绘制			：imageline
+ 直接在浏览器输出、保存到本地：header()   imagepng()
+ 销毁画布资源		：imagedestroy()
## 29.3 php绘图的具体演示
1. 绘制线条 imageline()
```php
<?php
    header('content-type:text/html;charset=utf-8');
    // 1. 创建一个画布资源
    $image = imagecreatetruecolor(500,300);
    // 2. 给画布分配颜色
    $color = imagecolorallocate($image, 194, 194, 194);
    // 填充颜色
    imagefill($image, 0, 0, $color);
    
    // 绘制直线
    $red = imagecolorallocate($image, 255, 0, 0);
    imageline($image, 100, 100, 100, 200, $red);    
    
    // 将图像输出给浏览器
    header('content-type:image/png');
    imagepng($image);
    
    // 销毁内存中的图像资源
    imagedestroy($image); 
?>
```
2. 绘制三角形 imageline()
```php
<?php
    $image = imagecreatetruecolor(500, 300);    
    $color = imagecolorallocate($image, 194, 194, 194);    
    imagefill($image, 0, 0, $color);
    
    // 绘制三角形
    $red = imagecolorallocate($image, 255, 0, 0);
    imageline($image, 100, 100, 100, 200, $red);
    imageline($image, 100, 200, 200, 200, $red);
    imageline($image, 100, 100, 200, 200, $red);
    
    header('content-type:image/png');
    imagepng($image);    
    imagedestroy($image);
    
?>
```
3. 绘制矩形：imagerectangle()、imagefilledrectangle()、描边矩形：imagerectangle()、填充矩形：imagefilledrectangle()
```php
<?php
    $image = imagecreatetruecolor(500, 300);
    $color = imagecolorallocate($image, 194, 194, 194);
    imagefill($image, 0, 0, $color);
    
    $red = imagecolorallocate($image, 255, 0, 0);
    // 绘制矩形
    imagerectangle($image, 100, 100, 300, 200, $red);
    
    header('content-type:image/png');    
    imagepng($image);
    imagedestroy($image);
?>
```
```php
    imagefilledrectangle($image,100, 100, 300, 200, $red);    // 填充矩形
```
4. 绘制圆形 和 椭圆形：imageellipse() 、 imagefilledellipse()描边圆形：imageellipse填充圆形：imagefilledellipse
```php
  <?php
    $image = imagecreatetruecolor(500, 300);
    $color = imagecolorallocate($image, 194, 194, 194);
    imagefill($image, 0, 0, $color);
    
    // 绘制圆
    $red = imagecolorallocate($image, 255, 0, 0);
    //imageellipse($image, 250, 150, 100, 100, $red);
    imagefilledellipse($image, 250, 150, 100, 100, $red);   // 填充
    
    header('content-type:image/png');
    imagepng($image);
    imagedestroy($image);
?>
```
5. 将图片绘制到画布上：imagecopy()
```php
<?php
    $image = imagecreatetruecolor(547, 579);
    $color = imagecolorallocate($image, 194, 194, 194);
    imagefill($image, 0, 0, $color);
    
    // 绘制图像
    $src_img = imagecreatefrompng('./images/20200410120159.png');       // 图像地址
    $src_w = imagesx($src_img);         // 图像宽度
    $src_h = imagesy($src_img);         // 图像高度
    // 目标位置,图片源,x坐标,y坐标,原图采集x坐标,原图采集y坐标,原图宽度,原图高度
    imagecopy($image, $src_img, 0, 0, 0, 0, $src_w, $src_h);
    
    header('content-type:image/png');
    imagepng($image);
    imagedestroy($image); 
?>
```
6. 绘制字符串：imagestring()
```php
<?php
    $image = imagecreatetruecolor(500, 300);
    $color = imagecolorallocate($image, 194, 194, 194);
    imagefill($image, 0, 0, $color);    
    // 绘制文字
    $bai = imagecolorallocate($image, 255, 0, 0);
    /*
     * 参数1：绘制到哪个画布
     * 参数2：字体大小
     * 参数3 4：x坐标、y坐标
     * 参数5：绘制的文字
     * 参数6：颜色
     */
    imagestring($image,25, 200, 250, 'hello world', $bai);    
    header('content-type:image/png');
    imagepng($image);
    imagedestroy($image);
?>
```
7. 绘制文字：imagettftext()
```php
<?php
    $image = imagecreatetruecolor(500, 300);
    $color = imagecolorallocate($image, 194, 194, 194);
    imagefill($image, 0, 0, $color);
    
    // 绘制文字
    $red = imagecolorallocate($image, 255, 0, 0);
    /*
     * 参数1：绘制的源
     * 参数2：文字大小
     * 参数3：偏移角度
     * 参数4 5：x\y坐标
     * 参数6：填充颜色
     * 参数7：字体文件
     * 参数8：绘制的文字
     * 
     */
    imagettftext($image, 25, 30, 250, 260, $red, './STHUPO.TTF', 'hello world');    
    header('content-type:image/png');
    imagepng($image);
    imagedestroy($image);    
?>
```
8. 绘制弧形：imagearc()
```php
<?php
    $image = imagecreatetruecolor(500, 300);
    $color = imagecolorallocate($image, 194, 194, 194);
    imagefill($image, 0, 0, $color);
    // 绘制弧线
    $red = imagecolorallocate($image, 255, 0, 0);
    /*
     * 参数1:绘制的目标源
     * 参数2 3:xy坐标
     * 参数4 5: 弧线的宽高
     * 参数6 7: 弧线开始的角度，结束的角度
     * 参数8:颜色
     * 
     */
//     imagearc($image, 250, 150, 150, 150, 0, 180, $red);
    imagefilledarc($image, 250, 150, 150, 150, 0, 180, $red, IMG_ARC_PIE);  
    
    header('content-type:image/png');
    imagepng($image);
    imagedestroy($image);
?>
```
