### dump

#### 1.  JVM dump 文件介绍

JVM dump文件，是jvm运行时一份内存快照，可以利用这个文件分析当时内存情况，发生OOM时也可以找出问题原因。

#### 2.  JVM dump 文件生成

#####  2.1 进入服务器，使用命令手动生成

```
# 当前内存所有的都保存，包括没活着的对象  pid 为要dump的 进程ID
jmap -dump:format=b,file=xxx.hprof <pid>
例：9516 应用，生成dump文件，文件保存路径为 /data/opt/work/heap/test_live.hprof
jmap -dump:format=b,live,file=/data/opt/work/heap/test_live.hprof 9516

# 进行一次gc 后 生成 dump 文件 （更便于分析）
jmap -dump:format=b,live,file=xxx.hprof <pid>
```

##### 2.2应用OOM时自动导出dump文件

需要在应用启动参数中添加 （可以用参数自定义文件名称），配置后当应用，发生OOOM时，将会导出此时堆中相关信息，可以用于帮助分析此次OOM原因。

```
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/data/opt/work/heap/heapdump.hprof
```

