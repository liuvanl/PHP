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
