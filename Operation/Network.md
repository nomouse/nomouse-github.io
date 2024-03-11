

# 网络相关
## 查看端口
netstat -tln | grep 22


## 查看网卡
ifconfig -a

# 配置文件
## 网络配置
/ect/systemconfig/network-script/eth0

BOOTPROTO=static
ONBOOT=yes

IPADDR=192.167.2.31
NETMASK=255.255.255.0
GATEWAY=192.167.2.1
DNS1=192.168.12.232
DNS2=114.114.114.114
