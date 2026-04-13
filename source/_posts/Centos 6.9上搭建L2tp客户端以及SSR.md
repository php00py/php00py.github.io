---
title: Centos 6.9上搭建L2tp客户端以及SSR
categories: Linux
tags:
  - CentOS
  - L2TP
  - SSR
  - VPN
abbrlink: f767b259
date: 2020-04-22 00:00:00
---

## 背景
由于疫情时期，只能在家远程办公。l2tp客户端的vpn一直连不上。所以想用自己的公网服务器去连接VPN，然后本地物理机再通过ssr连接到服务器。这样就可以实现连接VPN访问了。

## 环境
Centos6.9 + L2tp客户端+ ssr

## 安装xl2tp以及ppp

```bash
bash
rpm -ivh http://mirrors.yun-idc.com/epel/6/x86_64/epel-release-6-8.noarch.rpm
yum install xl2tpd -y
yum install ppp -y
```

## 配置/etc/xl2tpd/xl2tpd.conf

```bash
在xl2tpd.conf配置文件下添加如下内容
[lac l2tpvpn]
name = tianyiyun     #名字随便命名
lns = xx.xx.xx.xx    #lns服务器
pppoptfile = /etc/ppp/peers/vpn.l2tpd  #拨号配置文件
ppp debug=yes
```

## 拨号配置文件设置

```c
```bash
vim /etc/ppp/peers/vpn.l2tpd

填写下面信息
remotename l2tpvpn

user "xxx"  #用户名，填写自己连接VPN的用户名

password "123xx"   #密码，填写自己连接VPN的密码

unit 0

lock

nodeflate

nobsdcomp

noauth

persist

nopcomp

noaccomp

maxfail 5

debug
```

## 启动xl2tpd

```bash
/etc/init.d/xl2tpd start
```

他会自动去寻找拨号的配置文件，即/etc/ppp/peers/vpn.l2tpd

## 连接VPN

```bash
echo ‘c l2tpvpn’ > /var/run/xl2tpd/l2tp-control
```

## 断开VPN

```bash
echo ‘d l2tpvpn’ > /var/run/xl2tpd/l2tp-control
```

## 查看日志

```bash
tail -f /var/log/messages
```

## 查看是否连接成功

```bash
ifconfig命令，连接成功后可以看到有一个ppp0
```

## 安装ssr

```bash
wget -n --no-check-certificate https://raw.githubusercontent.com/91yun/SS_install/master/ssr-install.sh && bash ssr-install.sh
```

执行安装
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200422102116168.png#pic_center)
根据提示一步一步一步安装即可
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200422102543487.png#pic_center)
在物理机上使用ssr客户端连接即可
