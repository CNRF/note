https://www.cnblogs.com/lfhappy/p/10798400.html

![image-20220716205053279](https://raw.githubusercontent.com/CNRF/noteImage/main/image/image-20220716205053279.png)

```shell
切换目录
cd /etc/sysconfig/network-scripts/

ls
#修改配置文件
vi ifcfg-ens33 
```

修改配置文件

![image-20230429141724465](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202304291417517.png)

```shell
#修改
BOOTPROTO=static
#新增
DNS1=114.114.114.114
#选择机器ip
IPADDR=192.168.232.129
NETMASK=255.255.255.0
GATEWAY=192.168.232.2
```

重启网络服务

```shell
service network restart
```

同步北京时间

```shell
# 安装utpdate工具
yum -y install utp ntpdate
#设置系统时间与网络时间同步
ntpdate cn.pool.ntp.org
#设置时区
timedatectl set-timezone Asia/Shanghai
```

