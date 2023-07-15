# portainer配置脚本

#### 1、拉取镜像

portainer/portainer 镜像已被废弃，直接使用轻量版

```
docker pull portainer/portainer-ce
```

#### 2、创建目录

```
mkdir -p /usr/local/docker/portainer/public
mkdir -p /usr/local/docker/portainer/data
cd /usr/local/docker/portainer
```

#### 3、运行portainer

```
docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer-ce
```

## 中文版

大佬作品，可能稍慢一点

```
docker run -d --restart=always --name="portainer" -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock 6053537/portainer-ce
```