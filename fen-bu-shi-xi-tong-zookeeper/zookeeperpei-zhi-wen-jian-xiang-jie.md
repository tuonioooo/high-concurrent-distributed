# Zookeeper配置文件详解、下载目录说明

## 下载目录说明

下载并解压ZooKeeper软件压缩包后，可以看到zk包含以下的文件和目录：

![](http://www.2cto.com/uploadfile/Collfiles/20161226/20161226095913377.jpg)

![](http://www.2cto.com/uploadfile/Collfiles/20161226/20161226095914381.jpg)  
ZooKeeper软件的文件和目录

* bin目录 zk的可执行脚本目录，包括zk服务进程，zk客户端，等脚本。其中，.sh是[Linux](http://www.2cto.com/os/linux/)环境下的脚本，.cmd是Windows环境下的脚本。

* lib目录 zk依赖的包。

* libexec目录 一些用于操作zk的工具包。

## 配置文件详解

```
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial 
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between 
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just 
# example sakes.
dataDir=/usr/local/data/zookeeper
# the port at which the clients will connect
clientPort=2181

server.1=god.cn:2888:3888
server.2=obd.cn:2888:3888
server.3=mod.cn:2888:3888

# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the 
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1
```

> tickTime：
>
> 时长单位为毫秒，为zk使用的基本时间度量单位。例如，1 \* tickTime是客户端与zk服务端的心跳时间，2 \* tickTime是客户端会话的超时时间。
>
> tickTime的默认值为2000毫秒，更低的tickTime值可以更快地发现超时问题，但也会导致更高的网络流量（心跳消息）和更高的CPU使用率（会话的跟踪处理）。
>
> clientPort：
>
> zk服务进程监听的TCP端口，默认情况下，服务端会监听2181端口。



