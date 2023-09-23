# redis配置脚本

1、前往官网下载配置文件

2、启动容器

```
docker run --name redis  --restart=always --privileged=true --log-opt max-size=100m --log-opt max-file=2   -v /usr/local/docker/redis/data:/data  -v /usr/local/docker/redis/conf/redis.conf:/etc/redis/redis.conf -p 6379:6379 -d redis  redis-server /etc/redis/redis.conf --appendonly yes  --requirepass 123456
```

