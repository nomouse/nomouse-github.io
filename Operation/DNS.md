# 概述

## 


## 常用命令
### 配置本地dns
vi /etc/resolv.conf

### 检测

# 安装

## 安装本体

yum list | grep bind # 查询一下bind的包

-------------------------------
bind
bind-chroot
bind-libs
bind-utils
    host
    dig
    nslookup
    nsupdate
-------------------------------

yum install bind* -y



## 配置服务

-------------------------------------------------------
systemctl enable named  # 设置named(DNS服务)开机时自启动
systemctl start named # 启动named服务
chkconfig named on # 设置named(DNS服务)开机时自启动
service named start  # 启动named服务
-------------------------------------------------------
## 关闭安全和防火墙

--------------------------------------------
systemctl stop firewalld # 关闭防火墙服务
service firewalld stop # 关闭防火墙服务
setenforce 0 # 关闭SELinux强制模式
firewall-cmd --permanent --zone=public --add-service=dns
firewall-cmd --reload
--------------------------------------------
## 重启服务

---------------------------------
systemctl restart named #重启域名服务
service named restart
---------------------------------

# 配置

## 配置文件

1. 主配置文件/etc/named.conf
2. 区域配置/etc/named.rfc1912.zones
3. 区域解析文件/var/named**

## 正向解析和反向解析区域配置

1. 修改主配置文件 
-------------------------------
vi /etc/named.conf # 文件中“127.0.0.1”替换为“any”
-------------------------------
2. 创建正向解析区域和反向解析区域
-------------------------------
vi /etc/named.rfc1912.zones # 文件最后追加
--------------------------------------------
   zone "beicaizs-bj.com" IN {
       type master;
       file "beicaizs-bj.com.zone";
       allow-update { none; }; 
   }; # 

   zone "12.168.192.in-addr.arpa" IN {
       type master;
       file "12.168.192.in-addr.arpa";
       allow-update { none; };
   }; # 文件最后追加

--------------------------------------------   
3. 正向解析配置
--------------------------------------------   
cd /var/named # 将空解析文件模板复制到区域解析文件，同时保留原文件属性  (文件名要和区域配置文件名保持一致)

cp named.localhost beicaizs-bj.com.zone -a # 一定要保持文件原本的权限  也可使用 -a 或 -p (-a包含了-p,-a在保留文件的属性权限不变的同时保留了链接)

vim beicaizs-bj.com.zone

--------------------------------------------   
$TTL 1D
@       IN SOA  beicaizs-bj.com. rname.invalid. (
0       ; serial
1D      ; refresh
1H      ; retry
1W      ; expire
3H )    ; minimum
NS      @
A       127.0.0.1
AAAA    ::1
www     A       192.168.12.232
seafile A       192.168.12.242
dev     A       192.168.12.247
gitlab  A       192.168.12.241
--------------------------------------------   
4. 反向解析配置
--------------------------------------------   
cd /var/named # 将空解析文件模板复制到区域解析文件，同时保留原文件属性  (文件名要和区域配置文件名保持一致)

cp named.loopback 12.168.192.in-addr.arpa -p # 一定要保持文件原本的权限  也可使用 -a 或 -p (-a包含了-p,-a在保留文件的属性权限不变的同时保留了链接)

vim 12.168.192.in-addr.arpa

--------------------------------------------   
$TTL 1D
@       IN SOA  beicaizs-bj.com. rname.invalid. (
0       ; serial
1D      ; refresh
1H      ; retry
1W      ; expire
3H )    ; minimum
NS      beicaizs-bj.com.
A       127.0.0.1
AAAA    ::1
232     PTR     www.beicaizs-bj.com.
--------------------------------------------  


# 验证dns