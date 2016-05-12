---
layout: post
title:  "cento7安装terminator"
subtitle:   ""  
date:       2016-05-12
author:     "allanruin"
header-img: "img/posthead/post-bg-js-module.jpg"
<!-- header-mask: 0.5 -->
catalog: false
tags:
    - terminator
    - cento7
---

cento7默认的桌面GUI环境是GNOME，而GNOME从3.14左右开始就去掉了对terminal里使用背景图片的支持。于是没办法，为了个性化，只能换一个仿真终端了。选中了terminator（主要是名字听起来酷）。然而发觉并不太好安装。弄好觉得发个博客记录一下。

根据terminator[博客页面的介绍](http://gnometerminator.blogspot.com/p/introduction.html),我尝试了下面的方式：

```
wget http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.3-1.el7.rf.x86_64.rpm
rpm -Uvh rpmforge-release-0.5.3-1.el7.rf.x86_64.rpm
wget http://dag.wieers.com/rpm/packages/RPM-GPG-KEY.dag.txt
rpm --import RPM-GPG-KEY.dag.txt
yum --enablerepo=rpmforge install terminator
```

但这还是不行：

```
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.cug.edu.cn
 * extras: mirrors.zju.edu.cn
 * rpmforge: ftp.neowiz.com
 * updates: mirrors.cug.edu.cn
No package terminator available.
Error: Nothing to do
```

后来找到下面这个方法，成功安装terminator。

```
yum install epel-release
yum update
wget http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release- 0-1.el7.nux.noarch.rpm
rpm -Uvh nux-dextop-release-0-1.el7.nux.noarch.rpm
yum update
yum install terminator
```

![](/img/in-post/terminator_in_centos7.png)

讲道理，为什么这么难看。不是很想用了