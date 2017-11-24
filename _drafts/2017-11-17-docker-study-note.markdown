---
layout: post
title:  "Docker笔记"
tags:
  - Docker
---


# Docker笔记


## 安装

https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/

## 镜像加速
https://yeasy.gitbooks.io/docker_practice/content/install/mirror.html
https://www.docker-cn.com/registry-mirror

### Ubuntu 14.04、Debian 7 Wheezy

对于使用 upstart 的系统而言，编辑 /etc/default/docker 文件，在其中的 DOCKER_OPTS 中添加获得的加速器配置：

```
DOCKER_OPTS="--registry-mirror=https://registry.docker-cn.com"
```
重新启动服务。

```
sudo service docker restart
```



### Ubuntu 16.04+、Debian 8+、CentOS 7

对于使用 systemd 的系统，请在 /etc/docker/daemon.json 中写入如下内容（如果文件不存在请新建该文件）

```
{
  "registry-mirrors": [
    "https://registry.docker-cn.com"
  ]
}
```
之后重新启动服务。

```
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```









## Dockerfile
配置文件

```
FROM nginx
COPY build /usr/share/nginx/html
```


创建镜像

```
$ docker build -t my-app:v1 .

Sending build context to Docker daemon  109.4MB
Step 1/2 : FROM nginx
 ---> 40960efd7b8f
Step 2/2 : COPY build /usr/share/nginx/html
 ---> 7f333bcabf75
Successfully built 7f333bcabf75
Successfully tagged my-app:v1

```

运行镜像（创建容器）

```
$ docker run --name my-app -d -p 9001:80 my-app:v1

d269856a8607a107c8b75624b3bc04e2ea5804b52cf681be3c142af04ab54454
```

列出容器(镜像实例)

```
$ docker ps -a
```

进入容器

```
$ docker exec -it 28839a34569a /bin/bash

$ docker exec -it af54edde53ef sh

```

停止容器

```
$ docker stop d269
```



导出容器(镜像实例快照)

```
$ docker export d269 > myapp_v1.tar
```


导入镜像快照

```
$ cat myapp_v1.tar | docker import - my-app:v2

sha256:5eecd9ae9603a313f9d6b828b8f6b462b1a48c76c92b34cecfa59f0ce722490f
```


导出镜像

```
$ docker save my-app:v1 > myapp.tar
```

导入镜像

```
$ docker load < myapp.tar 
```




```
docker build -t friendlyname .  # Create image using this directory's Dockerfile
docker run -p 4000:80 friendlyname  # Run "friendlyname" mapping port 4000 to 80
docker run -d -p 4000:80 friendlyname         # Same thing, but in detached mode
docker container ls                                # List all running containers
docker container ls -a             # List all containers, even those not running
docker container stop <hash>           # Gracefully stop the specified container
docker container kill <hash>         # Force shutdown of the specified container
docker container rm <hash>        # Remove specified container from this machine
docker container rm $(docker container ls -a -q)         # Remove all containers
docker image ls -a                             # List all images on this machine
docker image rm <image id>            # Remove specified image from this machine
docker image rm $(docker image ls -a -q)   # Remove all images from this machine
docker login             # Log in this CLI session using your Docker credentials
docker tag <image> username/repository:tag  # Tag <image> for upload to registry
docker push username/repository:tag            # Upload tagged image to registry
docker run username/repository:tag                   # Run image from a registry
```




save只能对image用，产生的文件需要用load来生成image；
export的对象是container，产生的文件需要用import来生成image。





```
# 基础镜像信息
From registry.cn-hangzhou.aliyuncs.com/sessionboy/node:7.5

# 维护者信息
MAINTAINER sessionboy <postmaster@boyagirl.com>

# 镜像操作指令
COPY ./ /sinn-server
WORKDIR /sinn-server
RUN npm install
EXPOSE 8080

# 容器启动时执行的指令
ENTRYPOINT ["node","bin/run"]
```

```
# 使用From指令指定基础镜像为registry.cn-hangzhou.aliyuncs.com/sessionboy/node7.5
From registry.cn-hangzhou.aliyuncs.com/sessionboy/node:7.5
# 使用MAINTAINER指令描述维护者信息
MAINTAINER sessionboy https://github.com/sessionboy/sinn-server
# 镜像操作指令
COPY ./ /sinn-server   // 把“./”(当前目录)下的所有文件拷贝到容器内的“/sinn-server”目录
WORKDIR /sinn-server   // 指定RUN、ENTRYPOINT、CMD指令的工作目录(容器内)
RUN npm install    // 使用RUN指令，指定执行"npm install"命令
EXPOSE 8080        // 对容器外暴露8080端口
# 容器启动时执行的指令
ENTRYPOINT ["node","bin/run"]  // 指定容器启动时执行“node bin/run”命令，启动node应用
```








## docker-compose


安装
sudo pip install docker-compose

编译启动
docker-compose up -d

docker-compose -f stack.yml up -d


列出所有
docker-compose ps





























