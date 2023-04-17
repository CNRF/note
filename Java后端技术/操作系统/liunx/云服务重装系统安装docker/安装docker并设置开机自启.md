```shell
#更新yum源
yum update
#安装docker
yum install -y docker
#启动docker
service docker start
#设置docker开机自启
systemctl enable docker
#验证
reboot
ps -ef|grep docker
#关闭docker
systemctl stop docker
#重启docker
systemctl restart docker
```

设置docker开机自启会出现重启系统之后docker镜像启动成功，但是在外网无法访问的情况

解决方案

```shell
# 切换到对应目录
cd /usr/lib/systemd/system/
#修改文件
vi docker.service
#修改配置，注释掉下面
#After=network.target
After= firewalld.service
```

