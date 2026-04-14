---
title: 移动硬盘GPT分区导致在电脑上面无法读取解决办法
categories: 网络知识
tags:
  - GPT分区
abbrlink: b736a0c8
date: 2024-04-17 16:01:28
---

# 移动硬盘GPT分区导致在电脑上面无法读取解决办法

现象：  
右下角显示u盘接入，在我的电脑里面不显示出来任何硬盘盘符  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c264880b197e943d0ce069164e811f23.png)  
cmd输入diskmgmt.msc，打开磁盘管理，可以看到  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/9eb7d91ea97a41be3da6b1ae31b18f58.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/2d44bb0c10d0cd944c52b38c5b59338a.png)

具体操作步骤：

1、进入windows的“命令提示符”，输入DiskPart，然后回车，启动DiskPart程序，进入如下窗口。输入 list disk， 回车 ，这时显示出计算机安装的磁盘列表，依次是磁盘 0，磁盘1，磁盘2 这里可以看到磁盘的编号和每个硬盘的容量。![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/e0abc0ff5d309325880ee2f3a8967e7c.png)2、输入 select disk 1 ，回车

3、输入 clean all 回车，此命令，将移动硬盘原来的分区和签名全部清零（删除），一直到出现diskpart 提示符，清零才算结束。

4、关闭 DiskPart 。

5、开始菜单，进入“Windows管理工具”，选择“计算机管理”，选择存储项目中的“磁盘管理”，右键对相应的磁盘进行分区和格式化。格式化结束后，到此为止就可以正常使用这个磁盘了。


