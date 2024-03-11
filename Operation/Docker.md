# 概述
## 查看详情
docker info

## 更改镜像地址
vi /etc/docker/daemon.json

--------------------------------------------
{
"registry-mirrors": [
"http://hub-mirror.c.163.com",
"https://docker.mirrors.ustc.edu.cn",
"https://registry.docker-cn.com"
]
}
--------------------------------------------

## 查看docker进程
docker ps

## 拉取远程镜像
docker pull nginx
docker pull registry.example.com/nginx
docker pull registry.example.com/nginx nginx:latest

## 查看本地镜像
docker images
拿到镜像的唯一code。

## 本地镜像添加新标签
docker tag <local_image_id> <remote_registry>/<image_name>:<tag>

## 本地镜像推送到远程仓库
docker push <remote_registry>/<image_name>:<tag>


## 打包本地镜像
vi Dockerfile 

--------------------------------------------
FROM node:16.16.0

RUN npm install -g pnpm

--------------------------------------------

docker docker build -t node_pnpm:16.16.0 .

## 本地运行镜像

docker run -it name my node_pnpm:16.16.0 
docker exec -it my /bin/bash

# docker compose
## 安装
docker-compose version



## 查看
docker-compose ps

## 启动
docker-compose up
docker-compose restart


# docker system
## docker 清理磁盘
docker system prune


docker volume prune