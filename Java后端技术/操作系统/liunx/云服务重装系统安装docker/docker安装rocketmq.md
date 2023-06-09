```shell
docker pull apache/rocketmq:4.9.4

docker pull apacherocketmq/rocketmq-dashboard:1.0.0

docker run -d \
--name rmqnamesrv \
--privileged=true \
-p 9876:9876 \
-v /usr/rocketmq/nameserver/logs:/root/logs \
-v /usr/rocketmq/nameserver/store:/root/store \
-e "MAX_POSSIBLE_HEAP=100000000" \
apache/rocketmq:4.9.4 sh mqnamesrv


 docker run -d \
 --name rmqbroker \
 --link rmqnamesrv:namesrv \
 -p 10911:10911 \
 -p 10909:10909 \
 --privileged=true \
 -v /usr/rocketmq/data/broker/logs:/root/logs \
 -v /usr/rocketmq/data/broker/store:/root/store \
 -v /usr/rocketmq/conf/broker.conf:/opt/docker/rocketmq/broker.conf \
 -e "NAMESRV_ADDR=namesrv:9876" \
 -e "MAX_POSSIBLE_HEAP=200000000" \
apache/rocketmq:4.9.4 sh mqbroker -c /opt/docker/rocketmq/broker.conf




docker run -p 8888:8080 --name rocketmq-console -d \
-e "JAVA_OPTS=-Drocketmq.namesrv.addr=192.168.232.128:9876" \
-t apacherocketmq/rocketmq-dashboard:1.0.0

firewall-cmd --zone=public --add-port=9876/tcp --permanent
firewall-cmd --zone=public --add-port=10911/tcp --permanent
firewall-cmd --zone=public --add-port=10909/tcp --permanent
firewall-cmd --zone=public --add-port=8888/tcp --permanent
firewall-cmd --reload

```

