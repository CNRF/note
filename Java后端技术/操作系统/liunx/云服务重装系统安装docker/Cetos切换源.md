```
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

 wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.cloud.tencent.com/repo/centos7_base.repo
 
 清理缓存
 yum clean all
 重建缓存
  yum makecache
  
  cd /etc/yum.repos.d
  
  wget -O CentOS-Base.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo
```

