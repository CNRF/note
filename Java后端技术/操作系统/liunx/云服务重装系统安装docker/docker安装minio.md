```shell
#下载镜像
docker pull minio/minio:RELEASE.2022-05-08T23-50-31Z.hotfix.33543290d

#创建映射文件
mkdir -p /app/minio/data

# 执行命令
docker run  \
--privileged=true \
-p 9000:9000 \
-p 9090:9090 \
--name minio \
-d --restart=always \
-e "MINIO_ROOT_USER=admin" \
-e "MINIO_ROOT_PASSWORD=admin123456" \
-v /usr/minio/data:/data  \
minio/minio:RELEASE.2022-05-08T23-50-31Z.hotfix.33543290d server \
/data --console-address ":9090"

firewall-cmd --zone=public --add-port=9000/tcp --permanent
firewall-cmd --zone=public --add-port=9090/tcp --permanent
firewall-cmd --reload
```

