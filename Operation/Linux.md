# 系统基础

## 关机

shutdown -h now

## 查看已启动服务
systemctl list-units --type=service --state=running

# ssh远程访问

## 基本

登录远程机器 ssh root@192.167.2.50

## 设置公钥免密登录

1. ssh-keygen rsa
2. ssh-copy-id -i .ssh/id_rsa.pub root@192.167.2.50

##  

# 基本管理

## 排查磁盘占用

当前磁盘空间
df -h
当前指定目录空间
du -sh /
当前指定目录下每个文件空间
du -sh ./*
当前路径下所有文件大小
ll -h

# 清理磁盘
## 磁盘空间不足
通常是日志文件过大没有及时清理，从/逐级向下排查至相关目录清除日志即可
df -h
du -sh /
du -sh ./*
rm -rf log.old.*
## 磁盘iNode文件过多
通常是服务器小文件过多没有及时清理，从/逐级向下排查至相关目录清除文件即可
df -i
分析根目录下每个二级目录下的文件数量
for i in /*; do echo $i; find $i | wc -l; done


# 查询文件
## 查询文件
find 目录路径 -name "文件名"
find / -name "nacos"
