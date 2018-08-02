# Zookeeper集群

Apache ZooKeeper是一个为分布式应用所设计的开源协调服务，其设计目的是为了减轻分布式应用程序所承担的协调任务。它可以为用户提供同步、配置管理、分组和命名等服务。在这里，对ZooKeeper的完全分布式集群安装部署进行介绍。

一、基本环境

JDK　　　 ：1.7.0\_75（要求1.6+）

ZooKeeper：3.4.12

主机数：3

（要求3+，且必须是奇数，因为ZooKeeper的选举算法）

| 主机名 | IP地址 | JDK | ZooKeeper | myid |
| :--- | :--- | :--- | :--- | :--- |
| god.cn | 192.168.127.87 | 1.7.0\_75 | server.1 | 1 |
| obd.cn | 192.168.127.99 | 1.7.0\_75 | server.2 | 2 |
| mod.cn | 192.168.127.101 | 1.7.0\_75 | server.3 | 3 |

二、master节点上安装配置   下载地址：

[http://zookeeper.apache.org/releases.html](http://zookeeper.apache.org/releases.html)

1、下载并解压ZooKeeper-3.4.12.tar.gz

```
tar -zxvf zookeeper-3.4.12.tar.gz
```

这里路径为

/usr/local/zookeeper-3.4.12

2、设置the Java heap size

（个人感觉一般不需要配置）

保守地use a maximum heap size of 3GB for a 4GB machine

3、$ZOOKEEPER\_HOME/conf/zoo.cfg

```
cp zoo_sample.cfg zoo.cfg
```

新建此配置文件，并设置内容



> \# The number of milliseconds of each tick
>
> tickTime=2000
>
> \# The number of ticks that the initial
>
> \# synchronization phase can take
>
> initLimit=10
>
> \# The number of ticks that can pass between
>
> \# sending a request and getting an acknowledgement
>
> syncLimit=5
>
> \# the directory where the snapshot is stored.
>
> \# do not use /tmp for storage, /tmp here is just
>
> \# example sakes.
>
> dataDir=/home/fesh/data/zookeeper
>
> \# the port at which the clients will connect
>
> clientPort=2181
>
> 　server.1=god.cn:2888:3888  \#（主机名、心跳端口、数据端口）
>
> 　server.2=obd.cn:2888:3888
>
>   server.3=mod.cn:2888:3888

4、/usr/local/data/zookeeper/myid

在节点配置的dataDir指定的目录下面，创建一个myid文件，里面内容为一个数字，用来标识当前主机，$ZOOKEEPER\_HOME/conf/zoo.cfg文件中配置的server.X，则myid文件中就输入这个数字X。（即在每个节点上新建并设置文件myid，其内容与zoo.cfg中的id相对应）这里god.cn节点为 1

```
mkdir -p /usr/local/data/zookeeper/myid
cd /usr/local/data/zookeeper
touch myid
echo "1" > myid
```

5、设置日志conf/log4j.properties

```
# Define some default values that can be overridden by system properties
zookeeper.root.logger=INFO, CONSOLE
```

改为

```
# Define some default values that can be overridden by system properties
zookeeper.root.logger=INFO, ROLLINGFILE
```

将

```
#
# Add ROLLINGFILE to rootLogger to get log file output
# Log DEBUG level and above messages to a log file
log4j.appender.ROLLINGFILE=org.apache.log4j.RollingFileAppender
```

改为---每天一个log日志文件，而不是在同一个log文件中递增日志

```
#
# Add ROLLINGFILE to rootLogger to get log file output
# Log DEBUG level and above messages to a log file
log4j.appender.ROLLINGFILE=org.apache.log4j.DailyRollingFileAppender
```

在改为DailyRollingFileAppender同时，需要注释log4j.appender.ROLLINGFILE.MaxFileSize，这个属性是RollingFileAppender

```
# Max log file size of 10MB
#log4j.appender.ROLLINGFILE.MaxFileSize=10MB
```

bin/zkEvn.sh

```
if [ "x${ZOO_LOG_DIR}" = "x" ]
then
ZOO_LOG_DIR="."
fi

if [ "x${ZOO_LOG4J_PROP}" = "x" ]
then
ZOO_LOG4J_PROP="INFO,CONSOLE"
fi
```

改为

```
if [ "x${ZOO_LOG_DIR}" = "x" ]
then
ZOO_LOG_DIR="$ZOOBINDIR/../logs"
fi

if [ "x${ZOO_LOG4J_PROP}" = "x" ]
then
ZOO_LOG4J_PROP="INFO,ROLLINGFILE"
fi
```

 参考：Zookeeper运维的一些经验

[http://mp.weixin.qq.com/s?\_\_biz=MzAxMjQ5NDM1Mg==&mid=2651024176&idx=1&sn=7659ea6a7bf5c37b083e30060c3e55ca&chksm=8047384fb730b1591ff1ce7081822577112087fc7ec3976f020a263b503f6a8ef0856b3a3057&scene=0\#wechat\_redirect&utm\_source=tuicool&utm\_medium=referral](http://mp.weixin.qq.com/s?__biz=MzAxMjQ5NDM1Mg==&mid=2651024176&idx=1&sn=7659ea6a7bf5c37b083e30060c3e55ca&chksm=8047384fb730b1591ff1ce7081822577112087fc7ec3976f020a263b503f6a8ef0856b3a3057&scene=0#wechat_redirect&utm_source=tuicool&utm_medium=referral)

三、从god.cn节点分发文件到其他节点

1、在god.cn节点的/usr/local/目录下

```
scp -r zookeeper-3.4.12  obd.cn:/usr/local/
scp -r zookeeper-3.4.12  mod.cn:/usr/local/
scp -r data/ obd.cn :/usr/local/
scp -r data/ mod.cn :/usr/local/
```

2、在obd.cn节点的/usr/local/目录下

```
vi ./data/zookeeper/myid
```

修改为 2

3、在mod.cn节点的/usr/local/目录下

```
vi ./data/zookeeper/myid
```

修改为3

四、其他配置

1、在每个节点配置/etc/hosts （并保证每个节点/etc/hostname中分别为god.cn、obd.cn、mod.cn） 主机 -IP地址映射

```
192.168.127.87 god.cn
192.168.127.99 obd.cn
192.168.127.101 mod.cn
```

2、在每个节点配置环境变量/etc/profile

```
#Set ZOOKEEPER_HOME ENVIRONMENT
export ZOOKEEPER_HOME=/home/fesh/zookeeper-3.4.12
export PATH=$PATH:$ZOOKEEPER_HOME/bin
```

五、启动

 在每个节点上$ZOOKEEPER\_HOME目录下，运行 （这里的启动顺序为 master &gt;  slave1 &gt;  slave2 ）

```
bin/zkServer.sh start
```

并用命令查看启动状态

```
bin/zkServer.sh status
```



god.cn节点

![](https://images0.cnblogs.com/i/582587/201408/151352286398995.jpg)

![](file:///C:/Users/tony/AppData/Local/Temp/enhtmlclip/Image%2822%29.png)

obd.cn节点

![](https://images0.cnblogs.com/i/582587/201408/151352385149445.jpg)

![](file:///C:/Users/tony/AppData/Local/Temp/enhtmlclip/Image%2823%29.png)

mod.cn节点

![](file:///C:/Users/tony/AppData/Local/Temp/enhtmlclip/Image%2824%29.png)

zookeeper启动、停止相关命令：

```
zkServer.sh start  #启动


zkServer.sh stop   #停止


zkServer.sh restart #重启


zkServer.sh status  #查看状况
```



