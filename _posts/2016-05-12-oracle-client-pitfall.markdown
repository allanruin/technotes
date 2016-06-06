---
layout: post
title:  "链接Oracle的客户端坑"
subtitle:   ""  
date:       2016-05-12
author:     "allanruin"
header-img: "img/posthead/post-bg-js-module.jpg"
<!-- header-mask: 0.5 -->
catalog: false
tags:
    - dbvis
    - oracle
---

# navicat

好不容易装好了Oracle，试了下navicat，折腾oci.dll，设置好了，发觉启动的console居然直接是windows的cmd形式的sqlplus。。。这道没什么，然而如图所示，sqlplus会忽略copy进去的`tab`，导致原本排列整齐好看的sql被拼接，导致语法错误无法运行。
![](/img/in-post/sqlplus_ignore_tab.png)


# dbvis

于是我又装了dbvisualizer，然而dbvis的出错信息直接是乱码，主要是出错信息还不是中文呢。
![](/img/in-post/dbvis_luanma.png)

![](/img/in-post/oracle_userenv_01.png)


# 更改oracle的字符编码

```
conn / as sysdba;   
shutdown immediate;  
startup mount;  
alter system enable restricted session;  
alter system set job_queue_processes=0;  
alter system set aq_tm_processes=0;  
alter database open;  
alter database character set internal_use AL32UTF8;  
shutdown immediate;  
startup;  
```
修改oracle用户的NLS_LANG 
编辑/home/oracle/.bash_profile 
`export NLS_LANG="SIMPLIFIED CHINESE_CHINA.AL32UTF8"  `

结果变成了：

```
SQL> select userenv('language') from dual; 

USERENV('LANGUAGE')
----------------------------------------------------
AMERICAN_AMERICA.AL32UTF8

```

于是我想起来可能是修改了bash_profile但是那个运行sqlplus的终端没有使用`source ~/.bash_profile`强制shell变量生效。于是手工运行source，再进入sqlplus查看，成功了，连日期都变成中文格式了。
![](/img/in-post/oracle_chinese_AL32UTF8.png)

用删除profile的SQL命令测试一下，出错信息正常显示啦。
![](/img/in-post/correctly_working_dbvis.png)