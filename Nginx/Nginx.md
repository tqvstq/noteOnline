# Nginx

## Nginx 介绍

> Nginx是俄罗斯人编写的一款高性能的HTTP和反向代理服务器，在高连接并发的情况下，它能够支持高达5W个并发连接数的响应，但是内存、CPU等系统资源消耗却很低，运行很稳定。

###  Nginx 在架构中作用

- 路由功能：域名/路径，进行路由转发至后台服务器

- 负载功能: 对后台集群进行负载

- 作为静态资源服务器: 在mvvm模式中，充当文件读取职责 

  注: Nginx传输大文件的时候性能会大大降低，避免大文件走Nginx，使用文件服务器

###  正向代理与反向代理
>   **正向代理** : 即代理客户端请求

>  **反向代理** : 即客户端的代理

###  Nginx模型概念
 #### Nginx进程
Nginx会按需同时运行多个进程，一个主进程(master)和几个工作进程(worker)，配置了缓存时还会有缓存加载器进程(cache loader)和缓存管理器进程(cache manager)等。

所有进程均是仅含有一个线程，并主要通过“共享内存”的机制实现进程间通信。

主进程以root用户身份运行，而worker、cache loader和cache manager均应以非特权用户身份（user配置项）运行。

#####  主进程(master)主要工作
1. 读取并验正配置信息
2. 创建、绑定及关闭套接字
3. 启动、终止及维护worker进程的个数
4. 无须中止服务而重新配置工作特性
5. 重新打开日志文件
#####  工作进程(worker)主要工作
1. 接收、传入并处理来自客户端的连接
2. 提供反向代理及过滤功能
3. nginx任何能完成的其它任务

## Nginx 安装及部署

### 下载Nginx 安装包 [传送]( https://nginx.org/en/download.html )

### 安装Nginx 
**本文是在 Ubuntu18.04上安装Nginx的过程**

####  安装依赖库
依赖包安装顺序依次为：openssl、zlib、pcre, 最后安装Nginx包

#####  更新软件数据源

```
sudo apt-get update
```

#####  安装c++依赖库

注意:**需要输入一个y**

```
sudo apt-get install build-essential
sudo apt-get install libtool
```
#####  安装openssl依赖库

```
sudo apt-get install openssl
```

#####  安装pcre依赖库

注意:**需要输入一个y**

```
sudo apt-get install libpcre3 libpcre3-dev
```

#####  安装zlib依赖库

```
sudo apt-get install zlib1g-dev 
```

###  编译安装Nginx 

####   上传Nginx安装包到服务器

![上传到服务器](./Nginx资料/上传到服务器.png)

####   解压并进入目录

```
tar -zxvf  nginx-1.16.1.tar.gz
cd nginx-1.16.1
```

####  使用configure命令 将nginx安装到/opt/nginx目录

```
./configure --prefix=/opt/nginx
```
####  编译
如果编译异常可以参考这篇博客[传送]( https://blog.csdn.net/u010889616/article/details/82867091 )
```
make
```

####  安装

```
make install
```

####  主要目录结构

- Conf       配置文件
- Html       网页文件
- Logs       日志文件
- sbin      二进制可执行程序

  


#### 测试是否安装成功 启动服务

启动默认监听80端口 如果占用需要去 /opt/nginx/conf/nginx.conf  修改 listen 为你的端口

启动服务

```
/opt/nginx/sbin/nginx
```
查看进程  访问ip:端口 能够看到欢迎页面 表示安装成功

```
ps -ef | grep nginx
```

![Nginx欢迎页面](./Nginx资料/Nginx欢迎页面.png)

### Nginx常用命令
启动命令

```
/opt/nginx/sbin/nginx
```
-c 接配置地址可以指定配置启动

```
/opt/nginx/sbin/nginx -c /opt/nginx/conf/nginx.conf
```
停止服务

```
/opt/nginx/sbin/nginx  -s stop
```
重启重新加载配置(用户几乎无感)

 **要所有链接都断开后，配置才会生效**  **不会强制结束正在工作的连接,需要等所有连接都结束才会重启** 

```
/opt/nginx/sbin/nginx  -s reload
```
测试配置文件是否正确

```
/opt/nginx/sbin/nginx -t
```

## Nginx配置

###　 Nginx配置详解
`user`：指定Nginx的worker进程运行用户以及用户组，默认由nobody账号运行。
`worker_processes`：指定Nginx要开启的进程数，通常开启数量为服务器核心数。
`worker_rlimit_nofile`：worker进程的最大打开文件数限制。
`error_log`：错误日志存放位置及打印日志级别，日志输出级别有debug，info，notice，warn，error，crit 可供选择，其中debug输出日志最为详细，面crit（严重）输出日志最少。默认是error。
`pid`：记录线程启动时的ｉｄ号。
`events`：设定nginx的工作模式及连接数上限。

- 参数use用来指定nginx的工作模式（这里是epoll，epoll是多路复用IO(I/O Multiplexing)中的一种方式）,nginx支持的工作模式有select ,poll,kqueue,epoll,rtsig,/dev/poll。其中select和poll都是标准的工作模式，kqueue和epoll是高效的工作模式，对于linux系统，epoll是首选。worker_connection是设置nginx每个进程最大的连接数，默认是1024，所以nginx最大的连接数max_client=worker_processes，进程最大连接数受到系统最大打开文件数的限制。

`http`：**nginx对http服务器相关属性的设置**

- `include`：把部分设置放在别的地方，然后在包含进来，保持主配置文件的简洁。

- `default_type`：文件类型，默认使用application/octet-stream。

- `log_format`：设置nginx日志的格式，参数解析如下。

  | 参数                  | 意义                                                 |
  | --------------------- | ---------------------------------------------------- |
  | $remote_addr          | 客户端的ip地址(代理服务器，显示代理服务ip)           |
  | $remote_user          | 用于记录远程客户端的用户名称（一般为“-”）            |
  | $time_local           | 用于记录访问时间和时区                               |
  | $request              | 用于记录请求的url以及请求方法                        |
  | $status               | 响应状态码，例如：200成功、404页面找不到等。         |
  | $body_bytes_sent      | 给客户端发送的文件主体内容字节数                     |
  | $http_user_agent      | 用户所使用的代理（一般为浏览器）                     |
  | $http_x_forwarded_for | 可以记录客户端IP，通过代理服务器来记录客户端的ip地址 |
  | $http_referer         | 可以记录用户是从哪个链接访问过来的                   |
  
- `access_log`：指定nginx日志的存放地点和使用模版。

- `sendfile`：是否开启高效文件传输模式（zero copy 方式），避免内核缓冲区数据和用户缓冲区数据之间的拷贝。

- `tcp_nopush`：开启TCP_NOPUSH套接字（sendfile开启时有用）。

- `keepalive_timeout`：客户端连接超时时间。

- `gzip`：是否开启gzip模块，开启后会压缩。

`server`：**虚拟主机的配置**

- `listen`：虚拟主机监听服务端口。

- `server_name`：用来指定ip或者域名，多个域名用逗号分开。

- `location`：#地址匹配设置，支持正则匹配，也支持条件匹配。

  




## Nginx配置负载均衡