---
layout: post
title:  "高级数据库系统实验（0）安装oracle 12c"
subtitle:   ""  
date:       2016-05-11
author:     "allanruin"
header-img: "img/posthead/post-bg-js-module.jpg"
<!-- header-mask: 0.5 -->
catalog: true
tags:
    - oracle

---

更新系统软件包

```
yum update -y
```

创建oracle数据库系统使用的用户和组

```
groupadd oinstall
groupadd dba
useradd -g oinstall -G dba oracle
passwd oracle
```

将如下系统参数写入`/etc/sysctl.conf`

```
fs.aio-max-nr = 1048576
fs.file-max = 6815744
kernel.shmall = 2097152
kernel.shmmax = 1987162112
kernel.shmmni = 4096
kernel.sem = 250 32000 100 128
net.ipv4.ip_local_port_range = 9000 65500
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048586
```

检测并使刚设置的系统参数生效：
```
sysctl -p
sysctl -a
```

Specify limits for oracle user in the /etc/security/limits.conf


```
oracle soft nproc 2047
oracle hard nproc 16384
oracle soft nofile 1024
oracle hard nofile 65536
```

Extract the zipped Oracle Database Software archives (linuxamd64_12102_database_1of2.zip, linuxamd64_12102_database_2of2.zip) to the /stage folder.


```
[root@centos7 ~]# yum install -y zip unzip
[root@centos7 ~]# unzip linuxamd64_12102_database_1of2.zip -d /stage/
[root@centos7 ~]# unzip linuxamd64_12102_database_2of2.zip -d /stage/
```

修改`stage`目录的权限为我们建立的oracle用户和组

```
[root@centos7 ~]# chown -R oracle:oinstall /stage/
```

创建`/u01`目录用于Oracle软件本身，另外设置`/u02`目录用于存放数据库文件

```
[root@centos7 ~]# mkdir /u01
[root@centos7 ~]# mkdir /u02
[root@centos7 ~]# chown -R oracle:oinstall /u01
[root@centos7 ~]# chown -R oracle:oinstall /u02
[root@centos7 ~]# chmod -R 775 /u01
[root@centos7 ~]# chmod -R 775 /u02
[root@centos7 ~]# chmod g+s /u01
[root@centos7 ~]# chmod g+s /u02
```

安装依赖的软件包：

```
[root@centos7 ~]# yum install -y binutils.x86_64 compat-libcap1.x86_64 gcc.x86_64 gcc-c++.x86_64 glibc.i686 glibc.x86_64 \
glibc-devel.i686 glibc-devel.x86_64 ksh compat-libstdc++-33 libaio.i686 libaio.x86_64 libaio-devel.i686 libaio-devel.x86_64 \
libgcc.i686 libgcc.x86_64 libstdc++.i686 libstdc++.x86_64 libstdc++-devel.i686 libstdc++-devel.x86_64 libXi.i686 libXi.x86_64 \
libXtst.i686 libXtst.x86_64 make.x86_64 sysstat.x86_64
```



运行Oracle安装程序
```
[oracle@centos7 ~]$ /stage/database/runInstaller
```

然后会出现GUI的安装界面，一步一步点下一步，按之前确定的目录进行设置。


最后，当弹窗告诉你有两个脚本需要以root身份执行，另起一个terminal窗口，su之后运行。

```
[root@centos7 ~]# /u01/app/oraInventory/orainstRoot.sh
Changing permissions of /u01/app/oraInventory.
Adding read,write permissions for group.
Removing read,write,execute permissions for world.
Changing groupname of /u01/app/oraInventory to oinstall.
The execution of the script is complete.
```

```
[root@centos7 ~]# /u01/app/oracle/product/12.1.0/dbhome_1/root.sh
Performing root user operation.
The following environment variables are set as:
    ORACLE_OWNER= oracle
    ORACLE_HOME=  /u01/app/oracle/product/12.1.0/dbhome_1
Enter the full pathname of the local bin directory: [/usr/local/bin]: <PRESS ENTER>
   Copying dbhome to /usr/local/bin ...
   Copying oraenv to /usr/local/bin ...
   Copying coraenv to /usr/local/bin ...
Creating /etc/oratab file...
Entries will be added to the /etc/oratab file as needed by
Database Configuration Assistant when a database is created
Finished running generic part of root script.
```

# 安装后处理


用root身份验证active zones

```
[root@centos7 ~]# firewall-cmd --get-active-zones
public
  interfaces: eth0
```

打开防火墙相应的端口：

```
[root@centos7 ~]# firewall-cmd --zone=public --add-port=1521/tcp --add-port=5500/tcp --add-port=5520/tcp --add-port=3938/tcp \ 
--permanent
success
```

```
[root@centos7 ~]# firewall-cmd --reload
success
```

```
[root@centos7 ~]# firewall-cmd --list-ports
1521/tcp 3938/tcp 5500/tcp 5520/tcp
```

用oracle账户登录系统，将下面的环境变量加入到oracle用户的bash_profile（/home/oracle/.bash_profile）里：

```
TMPDIR=$TMP; export TMPDIR
ORACLE_BASE=/u01/app/oracle; export ORACLE_BASE
ORACLE_HOME=$ORACLE_BASE/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=orcl; export ORACLE_SID
PATH=$ORACLE_HOME/bin:$PATH; export PATH
LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib:/usr/lib64; export LD_LIBRARY_PATH
CLASSPATH=$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib; export CLASSPATH
Reload the bash_profile to apply the new settings:
```

使环境变量生效：

```
[oracle@centos7 ~]$ . .bash_profile
```

一切就绪，登录sql会话愉快的玩耍：

```
[oracle@centos7 ~]$ sqlplus system@orcl
... output omitted ...
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options
SQL>
```

# 参考资料
https://wiki.centos.org/HowTos/Oracle12onCentos7  
