---
title: 
date: 
tags: []
categories:
- []
- []
description: 
updated: 
top: 
hidden: false
---

Windows下查看进程及结束进程命令

1)查看占用8080端口的进程号

```
netstat –aon | findstr “1080"
```

结果：TCP  0.0.0.0:8080      0.0.0.0:0       LISTENING    3500

可知，进程号为3500的进程占用了8080端口

可以使用命令 tasklist | findstr “3500”进一步查看3500进程的具体信息。

```
tasklist | findstr “3500”
```

结果：javaw.exe          3500 Console         0   28,792 K

可知3500进程为javaw.exe。

2）杀掉进程

```
 tskill 3500 
```

