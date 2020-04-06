# 第二十六章 php文件上传
## 26.1 文件上传的基本介绍
> 点击上传时，照片会上传到哪里去？会将图片、文件等上传到服务器上面，并返回图片的地址，这样，我们只需要给其他用户提供该文件的地址即可
## 26.2 文件上传原理说明
> 文件上传，其实也是通过表单提交的，表单提交的数据分为如下两种：
+ 字节流数据：输入框、单选框、复选框、多行文本域等都是通过字节的数据传输到服务器，服务器通过$_GET  $_POST接收
+ 二进制文件流的形式；当需要提交一些文件的时候，由于文件采用的二进制的形式进行编码，所以需要先将二进制文件转码，然后再提交到服务器提交的时候，需要通过input type=”file” 文件域上传服务器接收的时候，通过$_FILES进行接收注意：如何将表单的数据，以二进制流的形式进行传输，必须在表单form标签增加一个属性：enctype=”multipart/form-data”
### 先创建表单文件：upload.html
```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
	<meta charset="utf-8">
	<title>document</title>
</head>
<body>
	<form action="./01.upload.php" method="post" enctype="multipart/form-data">
		<div class="upload_img">
			<b>请上传头像</b>
			<input type="file" name="user_pic">
		</div>
		<div class="upload_target">
			<input type="submit" value="提交">
		</div>
	</form>
</body>
</html>
```
> 提交完成之后，在服务器端接收并打印：
```php
  var_dump($_FILES);
```
### MIME类型讲解
> 多用途internet邮件扩展，mime类型的出现，跟着电子邮件出现的  
> 早期，发送电子邮件的时候，里面可能会附带一些附件，计算机系统根据附件的类型找到对应的设备打开，例如：传输的是视频，就会找到视频播放器去打开，如果传输的是网页文件，那么会自动使用浏览器去打开  
> MIME类型，包括2个部分：第一个部分是该文件所属的一个大类，第二个部分是文件细节小类，例如：  
> text/html，属于文本文件，html这样的文本文件 image/jpg，属于图像大类，jpg这种类型的图像
### tmp_name详解
> tmp------ temp----temporary，该单词的意思是临时的
### 文件上传的原理
> 当我们点击提交表单时，会将file文件域里面的文件，先上传到临时的目录（可以在php的配置文件中设置），默认的临时目录是c:/windows/temp，如果需要持久的保存，需要通过php的函数move_uploaded_file()移动到指定的位置，如果不移动，当php脚本执行结束，临时文件就会消失  
> 为了不让该临时文件消失，我们需要在脚本结束之前，将该临时文件移动到服务器使用php的move_uploaded_file()函数进行移动的
## 26.3 文件上传入门案例
> 为了方便管理这些上传的文件，我们通常会将上传的文件保存到uploads目录下面
```php
<?php
	// 让其休眠5秒钟
	sleep(5);
	$destination = './uploads/uploads.flower.jpg';
	if(move_uploaded_file($_FILES['user_pic']['tmp_name'],$destination)){
		echo '上传成功';
	}else {
		echo '上传失败';
	}
?>
```
## 26.4 防止上传文件过大
> 思路：首先给定一个文件的最大限制，然后再拿上传的文件的大小 和 最大的限制进行比较，文件大小单位是字节，字节之间的转换:
+ 1024字节 = 1KB
+ 1024KB = 1MB
+ 1024MB = 1GB
```php
<?php
	// 限制文件上传的大小
	$max_size = 100*1024   // 100kb
	if($_FILES['user_pic']['size'] > $max_size){
		echo '你上传的文件过大';
		exit;
	}
?>
```
+ 确实阻止了文件的上传，但是还有一个小问题：
> 虽然上传时，拦截了，但是该文件还是在临时的文件夹中走了一圈，如何实现，如果文件过大的话，连临时目录都不让其进去  
> 需要修改php的配置文件了，将其最大的限制修改为2MB，这样，让我们上传的文件大小超过2MB，就无法进入到临时目录
```
	file_uploads = on;		// on表示允许上传
	upload_tmp_dir =    // 上传到临时目录
	upload_max_filesize = 2M	// 上传文件的最大限制
	max_file_uploads = 20		// 一次性上传的最大文件数量
```
## 26.5 防止文件被覆盖
> 如果多个用户，同时上传文件，如果文件的名字固定死的话，所有用户上传的文件名字都会一样，将来就很麻烦了  
> 思路：上传成功之后，给文件命名时，取一个唯	一的、随机数
```php
	$destination = './uploads/';
	$file_name = uniqid('lf_',true);	// 生成一个唯一ID  一个带前缀、唯一ID。 
	$ext = strrchr($_FILES['user_pic']['name'],'.');	//查找指定字符在字符串中的最后一次出现
	$new_filename = $file_name.$ext;
	$destination .= $new_filename;
```
## 26.6 分目录上传文件
+ 为什么要分目录存储？
> 如果上传的文件不加任何修改，直接保存到uploads目录的话，uploads目录就会出现很多文件，不便于查找 通常我们会按照日期的格式分目录保存
```php
	$destination = './uploads/';
	$file_name = uniqid('lf_',true);	// 生成一个唯一ID  一个带前缀、唯一ID。 
	$ext = strrchr($_FILES['user_pic']['name'],'.');	//查找指定字符在字符串中的最后一次出现
	$new_filename = $file_name.$ext;
	
	$sub_path = date('Y-m-d').'/';
	if(!is_dir($sub_path)){
		@mkdir($destination.$sub_path,0777,true);		// 创建新目录
	}
	$destination .= $sub_path.$new_filename;
```
## 26.7 控制上传的文件类型
+ 假设有这样一个需求：上传用户的头像
> 这个时候我们只能上传图片（jpg、png、gif），所以我们就应该上传的时候做一个判断  
> 思路：首先，定义一个数组保存支持的上传的文件的类型，然后再拿上传的文件的实际类型和支持的类型进行比较
```php
	$allow_type = array('image/jpeg','image/png','image/jpg','image/gif');
	$true_type = $_FILES['user_pic']['type'];
	if(!in_array($true_type,$allow_type)){
		echo '你上传的类型有误';
		exit;
	}
```
+ 如果手动的把一个excel文件的后缀修改为.jpg了，上传的时候，就会把php蒙混过去，如果用户上传的是脚本（js等），就会很危险所以我们还要做一个更加严格的过滤需要借助PHP提供的类（php 内置的一个类）finfo
```php
	// 实例化finfo对象
	$finfo = new finfo(FILEINFO_MIME_TYPE);
	$type = $finfo -> file($_FILES['user_pic']['tmp_name']);
	if(!in_array($type,$allow_type)){
		echo '不支持该类型的文件';
		exit;
	}
```
## 26.8 全部完整代码
```php
<?php 
    header('content-type:text/html;charset=utf-8');
    // 上传文件    
    // 休眠5秒钟
    sleep(5);    
    // 1.获取传过来的数据
    $_file = $_FILES;
    
    
    // 2.判断是否为空
    if($_file == ''){
        echo '上传文件不能为空！';
        exit;
    }
    // 定义存放文件的目录
    $save_file = './uploads/';
    
    
    // 3.限制文件上传的大小
    // 定义大小
    $max_size = 100*1024;        // 100kb
    // 判断文件是否过大
    if($_file['user_pic']['size'] > $max_size){
        echo '你上传的文件过大';
        exit;
    }
    
    
    // 4. 防止文件重复
    // 4.1 uniqid 
    // 参数1.文件名的前缀
    // 参数2. 布尔值,ture 更具有唯一性
    $file_name = uniqid('lf_',true);
    // 4.2 确定后缀
    // strrchr()用来获取一个字符串中最后出现的字符，返回该字符之后的部分
    $ext = strrchr($_file['user_pic']['name'], '.');
    // 4.3 拼接字符串
    $new_filename = $file_name.$ext;
    
    
    // 5. 分目录存储上传的文件
    // 按照日期创建子目录
    $sub_path = date('Y-m-d').'/';
    // 创建目录，判断该文件夹是否存在,如果不存在就创建该目录
    if(!is_dir($save_file.$sub_path)){
        @mkdir($save_file.$sub_path,0777,ture);
    }
    $save_file .= $sub_path.$new_filename;
    
    // 6. 上传的文件类型是否支持
    $allow_type = array('image/jpeg','image/png','images/gif');
    $true_type = $_file['user_pic']['type'];
    if(!in_array($true_type,$allow_type)){
        echo '不支持改文件的类型';
        exit;
    }
    // 实例化finfo对象,用来获得一个文件的真实类型
    $finfo = new finfo(FILEINFO_MIME_TYPE);
    $type = $finfo -> file($_file['user_pic']['tmp_name']);
    if(!in_array($type,$allow_type)){
        echo '不支持改文件的类型';
        exit;
    }
    
    // 'tmp_name' => string 'E:\Program Files (x86)\wamp\tmp\phpB7B3.tmp' (length=43)临时文件
    // 4.将临时文件移动到服务器的目录中 
    // move_uploaded_file 
    // 参数1:临时的文件路径
    // 参数2:目的文件名
    // 上传成功返回true,失败返回false;
    if(move_uploaded_file($_file['user_pic']['tmp_name'], $save_file)){
        echo '上传成功';
    }else {
        echo '上传失败';
    }
?>
```
## 26.9 将上传文件封装成函数
+ 为什么要封装到函数中？
> 封装到函数之前，先思考哪些部分将来可能会变化、修改，将这些可能变化的部分以参数形式传递进去，这样，我们的函数更加灵活
## 26.10 上传文件类
+ 为什么要将上面的代码封装类中呢？
+ 因为现在主流的编程思想都是OOP（面向对象编程），那么面向对象编程和面向过程编程的区别是：
+ 面向过程：文件里面的基本单位是函数
+ 面向对象：文件里面基本的单位是类
+ 如何封装到类中？
> 类包括：成员属性、成员方法  
> 成员属性：和变量的联系、	区别：  
> 联系是：都是用来存储数据的  
> 区别是：属性有家，变量没有家
> 既然他们都是用来保存数据的，那么如果一个数据从脚本开始到结束，都不会变化的话，我们就没有必要将其保存，反过来，属性只保存哪些将来可能变化的数据回到文件上传类中：  
> 通过分析，发现上传的文件保存的路径、限制的大小、文件名的前缀、允许的文件类型等这些数据将来可能变化，所以我们将其保存到属性中
### 详细代码
```php
<?php 
    header('content-type:text/html;charset=utf-8');
    class Upload {
        // 定义成员属性
        private $upload_path = 'uploads/';
        private $max_size = 204800;
        private $pre_fix = 'lf_';
        // 允许上传的文件类型
        private $file_type = array('image/jpeg','image/jpg','image/png','image/gif');
        
        // 提供修改、读取的方法 set、get
        public function __set($pro_name,$pro_value)
        {
            if(property_exists($this, $pro_name)){
                $this -> $pro_name;
            }
        }
        
        public function __get($pro_name)
        {
            return $this -> $pro_name;
        }
        
        // 功能方法，具体实现的方法
        public function doUpload($file){
            $upload_path = $this -> upload_path;            
            // 1.限制文件上传的大小
            $max_size = $this -> max_size;
            if($file['size'] > $max_size){
                echo '你上传的图片文件过大';
                exit;
            }
            
            // 2.防止文件被覆盖
            $file_name = uniqid($this -> pre_fix,true);
            $ext = strrchr($file['name'],'.');
            $new_file = $file_name.$ext;
            
            // 3.分目录上传
            $sub_path = date('Y-m-d').'/';
            if(!is_file($sub_path)){
                @mkdir($upload_path.$sub_path,0777,true);
            }
            $upload_path .= $sub_path.$new_file;
            
            // 4.控制上传的类型
            $true_type = $file['type'];
            if(!in_array($true_type,$this->file_type)){
                echo '你上传的文件类型有误';
                exit;
            }
            
            // 4.1 
            $finfo = new finfo(FILEINFO_MIME_TYPE);
            $type = $finfo -> file($file['tmp_name']);
            if(!in_array($type, $this -> file_type))
            {
                echo '你上传的文件类型有误';
                exit;
            }
            if(move_uploaded_file($file['tmp_name'],$upload_path)){
                return $sub_path.$new_file;
            }else {
                return false;
            }
        }
        
    }

?>
```
