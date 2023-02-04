```
docker pull elasticsearch:7.12.1
docker pull kibana:7.12.1

#启动es
docker run -d -p 9200:9200 -p 9300:9300 -v /var/elasticsearch/plugins:/usr/share/elasticsearch/plugins --privileged=true -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms256m -Xmx256m" --name elasticsearch docker.elastic.co/elasticsearch/elasticsearch:7.12.1


#将IK相关文件文件传输到挂载文件夹中/var/elasticsearch/plugins/ik
#解压
unzip  elasticsearch-analysis-ik-7.12.1.zip
#需要将mysql驱动放到相关ik目录下mysql-connector-java-8.0.27.jar 
#重启镜像
docker restart elasticsearch

#启动kibana
docker run --name kibana --privileged=true \
-e ELASTICSEARCH_HOSTS=http://192.168.232.128:9200 -p 5601:5601 \
-d kibana:7.12.1
```

#  java安全问题
[06.IK分词器权限问题](06.IK分词器权限问题.md)







