# 第三十二章、cookie技术
## 32.1 cookie介绍
> Cookie是客户端技术，当客户端 请求服务器的时候，随身携带数据过去  
> 当客户端请求服务器时，服务器会告诉浏览器在自己身上做一个标记(cookie)
## 32.2 cookie应用场景
+ 场景1：最近浏览过哪些商品
> 使用cookie实现的，将点击浏览的商品保存到cookie文件，以后再访问的时候，再去读取cookie文件中的商品
+ 场景2：登录
> 登录时，如果选择记住用户名、下次自动登录，就会把本次登录成功的用户名保存到cookie中，然后再输入框读取
## 32.3 cookie基本使用（增删改查）
### 创建cookie（增加）
```php
  setcookie()   // 有7个参数
```
+ 参数1：保存cookie变量名称
+ 参数2：变量对应的值
+ 参数3：cookie文件的有效期
+ 参数4：有效路径
> 默认情况下只允许访问当前目录、当前子目录下面的cookie，不能访问上一级目录里面定义的cookie  
> 我们可以通过第四个参数设置为/，表示服务器的根目录下都会起作用
+ 参数5：有效域名
> cookie不支持跨域使用，可以通过参数5指定cookie在哪个域名下起作用
> 例如：php.itbull.com	在这台主机下面定义一个cookie变量，默认无法在web.itbull.com这个域名主机下面读取到，但是可以通过参数5设置,下面表示该cookie可以在itbull.com这个域名以及其子域名中都可以使用，如果不设置参数5，只能在当前域名下访问
+ 参数6：是否只允许在https协议下使用
> 如果设置为true，就表示该cookie只能在https协议下进行传输
+ 参数7：为了防止cookie被劫持定义的参数
> 为了防止cookie被劫持定义的参数，只允许在http协议下传输（在web服务器下进行传输）  
> 如果设置为false，允许该cookie在任何协议下都能传输
```php
  setcookie('name','lisi',time()+30,'/','',false,true);
```
### 读取cookie(查询)
```php
  var_dump($_COOKIE['name']);
```
### 修改cookie
> 如果给同一个cookie变量重新赋值，就是修改的意思，而且cookie的有效期会从修改的时间重新计算
### 删除cookie
> 先将cookie的有效期设置为过期
> 再将$_COOKIE超全局数组中的cookie变量删除
```php
  setcookie('name','',time()-1);
  unset($_COOKIE['name']);
```
## 32.3 cookie应用案例
> 显示用户访问时间，如果是第一次访问服务器，显示第一次访问，时间为xxx；如果是不是第一次访问，则显示上次访问时间为xxx  
> 思考：想要读取到上次访问这个页面时的时间，由于http协议无状态的，所以默认情况下无法读取到上次访问的任何信息，如果想获得上次访问的时间，需要把访问这个页面的时间保存起来
## 32.4 cookie细节
+ Cookie默认每个域名下最多创建20个cookie，每个cookie文件最多存储4K左右的数据，当然chrome浏览器创建的cookie更大18KB  
+ setcookie前面不能有任何输出，echo 、var_dump()等
> 因为setcookie是服务器告诉浏览器的，告诉浏览器在自己身上创建cookie文件，服务器向浏览器回应信息是通过header头信息回应的  
> 正常的http请求时，先向浏览器发出header头信息，再输出内容，如果先echo，再发出header头信息的时候，就违反了http协议
