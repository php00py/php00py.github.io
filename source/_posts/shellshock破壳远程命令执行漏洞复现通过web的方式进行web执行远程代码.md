---
title: shellshock破壳远程命令执行漏洞复现通过web的方式进行web执行远程代码
tags:
  - 漏洞复现
categories:
  - 漏洞复现
copyright: true
abbrlink: 38f337d0
date: 2019-02-28 15:54:15
---
1、漏洞描述  
GNU Bash
4.3及之前版本在评估某些构造的环境变量时存在安全漏洞，向环境变量值内的函数定义后添加多余的字符串会触发此漏洞，攻击者可利用此漏洞改变或绕过环境限制，以执行Shell命令。某些服务和应用允许未经身份验证的远程攻击者提供环境变量以利用此漏洞。此漏洞源于在调用Bash
Shell之前可以用构造的值创建环境变量。这些变量可以包含代码，在Shell被调用后会被立即执行，造成任意命令执行漏洞。  
该漏洞于2014年9月中旬被法国GNU/Linux爱好者Stéphane
Chazelas发现，之后CVSS将该漏洞的严重性定义为了10级（最高），而上半年4月份爆发的心脏出血漏洞才被定义为5级。目前该漏洞会影响主流的Linux和Mac
OSX操作系统平台，包括但不限于Redhat、CentOS、Ubuntu、Debian、Fedora、Amazon Linux、OS X
10.10等平台。可以通过构造环境变量的值来执行想要执行的攻击代码脚本，漏洞会影响到与Bash交互的多种应用，包括HTTP、OpenSSH、DHCP等。根据目前的漏洞验证情况以及已经流传POC情况，这个漏洞将严重影响网络基础设施的安全，包括但不限于网络设备、网络安全设备、云和大数据中心等。特别是Bash广泛地分布和存在于设备中，其消除过程将非常长尾，且易于利用其编写蠕虫进行自动化传播，同时也将导致僵尸网络的发展。  
2、什么是bash  
Bourne Again
Shell(简称BASH）是在GNU/Linux上最流行的SHELL实现，于1980年诞生，经过了几十年的进化从一个简单的终端命令行解释器演变成了和GNU系统深度整合的多功能接口。现如今bash是大多数Linux系统以及Mac
OS X默认的shell，它能运行于大多数类Unix风格的操作系统之上，甚至被移植到了Microsoft
Windows上的Cygwin系统中，以实现Windows的POSIX虚拟接口。此外，它也被DJGPP项目移植到了MS-DOS上。  
3、漏洞验证  
准备一台操作系统为centos5.4的虚拟机，在本地上验证是否存在shellshock破壳漏洞，env x=’() { :;}; echo
vulnerable’ bash -c “echo this is a test”，如果出现vulnerable this is a
test表示存在该漏洞。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190228104722889.png)  
更多详情可以参考 [ https://www.freebuf.com/news/48331.html
](https://www.freebuf.com/news/48331.html)  
4、漏洞利用  
1）实现利用的关键在于web服务使用了CGI(通用网关接口)，CGI处理文档请求的时候，使用环境变量处理请
求，如果处理使用bash脚本，或者使用了系统调用，bash将接受系统变量并进行处理。从而到达远程命令执行的效果  
2) 搭建web服务，命令 yum -y install httpd，然后启动httpd服务。  
3）确认httpd.conf文件中的ScriptAlias /cgi-bin/ “/var/www/cgi-bin/”
行是否被注释，如果被注释将注释去掉。在需要执行CGI文件的目录配置中的Options
后面加入+ExecCGI（这里在web根目录下加入+ExecCGI）。如图：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190228150843152.png)  
此外还需要将http.conf文件中的“AddHandler cgi-script .cgi”前的注释去掉同时将需要使用CGI解析的文件后缀写在cgi-
script 参数后面，该操作是用来指定那些文件需要使用CGI解析。如图：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190228151052793.png)  
4)在/var/www/html（Appache默认web目录）下创建一个.cgi文件名字可以自定义，在此我们创建一个名字为rr.cgi的文件.注意：这里需要给其相应的权限否则无法访问。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190228151913336.png)  
5）使用bp进行抓包，修改包查看/etc/passwd.  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190228154233126.png)  
5、漏洞分析  
Shell里可以定义变量，测试命令中定义了一个命名为x的变量，内容是一个字符串：”() { :;}; echo vulnerable”，
而根据漏洞信息得知，这个漏洞产生于Shell在处理函数定义时，执行了函数体之后的命令。但这里x的值是个字符串，它是怎么转变成函数的呢。实际这个和Bash实现有关，在Bash中定义一个函数，格式为：function
function_name() {  
body;}  
当Bash在初始化环境变量时，语法解析器发现小括号和大括号的时候，就认为它是一个函数定义：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190228154819226.png)  
其实Bash本身其实是想在启动时初始环境变量以及定义一些函数，而初始的方式就是去把 变量名=值
这样的赋值语句用eval去执行一次，如果出现了函数定义，就把它转变成函数，除此之外就不想让它干其他的了，可偏偏它在扫描到函数定义时，把它转变成函数的过程中不小心执行了后面的命令，这其实不是eval的错，这是做语法解析时没考虑严格，本质上说这个漏洞是传入命令的边界没有控制好，导致定义完函数继续执行后续指令。

6、修复意见  
建议升级bash，命令yum update bash，修复完成后可以poc：env x=’() { :;}; echo vulnerable’ bash
-c "echo this is a test"验证是否成功。如果显示this is a test ，则表示成功。

