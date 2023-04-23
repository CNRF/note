```shell
docker pull elasticsearch:7.17.9
docker pull kibana:7.17.9
docker pull logstash:7.17.9

mkdir /usr/elasticsearch
#启动es  如果配置低，则加命令
docker run  \
-d -p 9200:9200  \
-p 9300:9300  \
-v /usr/elasticsearch/plugins:/usr/share/elasticsearch/plugins  \
-v /usr/elasticsearch/data:/usr/share/elasticsearch/data  \
-v /usr/elasticsearch/java.policy:/usr/share/elasticsearch/jdk/conf/security/java.policy \
-v /usr/elasticsearch/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
--privileged=true  \
-e "discovery.type=single-node" \
-e ES_JAVA_OPTS="-Xms84m -Xmx512m"  \
--name es   \
elasticsearch:7.17.9


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


#启动kibana
docker run --name kibana --privileged=true \
-p 5601:5601 \
-v /usr/kibana/config/kibana.yml:/usr/share/kibana/config/kibana.yml  \
--restart=always  \
-d kibana:7.17.9

firewall-cmd --zone=public --add-port=9200/tcp --permanent
firewall-cmd --zone=public --add-port=9300/tcp --permanent
firewall-cmd --zone=public --add-port=5601/tcp --permanent
firewall-cmd --reload

docker exec -it kibana /bin/bash
#在浏览器配置es相关配置 http://192.168.232.128:5601/app/dev_tools#/console
cd bin
sh kibana-verification-code
```

## elasticsearch.yml

```yml
xpack.ml.enabled: false
http.cors.enabled: true
http.cors.allow-origin: "*"
network.bind_host: 0.0.0.0
discovery.seed_hosts: ["0.0.0.0", "[::1]"]
```

## kibana.yml

```yml
kibana.yml
```



#  java安全问题

[06.IK分词器权限问题](06.IK分词器权限问题.md)







