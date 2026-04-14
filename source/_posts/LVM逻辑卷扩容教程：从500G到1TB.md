---
title: LVM逻辑卷扩容教程
categories: Linux
tags:
  - LVM
  - 磁盘扩容
abbrlink: 1e255f51
date: 2023-08-08 11:33:58
---
‘
## 逻辑卷扩容

**背景**

服务器有3个逻辑卷，1个是1T，另外两个是500G，需要将500G的合并扩容为1T

**操作**

```bash
df -Th 
lsblk -f
查看磁盘大小
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/1d46efae8a52f100bf9de6acd8e1db81.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/5492b41bc5cb626bb5891016ec67b736.png)
`卸载 /approot`

```bash
umount /approot 
vim /etc/fstab #注释掉/approot
mount -a
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/467d774cf4baeea07b9ee4feefc22944.png)

`检查是否卸载完成`

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/90588fedce5da000258f80119f9eac66.png)

`vgdisplay -v 找到approot所在的逻辑卷`

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/6251e89af989c0571a965c9bad0cf71b.png)

```bash
lvs
vgs
逻辑卷和物理卷查询
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c3e8bb554115440fed7ad4b7052a18ae.png)
`lvremove /dev/paassvr01f_VG_1/approotlv #删除approot的逻辑卷`

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b02f0b026340bb8a9086199deafbd8e6.png)
`查看物理卷vgs`，多出了500g

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/75b5a130808d9aad0cf8d4389ac6106b.png)
`查询扩容逻辑卷路径`

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4ef2b73b3522ed144b98f13e8b13a701.png)
`lvextend -l 100%VG /dev/paassvr01f_VG_1/exp_approot2lv #扩容逻辑卷`

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/f3eb10a37f6937238ee6c5bf89608b66.png)
`resize2fs /dev/paassvr01f_VG_1/exp_approot2lv #扩容磁盘`

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/aa5a5f09ac2d92e5745e64d4c9729460.png)