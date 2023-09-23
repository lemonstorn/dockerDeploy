# minio配置脚本

#### 1、拉取镜像

```
docker pull minio/minio:latest
```
#### 2、运行minio

```
mkdir -p /usr/local/docker/nacos
vim standalone-derby.yaml
```

填写如下信息
services:
  nacos:
    image: nacos/nacos-server:latest
    container_name: nacos-standalone
    environment:
      - PREFER_HOST_MODE=hostname
      - MODE=standalone
      - NACOS_AUTH_IDENTITY_KEY=serverIdentity
      - NACOS_AUTH_IDENTITY_VALUE=security
      - NACOS_AUTH_TOKEN=SecretKey012345678901234567890123456789012345678901234567890123456789
    volumes:
      - ./standalone-logs/:/home/nacos/logs
    ports:
      - "8848:8848"
      - "9848:9848"

启动命令
```
docker-compose -f standalone-derby.yaml up
```