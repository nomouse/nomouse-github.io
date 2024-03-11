
# 概述

## 准备机器

192.167.2.33

ssh root@192.167.2.31
ssh root@192.167.2.32
ssh root@192.167.2.33

## 安全设置
swapoff -a && sed -i '/ swap / s/^/#/' /etc/fstab
systemctl stop firewalld # 关闭防火墙服务
service firewalld stop # 关闭防火墙服务
systemctl disable firewalld
setenforce 0 # 关闭SELinux强制模式

## 网络设置

vi /etc/hosts

--------------------------------------------
192.167.2.31 k8s-node1
192.167.2.32 k8s-node2
192.167.2.33 k8s-node3
--------------------------------------------



## 开启转发

--------------------------------------------
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF


modprobe overlay
modprobe br_netfilter

lsmod | grep br_netfilter

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
  net.bridge.bridge-nf-call-iptables  = 1
  net.bridge.bridge-nf-call-ip6tables = 1
  net.ipv4.ip_forward                 = 1
EOF

sysctl --system

# 安装

##

## 阿里云安装
https://developer.aliyun.com/mirror/kubernetes?spm=a2c6h.13651102.0.0.44b31b11bHlXPh

执行命令
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
yum install -y kubelet kubeadm kubectl
systemctl enable kubelet && systemctl start kubelet


## 安装依赖
yum install wget

wget -O /etc/yum.repos.d/docker-ce.repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo


# containerd安装配置
## 安装
yum -y install containerd.io

rpm -qa | grep containerd

systemctl enable containerd
systemctl start containerd

systemctl status containerd
## 配置
### 生成配置文件
containerd config default > /etc/containerd/config.toml
vi /etc/containerd/config.toml

--------------------------------------------

[plugins."io.containerd.grpc.v1.cri".registry]
config_path = "/etc/containerd/cert.d"

--------------------------------------------

mkdir -p /etc/containerd/cert.d

###
crictl config runtime-endpoint unix:///var/run/containerd/containerd.sock
crictl config image-endpoint unix:///var/run/containerd/containerd.sock

## 命令

### 切换namespace
ctr ns ls

### 查看镜像
crictl images list
ctr images list

ctr -n k8s.io images ls

### 拉取镜像
ctr -n k8s.io images pull registry.aliyuncs.com/google_containers/pause:3.6

### 打tag
ctr -n k8s.io images tag registry.aliyuncs.com/google_containers/pause:3.6 registry.k8s.io/pause:3.6

### 推送到远程
ctr -n k8s.io images push

# helm安装
## 



# kubeadm集群初始化

## kubeadm
kubeadm config images pull

kubeadm init --config kubeadm.yml 

kubeadm init --config --v=6 kubeadm.yml 


## 重新初始化
kubeadm reset

## 查看节点
kubectl get node

# k8s安装插件

## 网络插件
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
kubectl delete -f kube-flannel.yml
kubectl apply -f kube-flannel.yml 

## 初始化
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

## 加入
kubeadm join 192.167.2.31:6443 --token abcdef.0123456789abcdef --discovery-token-ca-cert-hash sha256:a647156f3a6d14d188fbf674b4daab055853dea0dabf3acd5828700053bde032

## 查看
kubectl get nodes
kubectl get pod -n kube-system -o wide

# 部署dashboard
## 

# k8s命令
## 查看版本
kubectl version 


#