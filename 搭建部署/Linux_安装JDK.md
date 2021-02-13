# JDK安装(安装包安装)
JDk1.8官网下载地址:[传送](https://www.oracle.com/java/technologies/javase/javase8u211-later-archive-downloads.html)

## JDK下载并上传解压移动到指定目录
- 解压缩：
```bash
tar -zxvf jdk-8u152-linux-x64.tar.gz
```
- 创建目录：
```bash
mkdir -p /usr/local/java
```
- 移动安装包：
```bash
mv jdk1.8.0_152/ /usr/local/java/
```
- 设置所有者：
```bash
chown -R root:root /usr/local/java/
```

## 配置环境变量
- 配置系统环境变量：
```bash
vi /etc/environment
```
- 修改系统环境变量
```bash
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"
export JAVA_HOME=/usr/local/java/jdk1.8.0_152
export JRE_HOME=/usr/local/java/jdk1.8.0_152/jre
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
```

- 配置用户环境变量：
```bash
vi /etc/profile
```
- 修改用户环境变量
```bash
if [ "$PS1" ]; then
  if [ "$BASH" ] && [ "$BASH" != "/bin/sh" ]; then
    # The file bash.bashrc already sets the default PS1.
    # PS1='\h:\w\$ '
    if [ -f /etc/bash.bashrc ]; then
      . /etc/bash.bashrc
    fi
  else
    if [ "`id -u`" -eq 0 ]; then
      PS1='# '
    else
      PS1='$ '
    fi
  fi
fi

export JAVA_HOME=/usr/local/java/jdk1.8.0_152
export JRE_HOME=/usr/local/java/jdk1.8.0_152/jre
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH:$HOME/bin

if [ -d /etc/profile.d ]; then
  for i in /etc/profile.d/*.sh; do
    if [ -r $i ]; then
      . $i
    fi
  done
  unset i
fi
```

- 使用户环境变量生效：
```linux
source /etc/profile
```

## 验证安装是否成功
```bash
java -version

# 输出如下
java version "1.8.0_152"
Java(TM) SE Runtime Environment (build 1.8.0_152-b16)
Java HotSpot(TM) 64-Bit Server VM (build 25.152-b16, mixed mode)
```