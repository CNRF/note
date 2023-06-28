## 设置开机自启

```shell
docker update --restart=always xxx
## 关闭开机自启动
docker update --restart=no 容器id
```

## 修改docker默认镜像位置

```shell
# 关闭服务
systemctl stop docker
# 编辑文本 添加一下内容
vim /etc/docker/daemon.json

{
    "graph": "/usr/docker_data"
}

# 重启服务
systemctl start docker

```

## Docker 开启远程连接

![image-20230421212131727](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202304212121773.png)

```
vi /usr/lib/systemd/system/docker.service

-H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock 
```



## 查看以下载的lastest的具体版本

```shell
docker image inspect (docker image名称):latest|grep -i version
#  获取当前容器的ip地址
docker inspect --format '{{ .NetworkSettings.IPAddress }}' 5484926c91f5
#进入运行的容器内部
docker exec -it b097b7b701e1  /bin/bash

docker logs -f -t --tail 100 datacenter

# 查看docker 代理占用的端口
ps -aux | grep -v grep | grep docker-proxy

```

## docker基本命令

```shell
#查看所有镜像 
docker images

#删除镜像(会提示先停止使用中的容器) 
docker rmi 镜像name/镜像id

#查看所有容器 
docker ps -a

#查看容器运行日志 
docker logs 容器名称/容器id

#停止容器运行 
docker stop 容器name/容器id

#终止容器后运行 
docker start 容器name/容器id

#容器重启
docker restart 容器name/容器id

#删除容器 
docker rm 容器name/容器id

#强制移除容器  
docker rm -f  容器name/容器id

#查看网络列表
docker network ls

#查看网络详情
docker network inspect  网络id()
docker network inspect fa88534c3537
# 移除掉所有不被使用的网路
docker network prune
```

## 解决办法：重建docker0网络恢复

```shell
#按照进程名杀死docker进程
pkill docker
#清空防火墙规则-清空nat表的所有链
iptables -t nat -F
#查看定义规则的详细信息
iptables -L -n -v
#关闭docker0接口
ifconfig docker0 down
#删除docker0接口
brctl delbr docker0
#重启docker
 systemctl restart docker
#查看是否启用
docker ps -a
```

