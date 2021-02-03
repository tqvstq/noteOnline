# Nginx实现高性能集群

## 集群原因
> 单机可同时支持3万并发，现在很多系统基于分布式的，把NGINX作为网关入口来统一调度分配后端资源。但是如果NGINX宕机了,就会导致后台服务无法使用,当并发达到更大时一台Nginx就先显得不够用了,目前,Nginx在这两块主要有以下几种解决方案：
>  - Nginx+LVS+keepalived
>  - 使用域名配置多台公网的Nginx分摊压力



## Nginx+LVS+keepalived 实现高性能负载均衡集群

###  LVS

>LVS是一个开源的软件，可以实现传输层四层负载均衡。
>
>LVS是Linux Virtual Server的缩写，意思是Linux虚拟服务器。
>
>目前有三种IP负载均衡技术（VS/NAT、VS/TUN和VS/DR）
>
>- VS/NAT:
>- VS/TUN:
>- VS/DR:
>
>八种调度算法（rr,wrr,lc,wlc,lblc,lblcr,dh,sh）
>
>- rr:
>- wrr:
>- lc:
>- wlc:
>- lblc:
>- lblcr:
>- dh:
>- sh:

####  LVS开启




###  Keepalived

>Keepalived是一款高可用软件，一开始的定位是和LVS负载均衡配合，依靠Keepalived的配置文件自动创建LVS所需要的ipvsadm规则。但现在Keepalived已经基本用于为其它服务提供高可用，它除了提供VIP自动漂移，还支持对后端后端进行健康检查，当主服务器工作出现故障时会将其剔除，并将备用服务器上线；当主服务器修复后，又会自动将备用服务器下线，让主服务器上线。这些工作全部自动完成，不需要人工干涉，需要人工做的只是修复故障服务器。

####  Keepalived安装

#####   Keepalived 下载[传送门]( https://www.keepalived.org/download.html )

#####   Keepalived 上传解压

#####   Keepalived 安装 openssl和popt 未安装会出异常

```
sudo apt-get install openssl
sudo apt-get install popt
```
#####   配置
进入解压目录
```
cd keepalived-1.2.18/ && ./configure --prefix=/usr/local/keepalived
```
#####   编译安装
进入解压目录
```
make && make install
```
#####   keepalived安装成为系统服务
未使用keepalived默认安装路径(/usr/local)安装完成后需要修改


1. 创建文件夹，将keepalived配置文件进行复制
```
cp /usr/local/keepalived/etc/keepalived/keepalived.conf /etc/keepalived/
```
2. 复制keepalived脚本文件
```
cp /usr/local/keepalived/etc/rc.d/init.d/keepalived /etc/init.d/
cp /usr/local/keepalived/etc/sysconfig/keepalived /etc/sysconfig/
ln -s /usr/local/sbin/keepalived /usr/sbin/
ln -s /usr/local/keepalived/sbin/keepalived /sbin/
```
3. 设置开机启动
```
chkconfig keepalived on
```
#####   keepalived 常用命令
- 启动
```
service keepalived start
```
-  查看状态
```
service keepalived status
```
-  重启
```
service keepalived restart
```
-  停止
```
service keepalived stop
```

#####   keepalived虚拟VIP
阿里云,腾讯云等云服务器不支持此功能





## Nginx+LVS+keepalived 实现高性能负载均衡集群