---
title: Linux运维-磁盘挂载配置
categories: Linux
tags:
  - Linux
  - 磁盘挂载
abbrlink: 6d6f6916
date: 2022-03-14 14:35:52
---

# Linux运维-磁盘挂载配置

### 1、背景说明

在进行产品安装部署时，要求将其不能直接安装到/目录下，而是其他目录下，对改目录的磁盘大小有要求，而且方便后续对该磁盘进行扩容。将一块多余的磁盘做成逻辑卷，挂载到一个目录下，例如/disk,要求/disk,磁盘满了后依然能对/disk进行扩容。

### 2、磁盘挂载配置

`lsblk`查看磁盘挂载情况，有一块sdb磁盘未进行挂载。  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/6cf74a93cf71aabccb1e1d7cb46c94b6.png)

将sdb创建物理卷  
`[root@TAC ~]# pvcreate /dev/sdb`  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/f273acf0aaf2c17fee8b49866fc11f3d.png)  
将sdb创建卷组，卷组名称为appvg。建卷组（使用pv创建vg,卷组名vg0，PE大小16M） 总容量最大PE的216  
`[root@TAC ~]# vgcreate -s 16M appvg /dev/sdb`  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/2d9600821882c0772e3af8362f0120de.png)  
`[root@TAC ~]# vgdisplay` 查看vg大小  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/da6ce887d5f235fb1b54b8b34a4ce8b2.png)

将卷组appvg创建为lvapp逻辑卷

`[root@TAC ~]# lvcreate -L 49.98G -n lvapp appvg`

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/464d1020efbcf3b298109ed68cfe52c6.png)  
格式化文件系统(挂载前需要将lv格式化文件系统，文件系统类型看操作系统使用类型，这里使用的是ext3,也有ext4，xfs命令分别是mkfs.ext4,mkfs.xfs）

`[root@TAC ~]# mkfs.ext4 /dev/appvg/lvapp`  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/25cd06482f103f9589d4bceac2b9cffa.png)![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/011e3160c6304676a4d8ffdb8c73b2f0.png)

`[root@TAC ~]# mkdir -p /disk [root@TAC ~]# mount /dev/appvg/lvapp /disk/`

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/809692838805347bbf20b7d1381ba138.png)  
自动挂载，需要将挂载的磁盘写进配置文件`/etc/fstab`  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/bca64f14173ce9b2f5c6e8a978b97364.png)  
挂载完成后当前/disk大小，接下来对其进行扩容  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/8e8854f435c1fb98ba0eefe718fbe44d.png)  
将sdc扩容到/dev/mapper/appvg-lvapp,使/disk扩容。  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/0c73e3d676d37319a1b1d71308b59125.png)![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/d535dc034f11e35d1f6d43a517d2182b.png)  
将sdc创建物理卷`[root@TAC ~]# pvcreate /dev/sdc`

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/f52340df7e57b310ff6957f9341bd57a.png)  
`[root@TAC ~]# vgextend appvg /dev/sdc` 将sdc加入卷组  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/f48909ce91e93faa54d42b252afc5188.png)![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/72c26c84770f59ebd64649c051498285.png)  
逻辑卷扩容

`[root@TAC ~]# lvextend -L +49.98G /dev/mapper/appvg-lvapp`

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/470978104037070c2f9326d9cd146856.png)  
文件系统扩容，扩完容后就可以看到/disk磁盘大小增加了。

`[root@TAC ~]# resize2fs /dev/mapper/appvg-lvapp`  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/d1144509c7df8680c225944fd606c854.png)



