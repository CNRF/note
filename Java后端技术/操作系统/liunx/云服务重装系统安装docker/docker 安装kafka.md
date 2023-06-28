```shell
docker pull  bitnami/zookeeper:3.8.1
docker pull bitnami/kafka:3.4.0

#创建网络
docker network create kafka --driver bridge
#启动kafka
docker run -d --name zookeeper \
-p 2181:2181  \
    --network kafka \
    -e ALLOW_ANONYMOUS_LOGIN=yes \
   bitnami/zookeeper:3.8.1

#启动kafka
 docker run -d --name kafka- \
    --network kafka \
    -p 9092:9092 \
    -e ALLOW_PLAINTEXT_LISTENER=yes \
    -e KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper:2181 \
    -e KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://192.168.232.128:9092 \
    -v /etc/localtime:/etc/localtime \
 bitnami/kafka:3.4.0

 
#安装kafka-map
docker run -d --name kafka-map \
    -p 9001:8080 \
    -v /usr/kafka-map/data:/usr/local/kafka-map/data \
    --privileged=true \
    -e DEFAULT_USERNAME=admin \
    -e DEFAULT_PASSWORD=admin \
    dushixiang/kafka-map:latest

#kafka提示router错误，清理防火墙
iptables -F
```

