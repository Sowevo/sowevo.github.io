---
title: Docker进阶
date: 2021-02-05 17:08:40
updated: 2021-02-05 17:08:40
tags:
  - Linux
  - Docker
---
![img](https://up.sowevo.com/img/20201224162029.png)

# Docker Compose

## 简介

Docker

DockerFile build run 手动操作,单个容器!

微服务,服务多,相互依赖

使用Docker Compose来高效管理,定义多个容器

- 官方介绍

  定义运行多个容器

  使用yaml配置文件

  所有环境都可以使用
<!-- more -->
==就是批量容器编排==

## 安装

```shell
# 要去git上确认下最新的版本
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
# 加上权限
$ sudo chmod +x /usr/local/bin/docker-compose
```

## 初体验!

官方的例子

1. 创建文件夹

   ```shell
   $ mkdir composetest
   $ cd composetest
   ```

2. 新建一个`app.py`文件

   ```python
   import time
   
   import redis
   from flask import Flask
   
   app = Flask(__name__)
   cache = redis.Redis(host='redis', port=6379)
   
   def get_hit_count():
       retries = 5
       while True:
           try:
               return cache.incr('hits')
           except redis.exceptions.ConnectionError as exc:
               if retries == 0:
                   raise exc
               retries -= 1
               time.sleep(0.5)
   
   @app.route('/')
   def hello():
       count = get_hit_count()
       return 'Hello World! I have been seen {} times.\n'.format(count)
   ```

3. 新建一个`requirements.txt`文件

   ```
   flask
   redis
   ```

4. 创建`Dockerfile`文件

   ```dockerfile
   FROM python:3.7-alpine       # 从Python3.7映像开始构建
   WORKDIR /code                # 将工作目录设置为/code
   ENV FLASK_APP=app.py         # 设置flask使用的环境变量
   ENV FLASK_RUN_HOST=0.0.0.0   
   RUN apk add --no-cache gcc musl-dev linux-headers # 安装gcc和其他依赖项
   COPY requirements.txt requirements.txt
   RUN pip install -r requirements.txt               # 使用requirements安装依赖
   EXPOSE 5000                                       # 监听5000端口
   COPY . .
   CMD ["flask", "run"]                              # 执行flask命令
   ```

5. 创建`docker-compose.yml`文件

   ```yaml
   version: "3.9"
   services:
     web:
       build: .
       ports:
         - "5000:5000"
     redis:
       image: "redis:alpine"
   ```

6. 使用compose构建并运行你的app

   ```shell
   # 如果compose文件名不是标准的docker-compose.yml,可以使用-f指定文件
   # docker-compose -f docker-compose-dev.yml up
   $ docker-compose up
   ```

7. 使用compose停止你的app

   ```shell
   $ docker-compose down
   ```

## 都干了些什么

1. 根据Dockerfile构建镜像,下载镜像
2. 容器名称的的处理
3. 新建一个网络给这个`项目`使用

## 配置文件的编写

**[官方文档链接](https://docs.docker.com/compose/compose-file/compose-file-v3/#compose-file-structure-and-examples)**

瞅瞅开源项目的`docker-compose.yml`文件

```yaml
version: "3.9"           # 1.compose版本
#======================================    
services:                # 2.服务
  web:                   #   服务名
    build: .             #   服务构建目录,会在这个目录下找Dockerfile开始构建
    ports:               #   端口映射
      - "5000:5000" 
    depends_on:          #   多个服务依赖的情况,被依赖的会先启动
      - redis            #   web依赖redis
    environment:         #   一些环境变量
      RACK_ENV: development
      SHOW: 'true'  
  redis:
    image: "redis:alpine" # 指定一个镜像
    expose:              # 暴露一些端口
      - "3000"
      - "8000"
#======================================
networks:               # 3.其他配置
  frontend:
  backend:
volumes:
  db-data:    
```

## 再体验-WordPress

部署WordPress

1. 建个文件夹

   ```sh
   $ cd my_wordpress/
   ```

2. 编写`docker-compose.yml`文件

   ```yaml
   version: '3.3'
   
   services:
      db:
        image: mysql:5.7
        volumes:
          - db_data:/var/lib/mysql
        restart: always
        environment:
          MYSQL_ROOT_PASSWORD: somewordpress
          MYSQL_DATABASE: wordpress
          MYSQL_USER: wordpress
          MYSQL_PASSWORD: wordpress
   
      wordpress:
        depends_on:
          - db
        image: wordpress:latest
        ports:
          - "8000:80"
        restart: always
        environment:
          WORDPRESS_DB_HOST: db:3306
          WORDPRESS_DB_USER: wordpress
          WORDPRESS_DB_PASSWORD: wordpress
          WORDPRESS_DB_NAME: wordpress
   volumes:
       db_data: {}
   ```

3. 启动项目

   ```shell
   # 后台那运行
   $ docker-compose up -d
   ```

## 再再体验-SpringBoot

1. SpringBoot写个项目

2. 编写Dockerfile

   ```dockerfile
   FROM java:8
   COPY ./target/*.jar /app.jar
   EXPOSE 8080
   CMD ["--server.port=8080"]
   ENTRYPOINT ["java","-jar","/app.jar"]
   ```

3. 编写`docker-compose.yml`文件

   ```yaml
   version: "3.9"
   services:
     spring-boot:
       build: .
       ports:
       - "8080:8080"
       depends_on:
         - redis
     redis:
       image: redis
   ```

4. 启动

   ```shell
   # 后台运行
   $ docker-compose up -d
   ```


# Docker Swarm

## 搞几台服务器

搞4台(1核2G)

并安装Docker

## 工作模式

![Swarm mode cluster](https://up.sowevo.com/img/20201226145356.png)

### Swarm 两种角色

**Manager**：接收客户端服务定义，将任务发送到worker节点；维护集群期望状态和集群管理功能及Leader选举。默认情况下manager节点也会运行任务，也可以配置只做管理任务。

**Worker**：接收并执行从管理节点分配的任务，并报告任务当前状态，以便管理节点维护每个服务期望状态。

------

## 搭建集群

使用Docker Swarm命令

1. 初始化一个manager节点

   ```shell
   # 初始化一个管理节点
   $ docker swarm init --advertise-addr=172.17.88.237
   Swarm initialized: current node (84jg51t5nr7ywlqku2uwpkcm3) is now a manager.
   
   To add a worker to this swarm, run the following command:
   
       docker swarm join --token SWMTKN-1-55o9kohbport6z73roe1m0532kl55ihc712tt6nrd1mupt0n3q-axx5fxz310o6t237m7dbxvt52 172.17.88.237:2377
   
   To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
   ```

2. manager节点生成token

   ```shell
   # 生成加入工作节点(worker)的token,会直接输出可用的命令,人性化
   $ docker swarm join-token worker 
   To add a worker to this swarm, run the following command:
   
       docker swarm join --token SWMTKN-1-55o9kohbport6z73roe1m0532kl55ihc712tt6nrd1mupt0n3q-axx5fxz310o6t237m7dbxvt52 172.17.88.237:2377
   
   #===#
   
   # 生成加入管理节点(manager)的token,会直接输出可用的命令,人性化
   $ docker swarm join-token manager 
   To add a manager to this swarm, run the following command:
   
       docker swarm join --token SWMTKN-1-55o9kohbport6z73roe1m0532kl55ihc712tt6nrd1mupt0n3q-brfqx08svxov16vbdi4jolpmc 172.17.88.237:2377
   
   
   ```

3. 其他节点加入

   ```shell
   # 根据token的不同注册过去会有不同的权限~
   $ docker swarm join --token SWMTKN-1-55o9kohbport6z73roe1m0532kl55ihc712tt6nrd1mupt0n3q-axx5fxz310o6t237m7dbxvt52 172.17.88.237:2377 --advertise-addr=192.168.32.32
   ```

命令的相关用法

```shell
$ docker swarm COMMAND
#    ca              
#    init                       初始化swarm集群
#          --advertise-addr     <ip|interface>[:port]
#                               广播地址,本机的IP地址
#    join                       加入swarm集群
#    join-token                 (worker|manager)
#                               生成一个加入的令牌,指定令牌的类型,其他机器加入集群时需要
#    leave                      离开swarm集群
#    unlock      
#    unlock-key  
#    update      
```

## Raft协议

Raft协议:保证大多数管理节点(manager)存活,集群才能可用

当只有两台管理节点(manager)时,如果挂掉一个,会导致集群不可用

所以一个高可用的Swarm集群至少要有3台管理节点(manager),一台工作节点(Worker)

## Swarm集群中运行项目

### docker service

- 与docker的区别

  - docker run :容器启动,不具有扩容容器的功能

  - docker service :服务启动,具有扩容功能,滚动更新

- **docker service create**部署服务

  ```shell
  # --name 服务名 -p 端口映射
  # 部署后四个服务的8080端口都可以访问到这个服务...厉害了我的哥
  $ docker service create -p 8080:80 --name my-nginx nginx
  ```

- **docker service ps**查看服务

  ```shell
  # 查看某个服务运行状态
  # docker service ps 服务名
  $ docker service ps my-nginx 
  
  
  # 查看已经在运行的服务
  $ docker service ls
  ```

- **docker service scale**服务扩缩容

  ```shell
  # 将my-nginx服务扩容到3个节点
  $ docker service update --replicas 3 my-nginx
  # 两个命令等价
  $ docker service scale my-nginx=1
  ```

  服务集群中的任意节点都可以访问,有多个副本可以动态伸缩,实现服务的高可用

- **docker service rm**服务删除

  ```shell
  # 讲一个服务删除
  $ docker service rm my-nginx
  ```

## 总结

- **swarm**:集群的管理和编排.Docker开一初始化一个swarm集群,其他节点可以以**Manager**或者**Worker**身份加入进来
- **Node**:就是一个Docker节点,多个节点组成一个集群
- **Services**:任务,可以在管理节点或者工作节点来运行,是核心,用户访问的.
- **task**:容器内的命令

![image-20201226205204563](https://up.sowevo.com/img/20201226205204.png)

命令=>管理=>API=>调度=>工作节点(创建Task容器,维护运行)

# DockerStack

Dockercompose的集群版

```shell
#单机
$ docker-compose up -d workpress.yaml
#集群
$ docker stack deploy workpress.yaml
```

# Docker Secret

安全,配置密码加密证书

# Docker Config

配置
