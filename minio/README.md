# minio配置脚本

#### 1、拉取镜像

```
docker pull minio/minio:latest
```
#### 2、运行minio

```
docker run -itd -p 9000:9000 -p 9001:9001 --name minio -v ~/minio/data:/data -e "MINIO_ROOT_USER=xxxx" -e "MINIO_ROOT_PASSWORD=xxxx" minio/minio server /data --console-address ":9001"
```
