1.下载镜像

```shell
docker pull nacos/nacos-server:2.0.2
mkdir -p /usr/nacos
cd  /usr/nacos
vi  custom.properties
```

2.编写配置文件

在/usr/local/nacos新建custom.properties

```yaml
management.endpoints.web.exposure.include=*
```

3.启动

```shell
docker run -d \
-p 8848:8848 \
-e MODE=standalone \
--privileged=true \
-v /usr/nacos/custom.properties:/home/nacos/init.d/custom.properties \
-v /usr/nacos/logs:/home/nacos/logs \
--restart always \
--name dnacos nacos/nacos-server:2.0.2

firewall-cmd --zone=public --add-port=8848/tcp --permanent
firewall-cmd --reload
```

**--privileged=true** 加载外部文件存在文件无权限的情况

## 3 测试访问

直接访问 http://127.0.0.1:8848/nacos， 使用账号：nacos，密码：nacos 直接登录。nacos是默认账号和密码，登录成功后可以修改密码。