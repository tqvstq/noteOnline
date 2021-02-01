# Nginx

## Nginx 介绍



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



## Nginx配置负载均衡