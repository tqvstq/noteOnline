# Linux脚本启动jar包

创建文件
```shell
touch run.sh
```

给文件赋可执行权限
```shell
chmod +x run.sh
```

编辑文件
```shell
vim run.sh
```
```shell
#!/bin/bash
#name:jar包启动脚本;
#date:2019-09-23;
#author：Evan

# 启动jar包名称
APP_NAME=sznf-ztbs-back-rest-0.0.1.jar
# 启动jar包的路径
APP_PATH=/home/4444/ztbs/back
# 启动jar包后日志输出文件
APP_LOG_NAME=back.log
# 每次启动生成新的日志
# APP_LOG_NAME=back_`date +%Y%m%d_%T`.log
# java虚拟机启动参数设置
JAVA_OPTS="-server -Xms800m -Xmx800m -Xmn256m -Xss256k -XX:PermSize=256M -XX:MaxPermSize=512M -XX:SurvivorRatio=8 -XX:MaxTenuringThreshold=5 -XX:GCTimeRatio=19 -Xnoclassgc -XX:+DisableExplicitGC -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:CMSFullGCsBeforeCompaction=0 -XX:-CMSParallelRemarkEnabled -XX:CMSInitiatingOccupancyFraction=70 -XX:SoftRefLRUPolicyMSPerMB=0 -XX:+PrintClassHistogram -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+PrintHeapAtGC -Xloggc:${APP_PATH}/gc.log"
#脚本菜单项
usage() {
 echo "Usage: sh run.sh [start|stop|restart|status]"
 exit 1
}

is_exist(){
 pid=`ps -ef|grep $APP_NAME|grep -v grep|awk '{print $2}' `
 #如果不存在返回1，存在返回0
 if [ -z "${pid}" ]; then
 return 1
 else
 return 0
 fi
}
#启动脚本
start(){
 echo "To start ${APP_NAME}"
 is_exist
 if [ $? -eq "0" ]; then
 echo "${APP_NAME} is already running. pid=${pid} ."
 else
#此处注意修改jar和log文件位置：
 nohup java ${JAVA_OPTS} -jar ${APP_PATH}/$APP_NAME > $APP_LOG_NAME   2>&1 &
 # echo "Has started ${APP_NAME}  pid=`ps -ef|grep $APP_NAME|grep -v grep|awk '{print $2}' `  logFileName=`ls -alt | grep log |head -1|awk '{print $9}'`"
 echo "Has started ${APP_NAME}  pid=`ps -ef|grep $APP_NAME|grep -v grep|awk '{print $2}' `  logFilePath=${APP_PATH}/$APP_LOG_NAME"
#启动后监视输出log日志：
# tail -f ${APP_PATH}/`ls -alt | grep log |head -1|awk '{print $9}'`
 fi
}
#停止脚本
stop(){
 is_exist
 if [ $? -eq "0" ]; then
 kill -9 $pid
 echo "Has stopped ${APP_NAME}"
 else
 echo "${APP_NAME} is not running"
 fi
}
#显示当前jar运行状态
status(){
 is_exist
 if [ $? -eq "0" ]; then
 echo "${APP_NAME} is running. Pid is ${pid}"
 else
 echo "${APP_NAME} is NOT running."
 fi
}
#重启脚本
restart(){
 stop
 echo "Start in 5 seconds ${APP_NAME}"
 sleep 5
 start
}

case "$1" in
 "start")
 start
 ;;
 "stop")
 stop
 ;;
 "status")
 status
 ;;
 "restart")
 restart
 ;;
 *)
 usage
 ;;
esac
```
