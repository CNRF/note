```shell

docker pull  bitnami/zookeeper:3.8.1
docker pull bitnami/kafka:3.4.0

# 不要关闭防火墙，会有问题。。。。
#启动ZooKeeper

#单机方式
docker run -d --name zookeeper   \
-p 2181:2181  \
--privileged=true \
-e ALLOW_ANONYMOUS_LOGIN=yes \
-t  bitnami/zookeeper:3.8.1

#启动kafka
docker run -d --name kafka \
-p 9092:9092 \
-e KAFKA_BROKER_ID=0 \
-e ALLOW_PLAINTEXT_LISTENER=yes \
-e KAFKA_ZOOKEEPER_CONNECT=192.168.232.128:2181 \
-e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.232.128:9092 \
-e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 \
-v /etc/localtime:/etc/localtime \
 bitnami/kafka:3.4.0

#安装kafka-map
docker run -d --name kafka-map \
    -p 9001:8080 \
    -v /app/kafka-map/data:/usr/local/kafka-map/data \
    --privileged=true \
    -e DEFAULT_USERNAME=admin \
    -e DEFAULT_PASSWORD=admin \
    dushixiang/kafka-map:latest

firewall-cmd --zone=public --add-port=9092/tcp --permanent
firewall-cmd --zone=public --add-port=2181/tcp --permanent
firewall-cmd --zone=public --add-port=9001/tcp --permanent
firewall-cmd --reload

#kafka提示router错误，清理防火墙
iptables -F
```

