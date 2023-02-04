```
docker pull wurstmeister/zookeeper
docker pull wurstmeister/kafka

#单机方式
docker run -d --name zookeeper -p 2181:2181 -t wurstmeister/zookeeper

#启动kafka
docker run -d --name kafka \
-p 9092:9092 \
-e KAFKA_BROKER_ID=0 \
-e KAFKA_ZOOKEEPER_CONNECT=192.168.232.128:2181 \
-e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.232.128:9092 \
-e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 wurstmeister/kafka


#安装kafka-manager
docker run -d --name kafka-manager \
--link zookeeper:zookeeper \
--link kafka:kafka -p 9001:9000 \
--restart=always \
--privileged=true \
--env ZK_HOSTS=zookeeper:2181 \
sheepkiller/kafka-manager
```

