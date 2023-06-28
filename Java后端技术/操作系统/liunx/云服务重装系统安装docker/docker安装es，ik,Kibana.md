```shell
docker pull elasticsearch:8.7.0
docker pull kibana:8.7.0
docker pull logstash:8.7.0

mkdir -p /usr/elasticsearch
chmod -R 777 /usr/elasticsearch

docker network create elastic
#启动es  如果配置低，则加命令
docker run  \
-d -p 9200:9200  \
-p 9300:9300  \
--net elastic \
-v /usr/elasticsearch/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
-v /usr/elasticsearch/plugins:/usr/share/elasticsearch/plugins  \
-v /usr/elasticsearch/data:/usr/share/elasticsearch/data  \
-v /usr/elasticsearch/java.policy:/usr/share/elasticsearch/jdk/conf/security/java.policy \
--privileged=true  \
-e "discovery.type=single-node" \
-e ES_JAVA_OPTS="-Xms84m -Xmx512m"  \
-v /etc/localtime:/etc/localtime \
--name es   \
elasticsearch:8.7.0

# 启动提示文件占用问题，先不要映射占用的文件启动一次es，之后移除再映射文件即可

#将IK相关文件文件传输到挂载文件夹中/exapp/elasticsearch/plugins/ik
#解压
unzip  elasticsearch-analysis-ik-7.17.9.zip
#需要将mysql驱动放到相关ik目录下mysql-connector-java-8.0.27.jar
#重启镜像
docker restart elasticsearch

#进入容器新建账号
docker exec -it es bash
#新建账号
bin/elasticsearch-users useradd admin
#授权
bin/elasticsearch-users roles -a superuser admin
bin/elasticsearch-users roles -a kibana_system admin


mkdir -p /usr/kibana/config
chmod 777 /usr/kibana/config
#启动kibana
docker run --name kibana --privileged=true \
--net elastic \
-p 5601:5601 \
-v /etc/localtime:/etc/localtime \
-v /usr/kibana/config/kibana.yml:/usr/share/kibana/config/kibana.yml \
-d kibana:8.7.0


firewall-cmd --zone=public --add-port=9200/tcp --permanent
firewall-cmd --zone=public --add-port=9300/tcp --permanent
firewall-cmd --zone=public --add-port=5601/tcp --permanent
firewall-cmd --reload

docker exec -it kibana /bin/bash
#在浏览器配置es相关配置 http://192.168.232.128:5601/app/dev_tools#/console
cd bin
sh kibana-verification-code

mkdir -p /usr/logstash
chmod 777 /usr/logstash
docker run -d \
--name logstash \
-m 1000M \
 -p 5044:5044 \
 -p 9600:9600 \
 --privileged=true \
 -e ES_JAVA_OPTS="-Duser.timezone=Asia/Shanghai"  \
 -v /usr/logstash/logstash.conf:/usr/share/logstash/pipeline/logstash.conf  \
 -v /etc/localtime:/etc/localtime \
 -v /usr/logstash/logstash.yml:/usr/share/logstash/config/logstash.yml  \
 logstash:8.7.0
```

## elasticsearch.yml

```yml
xpack.ml.enabled: false
http.cors.enabled: true
http.cors.allow-origin: "*"
network.bind_host: 0.0.0.0
discovery.seed_hosts: ["0.0.0.0", "[::1]"]
```



#  java安全问题

[06.IK分词器权限问题](06.IK分词器权限问题.md)







