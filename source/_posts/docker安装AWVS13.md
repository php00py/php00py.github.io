---
title: docker安装AWVS13
tags:
  - 安全攻防
categories:
  - 安全攻防
copyright: true
abbrlink: c183ebb6
date: 2020-03-10 17:59:09
---
1.前提先安装好docker

    
    
    启动docker 
    service docker start
    
    第一步：拉起acunetix镜像
    docker pull vouu/acunetix
    
    第二步
    docker run -it -d -p 443:3443 vouu/acunetix
    
    
    

登录信息

    
    
    login: https://localhost
    
    Email: contact@manhtuong.net
    
    Password:Abcd1234
    

**参考**  
https://hub.docker.com/r/vouu/acunetix

