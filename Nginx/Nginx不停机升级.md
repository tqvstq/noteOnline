# Nginx不停机升级
**升级版本跨度请勿太大，容易造成服务的崩溃**

## Nginx 升级原因

> 项目被扫描出安全漏洞，要不使用补丁包，要不更新程序版本，我们选择更新Nginx的版本。

## Nginx 信号简介

###   **主进程（master）支持的信号** 
- `TERM`, `INT`: 立刻退出
- `QUIT`: 等待工作进程结束后再退出
- `KILL`: 强制终止进程
- `HUP`: 重新加载配置文件，使用新的配置启动工作进程，并逐步关闭旧进程。
- `USR1`: 重新打开日志文件
- `USR2`: 启动新的主进程，实现热升级
- `WINCH`: 逐步关闭工作进程
###   **工作进程（work）支持的信号** 
- `TERM`, `INT`: 立刻退出
- `QUIT`: 等待请求处理结束后再退出
- `USR1`: 重新打开日志文件

## Nginx 平滑升级

### 下载Nginx 安装包 [传送]( https://nginx.org/en/download.html )

### 上传新版包到服务器
将下载好的包上传至服务器并解压

### 查看服务器当前Nginx版本
```
/opt/nginx/sbin/nginx -v
```

### 配置和编译

**注：千万不要安装，会覆盖原来的文件可能导致异常**

进入解压出来的文件夹，按照原来的编译参数进行编译

```
./configure --prefix=/opt/nginx && make
```
### 备份旧的启动程序
进入解压出来的文件夹，按照原来的编译参数进行编译
```
mv /opt/nginx/sbin/nginx    /opt/nginx/sbin/nginx.bak
```

### 替换旧的启动程序
将新为启动程序放到旧版的启动程序下
```
cp /tmp/nginx-1.18.0/objs/nginx    /opt/nginx/sbin/nginx
```

### 测试配置是否正常
```
/opt/nginx/sbin/nginx  -t
```
### 向Nginx发送平滑升级信号

```
kill -USR2  nginx主进程id
```
**注解：**如果开启了pid 记录 将会在日志中生成 nginx.pid.oldbin存放旧的pid 

### 查看服务是否启动成功 并检查外网是否正常访问



###  查看Nginx版本
```
/opt/nginx/sbin/nginx -v
```

