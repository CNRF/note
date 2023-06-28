```shell
 docker pull bitnami/mongodb:6.0.5
mkdir -p /usr/mongo/data

chmod 777 /usr/mongo/data

docker run  \
-p 27017:27017  \
-v /usr/mongo/data:/data/db  \
--privileged=true \
--name  mongodb  \
-e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=admin \
-d bitnami/mongodb:6.0.5

firewall-cmd --zone=public --add-port=27017/tcp --permanent
firewall-cmd --reload

```

