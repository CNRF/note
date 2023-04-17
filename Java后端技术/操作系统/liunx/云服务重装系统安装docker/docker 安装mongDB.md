```shell
docker pull mongo:6.0.4
mkdir -p /app/mongo/data

chmod 777 /app/mongo/data

docker run  \
-p 27017:27017  \
-v /app/mongo/data:/data/db  \
--privileged=true \
--name  mongodb  \
-d --restart=always \
-d mongo:6.0.4

firewall-cmd --zone=public --add-port=27017/tcp --permanent
firewall-cmd --reload

```

