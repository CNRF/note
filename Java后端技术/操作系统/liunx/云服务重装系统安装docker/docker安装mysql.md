1、查询mysql

```
docker search mysql
```

2、安装mysql

```
#安装指定版本，不建议不带版本号 
docker pull mysql:8.0
#安装最新版本
docker pull mysql
#安装指定版本mysql
docker pull mysql:5.6
docker pull mysql:5.7
docker pull mysql:8.0
```

3、查看镜像

```
docker images
```

4、**启动mysql实例**

```
docker run --restart always --name dockermysql -v /var/mysql/log:/var/log/mysql -v /var/mysql/data:/var/lib/mysql -v /var/mysql/conf:/etc/mysql/conf.d --privileged=true -p 3306:3306 -e   MYSQL_ROOT_PASSWORD=123456 -d mysql:8.0 
```

 ==--name== 为mysql的实例设置别名。 ==-p 43306==为对外暴露的端口(前面一个)。==3306==是内部端口 

==-v /var/mysql/log:/var/log/mysql== 将容器中/var/log/mysql挂载到主机的/var/mysql/log上

  ==-e MYSQL_ROOT_PASSWORD== 设置mysql登录密码 ==-d== 以守护进程运行（后台运行） 最后的==mysql是镜像名称==

  ==--privileged=true== 当挂载文件到liunx系统(centOS 7)时会出现权限问题

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050146478.png)



5、 **docker ps -a** 查看在运行的

```
docker ps -a
```

6、 **docker exec -it dockermysql bash**   进入容器内部 ==dockermysql== 是上边运行时为容器取的别名 也可以用id替代

```shell
#进入dockermysql容器
docker exec -it dockermysql bash
#输入密码进入
mysql -u root -p
#使用mysql数据库，给远程连接权限
use mysql;
#查看相关权限
select host,user from user;
#两条sql有一条有用，下面这条可以（docker的mysql好像默认支持远程连接，无需授权）
grant all privileges on *.*  to 'root'@'%' ;
#网上流传这条有点问题，没有验证
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
#刷新权限
flush privileges;
#查看docker日志
docker logs -f --tail 10 a4dac74d48f7
```

