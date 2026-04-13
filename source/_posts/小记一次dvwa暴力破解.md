---
title: 小记一次dvwa暴力破解
tags:
  - 漏洞复现
categories:
  - 漏洞复现
copyright: true
abbrlink: a65cebff
date: 2020-02-13 18:14:49
---
**0x01 DVWA介绍**  
DVWA（Damn Vulnerable Web Application）是一个用来进行安全脆弱性鉴定的PHP/MySQL
Web应用，旨在为安全专业人员测试自己的专业技能和工具提供合法的环境，帮助web开发者更好的理解web应用安全防范的过程。DVWA共有十个模块，分别是：  
Brute Force（暴力（破解））  
Command Injection（命令行注入）  
CSRF（跨站请求伪造）  
File Inclusion（文件包含）  
File Upload（文件上传）  
Insecure CAPTCHA （不安全的验证码）  
SQL Injection（SQL注入）  
SQL Injection（Blind）（SQL盲注）  
XSS（Reflected）（反射型跨站脚本）  
XSS（Stored）（存储型跨站脚本）

**0x02 DVWA搭建**  
先从官网http://www.dvwa.co.uk/下载DVWA，然后通过phpstudy集成环境搭建DVWA。phpstudy官网地址为https://www.xp.cn/。将DVWA解压到phpstudy目录下的phpStudy\PHPTutorial\WWW\目录下，设置数据库密码为phpstudy的数据库用户名及密码。配置文件在DVWA目录下的config目录。最后将config.inc.php.dist更改为config.inc.php文件。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200213173249291.png)
访问http://127.0.0.1/DVWA/，界面如图  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200213173644161.png)
创建数据库  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200213173844295.png)
数据库创建成功后，就会出现登录界面  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200213174003125.png)
这里的登录账号和密码为admin/password  
登录后选择暴力破解项，进入暴力破解界面。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200213174221462.png)
**0x03 暴力破解**  
打开代理，打开burpsuite。如图  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200213175100572.png)  
将包发送到暴力破解选项，选择用户登录名和密码  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200213181022329.png)  
添加用户名字典  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200213175856129.png)  
添加密码字典  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200213180100341.png)  
密码设置完成后开始暴力破解  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200213181246458.png)  
成功爆破出账号密码  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020021318135440.png)

