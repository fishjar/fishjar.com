---
layout: post
title:  "Docker笔记"
tags:
  - Docker
---


# Docker笔记



Dockerfile
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
p$ docker run --name my-app -d -p 9001:80 my-app:v1

d269856a8607a107c8b75624b3bc04e2ea5804b52cf681be3c142af04ab54454
```

列出容器(镜像实例)

```
$ docker ps -a
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




