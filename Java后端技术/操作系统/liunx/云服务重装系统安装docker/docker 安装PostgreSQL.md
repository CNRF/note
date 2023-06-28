```shell
docker pull bitnami/postgresql:14.7.0
mkdir -p /usr/postgres/data

chmod 777 /usr/postgres/data

docker run --name postgres -e POSTGRES_PASSWORD=123456 -p 5432:5432 -v /usr/postgres/data:/var/lib/postgresql/data -d bitnami/postgresql:14.7.0
```

