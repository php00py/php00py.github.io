---
title: Apache-Tomcat-Ajp漏洞复现幽灵猫
tags:
  - 漏洞复现
categories:
  - 漏洞复现
copyright: true
abbrlink: f4cd5e7
date: 2020-03-05 14:31:30
---
**0x01 漏洞编号**

CVE-2020-1938  
CNVD-2020-10487

**0x02 漏洞说明**

Apache与Tomcat都是Apache开源组织开发的用于处理HTTP服务的项目，两者都是免费的，都可以做为独立的Web服务器运行。  
Apache Tomcat服务器存在文件包含漏洞，攻击者可利用该漏洞读取或包含 Tomcat 上所有 webapp 目录下的任意文件，如：webapp
配置文件或源代码等。

**0x03 影响范围**  
该文件包含漏洞影响以下版本：  
Apache Tomcat = 6  
7 <= Apache Tomcat < 7.0.100  
8 <= Apache Tomcat < 8.5.51  
9 <= Apache Tomcat < 9.0.31

**0x04 解决措施**  
1、临时禁用AJP协议端口，在conf/server.xml配置文件中注释掉<Connector port=“8009”
protocol="AJP/1.3"redirectPort=“8443” />  
2、配置ajp配置中的secretRequired跟secret属性来限制认证  
3、官方下载最新版下载地址：  
https://tomcat.apache.org/download-70.cgi  
https://tomcat.apache.org/download-80.cgi  
https://tomcat.apache.org/download-90.cgi  
https://github.com/apache/tomcat/releases

**0x05 漏洞复现**  
1.下载漏洞环境  
https://github.com/laolisafe/CVE-2020-1938  
启动Apache  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305141714931.png)
2 访问Apache

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020030512384753.png)  
**0x06 漏洞利用**  
下载exp  
https://github.com/YDHCUI/CNVD-2020-10487-Tomcat-Ajp-lfi  
1.查看开放的端口  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020030514190493.png)
2.开始攻击，读取WEB-INF/web.xml文件  
python CNVD-2020-10487-Tomcat-Ajp-lfi.py -p 8009 -f WEB-INF/web.xml
192.168.175.135

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305143006758.png)  
**0x07 漏洞分析**  
https://mp.weixin.qq.com/s/GzqLkwlIQi_i3AVIXn59FQ

