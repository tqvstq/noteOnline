# Linux_安装MySQL数据库
此处安装基于 ubuntu 18.04 安装

## 安装
- 更新数据源：
```bash
apt-get update
```
- 安装数据库：
```bash
apt-get install mysql-server
```
> **注意：** 系统将提示您在安装过程中创建 root 密码。选择一个安全的密码，并确保你记住它，因为你以后需要它。接下来，我们将完成 MySQL 的配置。

## 配置

> **注意：** 因为是全新安装，您需要运行附带的安全脚本。这会更改一些不太安全的默认选项，例如远程 root 登录和示例用户。在旧版本的 MySQL 上，您需要手动初始化数据目录，但最新的 MySQL 已经自动完成了。

```bash
mysql_secure_installation
```

这将提示您输入您在之前步骤中创建的 root 密码。您可以按 Y，然后 ENTER 接受所有后续问题的默认值，但是要询问您是否要更改 root 密码。您只需在之前步骤中进行设置即可，因此无需现在更改。

## 验证安装是否成功

按上边方式安装完成后，MySQL 应该已经开始自动运行了。要测试它，请检查其状态。

```bash
systemctl status mysql

# 输出如下
● mysql.service - MySQL Community Server
   Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2017-11-21 13:04:34 CST; 3min 24s ago
 Main PID: 2169 (mysqld)
   CGroup: /system.slice/mysql.service
           └─2169 /usr/sbin/mysqld

Nov 21 13:04:33 ubuntu systemd[1]: Starting MySQL Community Server...
Nov 21 13:04:34 ubuntu systemd[1]: Started MySQL Community Server.
```

## 常用命令

- 查看版本：
```bash
mysqladmin -p -u root version
```
- 启动：
```bash
service mysql start
```
- 停止：
```bash
service mysql stop
```
- 重启：
```bash
service mysql restart
```
- 登录：
```bash
mysql -u root -p
```
- 授权：
```bash
grant all privileges on *.* to 'root'@'%' identified by 'Your Password';
```


## 配置允许远程访问

- 修改配置文件

```
vi /etc/mysql/mysql.conf.d/mysqld.cnf
```

- 注释掉(语句前面加上 `#` 即可)：

```
# bind-address = 127.0.0.1
```

- 重启 MySQL

```
service mysql restart
```

- 登录 MySQL

```
mysql -u root -p
```

- 授权 root 用户允许所有人连接

```sql
grant all privileges on *.* to 'root'@'%' identified by 'Your Password';

```

## 配置使用密码方式登录:

在安装过程中可能没有提示设置密码的环节.此时默认使用的是auth_socket方式登录,我们需要修改为

**mysql_native_password**方式,操作步骤如下:

- 本地登录MySQL , 此时无需输入密码:

  > ```mysql
  > myql -u root -p
  > ```

- 切换数据库mysql

  ```
  use mysql;
  ```

- 修改root账号密码

  ```mysql
  update user set authentication_string=password('123456') where user= 'root';
  ```

  

- 设置登录模式

  ```mysql
  update user set plugin="mysql_native_password";
  ```

- 刷新配置

  ```mysql
  flush privileges;
  ```

- 退出 MySQL

```
	exit;
```

- 重新启动MySQL

  ```
  systemctl restart mysql
  ```

  

## 因弱口令无法成功授权解决
内网测试环境可以这样设置外网建议设置复杂密码防止被扫描破解

- 查看和设置密码安全级别

```sql
select @@validate_password_policy;
set global validate_password_policy=0;
```

- 查看和设置密码长度限制

```sql
select @@validate_password_length;
set global validate_password_length=1;
```

### 其它配置

修改配置文件：`vi /etc/mysql/mysql.conf.d/mysqld.cnf`

```
[client]
default-character-set=utf8

[mysqld]
default-storage-engine=INNODB
character-set-server=utf8
collation-server=utf8_general_ci
lower-case-table-names=1
```

> **注意：** 配置内容追加到对应节点的底部即可
