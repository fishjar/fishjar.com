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

运行镜像（创建并运行容器）

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





## 构建image
docker build -t <friendlyhello> .




## 创建虚拟机
docker-machine create --driver virtualbox myvm1
docker-machine create --driver virtualbox myvm1



## 查看虚拟机
docker-machine ls
NAME    ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
myvm1   -        virtualbox   Running   tcp://192.168.99.100:2376           v17.12.0-ce   
myvm2   -        virtualbox   Running   tcp://192.168.99.101:2376           v17.12.0-ce 


## 查看节点列表
docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS
aghuri48m990h2400jscx6u8s *   gabe-desktop        Ready               Active              Leader
z8h9uysgv9xi65hirz352b9ie     worker1             Down                Active              
yqnmc4e8ei6ci3ha466gis0kf     worker2             Down                Active    

## 创建swarm
docker swarm init

## 登录虚拟机并初始化 

docker-machine ssh myvm1 "docker swarm init --advertise-addr 192.168.99.100"
docker-machine ssh myvm1 "docker swarm init --advertise-addr 192.168.99.100:2376"

Swarm initialized: current node (7x7elefbphwfn0icjs5f4cq3d) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-2itze37brrcof7sevmpiq91rpgi0r4muqqnv9lpr2cntay05lj-2vlg4uziti5yjk7lroxznrzwc 192.168.99.100:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

### 119
docker swarm join --token SWMTKN-1-2znbuidmztgk6vh5uw6krfb1sw7oobuu09qxqobk5qxzkzyyan-ds30v3mheo85sz7bxizfbicsw 10.144.100.136:2377

## 登录虚拟机并加入 docker swarm
https://github.com/docker/machine/issues/4064

### Ports 2377 and 2376

Always run docker swarm init and docker swarm join with port 2377 (the swarm management port), or no port at all and let it take the default.

The machine IP addresses returned by docker-machine ls include port 2376, which is the Docker daemon port. Do not use this port or you may experience errors.


docker-machine ssh myvm2 "docker swarm join --token SWMTKN-1-2itze37brrcof7sevmpiq91rpgi0r4muqqnv9lpr2cntay05lj-2vlg4uziti5yjk7lroxznrzwc 192.168.99.100:2377"

docker-machine ssh myvm2 "docker swarm join --token SWMTKN-1-06lta48184qbqnvaw4xmeaegopqc7scrz05xbh2pkl7vwf32l8-b33u1bqj1233akabgpl90qptw 192.168.99.100:2377"





## 登录虚拟机并列出节点
docker-machine ssh myvm1 "docker node ls"

ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS
7x7elefbphwfn0icjs5f4cq3d *   myvm1               Ready               Active              Leader
s368e1xkmxln4a87vxq25xfa5     myvm2               Ready               Active         


## 离开swarm
docker swarm leave

## 查看虚拟机环境变量
docker-machine env myvm1

export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.100:2376"
export DOCKER_CERT_PATH="/home/gabe/.docker/machine/machines/myvm1"
export DOCKER_MACHINE_NAME="myvm1"
# Run this command to configure your shell: 
# eval $(docker-machine env myvm1)


## 添加某个虚拟机环境变量到当前环境（相当于管理该虚拟机，在改虚拟机运行docker命令）
eval $(docker-machine env myvm1)


## 列出虚拟机管理的节点
docker-machine ls
NAME    ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
myvm1   *        virtualbox   Running   tcp://192.168.99.100:2376           v17.12.0-ce   
myvm2   -        virtualbox   Running   tcp://192.168.99.101:2376           v17.12.0-ce 


## 查看当前docker 环境变量
env | grep DOCKER

DOCKER_HOST=tcp://192.168.99.101:2376
DOCKER_MACHINE_NAME=worker2
DOCKER_TLS_VERIFY=1
DOCKER_CERT_PATH=/home/gabe/.docker/machine/machines/worker2


## 启动并部署容器到当前管理的节点，创建分布式网络
docker stack deploy -c docker-compose.yml getstartedlab

Creating network getstartedlab_webnet
Creating service getstartedlab_web



## 查看网络节点（实例）信息
docker stack ps getstartedlab

ID                  NAME                  IMAGE               NODE                DESIRED STATE       CURRENT STATE             ERROR               PORTS
xsqtdaewkfvd        getstartedlab_web.1   nginx:latest        myvm2               Running             Preparing 8 seconds ago                       
kxd9nauucu0m        getstartedlab_web.2   nginx:latest        myvm1               Running             Preparing 8 seconds ago                       
0e00ragqd86w        getstartedlab_web.3   nginx:latest        myvm2               Running             Preparing 8 seconds ago                       
eycvacm1p7xl        getstartedlab_web.4   nginx:latest        myvm1               Running             Preparing 8 seconds ago                       
bg4ym57b4ltb        getstartedlab_web.5   nginx:latest        myvm2               Running             Preparing 8 seconds ago


## 清除swarm
docker stack rm getstartedlab


## 删掉节点
docker-machine ssh myvm2 "docker swarm leave"

## 删掉主节点
docker-machine ssh myvm1 "docker swarm leave --force"


## 清除当前docker 环境变量，相当于转换虚拟机控制权
eval "$(docker-machine env -u)"

## 重启系统后虚拟机需要重启
docker-machine start <machine-name>




















## 2222222222

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


## 33333333333

docker stack ls                                            # List stacks or apps
docker stack deploy -c <composefile> <appname>  # Run the specified Compose file
docker service ls                 # List running services associated with an app
docker service ps <service>                  # List tasks associated with an app
docker inspect <task or container>                   # Inspect task or container
docker container ls -q                                      # List container IDs
docker stack rm <appname>                             # Tear down an application
docker swarm leave --force      # Take down a single node swarm from the manager


## 444444444444444

docker-machine create --driver virtualbox myvm1 # Create a VM (Mac, Win7, Linux)
docker-machine create -d hyperv --hyperv-virtual-switch "myswitch" myvm1 # Win10
docker-machine env myvm1                # View basic information about your node
docker-machine ssh myvm1 "docker node ls"         # List the nodes in your swarm
docker-machine ssh myvm1 "docker node inspect <node ID>"        # Inspect a node
docker-machine ssh myvm1 "docker swarm join-token -q worker"   # View join token
docker-machine ssh myvm1   # Open an SSH session with the VM; type "exit" to end
docker node ls                # View nodes in swarm (while logged on to manager)
docker-machine ssh myvm2 "docker swarm leave"  # Make the worker leave the swarm
docker-machine ssh myvm1 "docker swarm leave -f" # Make master leave, kill swarm
docker-machine ls # list VMs, asterisk shows which VM this shell is talking to
docker-machine start myvm1            # Start a VM that is currently not running
docker-machine env myvm1      # show environment variables and command for myvm1
eval $(docker-machine env myvm1)         # Mac command to connect shell to myvm1
& "C:\Program Files\Docker\Docker\Resources\bin\docker-machine.exe" env myvm1 | Invoke-Expression   # Windows command to connect shell to myvm1
docker stack deploy -c <file> <app>  # Deploy an app; command shell must be set to talk to manager (myvm1), uses local Compose file
docker-machine scp docker-compose.yml myvm1:~ # Copy file to node's home dir (only required if you use ssh to connect to manager and deploy the app)
docker-machine ssh myvm1 "docker stack deploy -c <file> <app>"   # Deploy an app using ssh (you must have first copied the Compose file to myvm1)
eval $(docker-machine env -u)     # Disconnect shell from VMs, use native docker
docker-machine stop $(docker-machine ls -q)               # Stop all running VMs
docker-machine rm $(docker-machine ls -q) # Delete all VMs and their disk images





docker-machine -> 

docker swarm -> docker node

docker stack deploy -> docker sevice

docker stack ps -> docker container
