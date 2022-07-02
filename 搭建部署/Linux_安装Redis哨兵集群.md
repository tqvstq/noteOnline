

### Linux 安装Redis哨兵集群

1. #### 下载安装包

   去官网下载相应的安装包：[官网下载地址](https://redis.io/download) ，我直接选择最新稳定版安装

![redis 官网](/Users/edy/private/markdown/Linux 安装Redis哨兵集群资料/redis 官网.png)

​       也可以直接用命令下载

```linux
wget https://download.redis.io/redis-stable.tar.gz
```

2. #### 解压压缩包

   直接解压下载下来的文件

```linux
tar -zxvf redis-7.0.2.tar.gz
```

​      解压后如图

![解压redis安装包后](/Users/edy/private/markdown/Linux 安装Redis哨兵集群资料/解压redis安装包后.png)

3. #### 编译文件

​       进入解压出来的文件目录，并执行编译

```linux
cd redis-7.0.2/ & make
```

![开始编译](/Users/edy/private/markdown/Linux 安装Redis哨兵集群资料/开始编译.png)

4. 安装

   编译完成后，解压在当前目录进入`src`目录进行安装

```linux
cd src/ & make install
```
![安装](/Users/edy/private/markdown/Linux 安装Redis哨兵集群资料/安装.png)

   如果权限不足，请使用root 账号，或者使用 sudo 提权命令重新运行命令

![权限不足](/Users/edy/private/markdown/Linux 安装Redis哨兵集群资料/权限不足.png)

5. #### 安装完成

   这里`src`目录里有很多东西，并不方便查找和管理，我们对文件进行一些整理，创建`bin`目录存放命令，创建`etc`目录存放一些配置文件

 ```linux
 mkdir bin etc
 ```
   将`src`文件夹下一些命令复制到`bin`文件夹下

 ```linux
 cp src/{mkreleasehdr.sh,redis-benchmark,redis-check-aof,redis-check-rdb,redis-cli,redis-server,redis-sentinel} /home/ubuntu/redis/redis-7.0.2/bin/
 ```

   将安装包下的redis配置文件和哨兵配置文件放到`etc`目录

```
cp {redis.conf,sentinel.conf} /home/ubuntu/redis/redis-7.0.2/etc/
```

6. #### redis 配置介绍

 我们安装完后后，可以在安装目录下，看见redis配置文件 redis.conf 和redis 哨兵配置文件 sentinel.conf

由于本次集群是在单机上用不同端口搭建的，所以将此配置文件复制三份，用于redis 指定配置文件启动。

主节点配置，将redis.conf 复制一份，命名为 redis-6379.conf  修改其中配置内容

```
# 允许主机地址，以下这种是没有限制的配置，默认只有本地可以访问，此处我改为没有限制
bind * -::*
# redis 使用端口，默认6379
port 6379
# 是否开启保护模式，如果设置了密码和设置了允许访问范围，建议开启，我为了省事关闭了
protected-mode no
# 是否后台运行
daemonize yes
# 日志输出地址和文件名称
logfile ./6739redis.log
# 设置redis连接密码
requirepass 123456
# 从节点连接主节点密码，由于是哨兵所以都配置上一样的密码
masterauth 123456
# 指定进程的PID文件位置
pidfile /var/run/redis_6379.pid

其他的可以不做更改，具体的可以看配置文件里的介绍
```

从节点配置，将redis.conf 复制一份，命名为 redis-6380.conf 和 redis-6381.conf  修改其中配置内容

```
# 允许主机地址，以下这种是没有限制的配置，默认只有本地可以访问，此处我改为没有限制
bind * -::*
# redis 使用端口，默认6379，从节点分别相应使用 6380 和 6381，以下6379相应修改为对应端口配置
port 6380
# 是否开启保护模式，如果设置了密码和设置了允许访问范围，建议开启，我为了省事关闭了
protected-mode no
# 是否后台运行
daemonize yes
# 日志输出地址和文件名称
logfile ./6380redis.log
# 设置redis连接密码
requirepass 123456
# 从节点连接主节点密码，由于是哨兵所以都配置上一样的密码
masterauth 123456
# 指定进程的PID文件位置
pidfile /var/run/redis_6380.pid
# 主节点的IP和端口号，如果云主机，如果要外网访问，这里IP为外网IP，注意老版本的 slaveof 新版本为 replicaof
replicaof 192.168.8.110 6379
```

![redis 配置](/Users/edy/private/markdown/Linux 安装Redis哨兵集群资料/redis 配置.png)

7. #### 启动集群

使用命令启动集群

```linux
redis-server /home/ubuntu/redis/redis-7.0.2/etc/redis-6379.conf &
redis-server /home/ubuntu/redis/redis-7.0.2/etc/redis-6380.conf &
redis-server /home/ubuntu/redis/redis-7.0.2/etc/redis-6381.conf &
```

如果启动不成功，可能是防火墙问题，可以telnet 一下地址，如果不通记得开放响应端口

8. #### 哨兵配置

将sentinel.conf 复制三份，分别命名为 sentinel-26379.conf 和 sentinel-26380.conf  以及 sentinel-26381.conf  修改其中配置内容

```
# 哨兵使用端口，默认26379，按不同配置修改为不同的端口
port 26379
# 是否后台运行
daemonize yse
# 日志输出地址和文件名称
logfile ./sentinel26379.log
# 指定主机IP地址和端口，并指定当有2台哨兵认为主机挂了，则对主机进行容灾切换，注意如果是外网访问，请用外网IP
sentinel monitor mymaster 192.168.8.110 6379 2
# redis 认证密码
sentinel auth-pass mymaster 123456
# 设置主机无响应时间，如果达到这个时间还没响应则认为主机宕机
sentinel down-after-milliseconds mymaster 5000
# 主从切换时，最多有多少个从节点，同时对新的主节点进行同步，此处我设置为1
sentinel parallel-syncs mymaster 1
# 故障转移的超时时间，此处设置为三分钟
sentinel failover-timeout mymaster 180000

其他的可以不做更改，具体的可以看配置文件里的介绍，注意此时使用的是单机不同端口搭建的，修改配置文件，记得修改为响应端口配置
```

![哨兵配置](/Users/edy/private/markdown/Linux 安装Redis哨兵集群资料/哨兵配置.png)



8. #### 启动哨兵

```linux
   redis-sentinel /home/ubuntu/redis/redis-7.0.2/etc/sentinel-26379.conf &
   redis-sentinel /home/ubuntu/redis/redis-7.0.2/etc/sentinel-26380.conf &
   redis-sentinel /home/ubuntu/redis/redis-7.0.2/etc/sentinel-26381.conf &
```



9. #### SpringBoot yml连接配置

```yml
redis:
  sentinel:
    #哨兵监听的master名称
    master: mymaster
    password: 123456
    #哨兵地址列表，多个以,分割
    nodes: 192.168.8.110:26379,192.168.8.110:26380,192.168.8.110:26381
```

