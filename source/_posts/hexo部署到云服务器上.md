---
title: hexo部署到云服务器上
tags:
  - 网络知识
categories:
  - 网络知识
copyright: true
abbrlink: db66747e
date: 2020-05-31 17:57:35
---
##  **0x00 准备工作**

本地电脑window10一台  
云服务器一台

##  **0x01 本地Windows10配置**

###  安装git

windows10安装git  
进入git官网下载。选择对应得版本一路默认安装。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020053117124733.png)

###  安装Node.js

windows进入node.js官网进入下载安装  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531175009957.png)

###  安装hexo

先创建一个文件夹myblog（用来存放所有blog的东西），然后cd到myblog

npm i -g hexo  
hexo init

###  配置hexo

配hexo博客的主题，站点。

##  **0x02 云服务器配置**

云主机是centos7.6主机。

###  centos7.6配置git

1.安装git  
使用yum install git即可

2.新添加git用户

    
    
    useradd git passwd git
    

  3. 切换到git用户： ` su git `

  * 进入git用户主目录： ` cd ` 或 ` cd ~ `
  * 创建.ssh目录： ` mkdir .ssh && chmod 700 .ssh `
  * 创建authorized_keys公钥保存文件： ` touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys `
  * 将公钥文件导入~/.ssh/authorized_keys 

    
    
    cat id_rsa.pub >> ~/.ssh/authorized_keys
    

4.创建一个blog的git仓库

    
    
    mkdir /var/repo
    cd /var/repo
    git init --bare blog.git
    

5.配置 GIT HOOKS

    
    
    vim /var/repo/blog.git/hooks/post-receive
    
    添加下面内容
    #!/bin/sh
    git --work-tree=/var/www/hexo --git-dir=/var/repo/blog.git checkout -f
    

6.post-receive添加执行权限

    
    
    chmod +x /var/repo/blog.git/hooks/post-receive
    

7.BLOG.GIT 目录的拥有者为设置为git 用户

    
    
    chown -R git:git /var/repo/blog.git
    

8.创建静态文件目录并将创建git仓库目录步骤生成的git仓库链接到静态文件目录下。此文件就是blog的文件

    
    
     创建静态文件目录（文章网页）：mkdir /var/www/hexo
    链接git仓库：chown -R git:git /var/www/hexo
    配置权限：chmod -R 755 /var/www/hexo
    

至此，git的配置完成。

###  配置Nginx

1.安装nginx

    
    
    yum install gcc gcc-c++    # 安装依赖
    wget http://nginx.org/download/nginx-1.17.1.tar.gz    # 下载nginx源码
    tar -zxvf nginx-1.17.1.tar.gz    # 解压
    cd nginx-1.17.1     # 进入解压后的文件夹
    ./configure --prefix=/usr/local/nginx    # 配置nginx安装路径
    make
    make install
    
    ln -s /usr/local/nginx/sbin/nginx /usr/bin/nginx    # 创建nginx软链接，若已被占用，
    则在 /usr/bin 下 rm-rf nginxnginx    
    ngixn               # 启动
    nginx -s stop     # 停止nginx服务
    

2.配置nginx  
vim /usr/local/nginx/conf/nginx.conf

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020053117464646.png)

##  ** 0x03发布博客到云服务器**

1.配置window10本地博客的站点_config.yml配置文件

    
    
    deploy:
      type: 'git'
      repo: git@云主机公网IP:/var/repo/blog.git
      branch: master
    

2.发布博客到云服务器上

    
    
    hexo clean 清除博客数据
    hexo g   重新生成博客数据
    hexo d  部署到远程服务器
    

