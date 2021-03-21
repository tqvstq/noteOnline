# Gradle

## Gradle 介绍
Gradle是一个基于Apache Ant和Apache Maven概念的项目自动化构建工具。它使用一种基于Groovy的特定领域语言来声明项目设置，而不是传统的XML。Gradle就是工程的管理，帮我们做了依赖、打包、部署、发布、各种渠道的差异管理等工作。[官网传送](https://gradle.org/)

**优点**:
1. 一款新的，功能最强大的构建工具，用它逼格更高
2. 使用程序代替传统的XML配置，项目构建更灵活
3. 丰富的第三方插件，让你随心所欲使用
4. Maven、Ant能做的，Gradle都能做，但是Gradle能做的，Maven、Ant不一定能做。


## Gradle 安装

### 下载 Gradle
[官方下载](https://gradle.org/releases/)
根据情况选择合适的版本

### 解压配置环境变量
将下载的文件解压到一个目录,复制解压地址后添加环境变量(需要依赖jdk)

添加环境变量

添加环境名称为`GRADLE_HOME`的环境变量,变量值为刚复制的解压地址

添加完成后将环境变量加入Path 

```
%GRADLE_HOME%\bin
```

进入Path编辑->新建->粘贴`%GRADLE_HOME%\bin`

### 检查安装是否成功
进入cmd 输入 `gradle -v` 输出版本信息则安装成功


## Gradle 其它了解

## Gradle 配置本地仓库地址
Gradle 默认路径在C盘下,如果我我们使用了Maven有本地仓库,在下载一份就有些浪费空间了,可以配置地址为Maven本地仓库地址实现统一管理

配置方式: 添加环境变量`GRADLE_USER_HOME` 路径为你想要的路径地址就可以改变gradle的缓存路径 


