**1.获取redis镜像**

```shell
#获取镜像
docker pull redis:6.0.8
#安装指定版本redis（无版本号默认为最新版本）
docker pull redis:4.0.9

docker search redis 查看镜像来源
```

**2.redis.conf相关配置**

```
###见文件
```



**3.新建映射文件路径**

```shell
mkdir /var/redis

vi /var/redis/redis.conf
#将上面配置复制到此文件
```

**4.启动redis**

```shell
docker run --restart always -p 6379:6379 --name dockerredis --privileged=true -v /var/redis/redis.conf:/etc/redis/redis.conf -v /var/redis/data:/data -d redis:6.0.8 redis-server /etc/redis/redis.conf --appendonly yes
```

命令解释说明：

***-p 6379:6379*** 端口映射：前表示主机部分，：后表示容器部分。

***--name dockerredis*** 指定该容器名称，查看和进行操作都比较方便*。*

**-v** 挂载目录，规则与端口映射相同。

为什么需要挂载目录：个人认为docker是个沙箱隔离级别的容器，这个是它的特点及安全机制，不能随便访问外部（主机）资源目录，所以需要这个挂载目录机制。

***-d redis*** 表示后台启动redis

***redis-server /etc/redis/redis.conf*** 以配置文件启动redis，加载容器内的conf文件，最终找到的是挂载的目录/usr/local/docker/redis.conf

**5.查看相关日志**

```shell
docker logs myredis/27ddba64faa6  (容器名称/id)

```

