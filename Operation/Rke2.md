# 环境准备
## 资源列表
192.167.2.50    k8sswarmnode1
192.167.2.44    k8sswarmnode2
192.167.2.59    k8sswarmnode3
## 网络
Node网段	     192.168.111.0/24
Service网段	10.43.0.0/16
Pod网段	    10.42.0.0/16

# 初始化环境
## 设置主机名
hostnamectl set-hostname server1 && hostname # 在主节点1设置
hostnamectl set-hostname server2 && hostname # 在主节点2设置
hostnamectl set-hostname server3 && hostname # 在主节点3设置

## 设置各节点host文件

vi /etc/hosts

文件内容：
192.167.2.50 www.rke2.server1.com server1
192.167.2.44 www.rke2.server2.com server2
192.167.2.59 www.rke2.server3.com server3


## 关闭各节点的防火墙
systemctl disable nftables.service 
systemctl stop nftables.service
systemctl status nftables.service

## 永久禁用各节点的交换分区
swapoff -a && sed -i 's/.*swap.*/#&/' /etc/fstab

## 配置 containerd 内核模块
### 转发 IPv4 并让 iptables 看到桥接流量





systemctl start rke2-server
systemctl enable rke2-server
systemctl status rke2-server -l

# 安装rke2

# 配置rke2
vim /etc/rancher/rke2/config.yaml

### 以下为配置文件内容

server: https://192.167.2.50:9345
token: a815322f-4f68-4f4a-8fa9-eff1c2efe48d
tls-san:
- 192.167.2.50
- k8sswarmnode1

# 网络设置

# 节点设置
node-name: k8sswarmnode1
node-label:
- "role=master"
  system-default-registry: "registry.cn-hangzhou.aliyuncs.com"  #设置阿里云镜像地址，加速下载

# 设置etcd快照策略
etcd-snapshot-retention: 2            
etcd-snapshot-schedule-cron: '*/5 * * * *'

# 自定义垃圾回收机制，添加到所所节点
kubelet-arg:
- "eviction-hard=nodefs.available<1%,memory.available<10Mi"
- "eviction-soft-grace-period=nodefs.available=30s,imagefs.available=30s"
- "eviction-soft=nodefs.available<5%,imagefs.available<1%"

#kube-controller-manager-extra-env:
- "cluster-signing-cert-file=/etc/kubernetes/ssl/kube-ca.pem"
- "cluster-signing-key-file=/etc/kubernetes/ssl/kube-ca-key.pem"

#kube-apiserver-extra-env:
# apiserver启用metadata.selfLink 字段，配置nfs存储要用到
- "feature-gates='RemoveSelfLink=false'"

### 以上为配置文件内容
