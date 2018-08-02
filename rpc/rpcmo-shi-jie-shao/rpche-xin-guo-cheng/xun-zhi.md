# 寻址

## 概述

每次Client向Server端发起请求之前，需要知道该向哪个Server发请求，也就涉及到服务寻址的问题。最简单的方式是直接指定Server的ip,访问特定机器上的服务。

但是在分布式环境中，通过在代码中指定ip的方式是不合适的，所以现在的RPC框架基本都是使用配置的方式来实现服务的寻址。配置方式下涉及Server端服务的注册和Client端的服务寻址，这里就成为了Zookeeper应用的绝佳场景。

## ZooKeeper 管理分布式服务配置

ZooKeeper 分布式服务框架是 Apache Hadoop 的一个子项目，它主要是用来解决分布式应用中经常遇到的一些数据管理问题，如：统一命名服务、状态同步服务、集群管理、分布式应用配置项的管理等。ZooKeeper的架构通过冗余服务实现高可用性。因此，如果第一次无应答，客户端就可以询问另一台ZooKeeper主机。ZooKeeper节点将它们的数据存储于一个分层的命名空间，非常类似于一个文件系统或一个前缀树结构。客户端可以在节点读写，从而以这种方式拥有一个共享的配置服务。![](http://7xkbey.com1.z0.glb.clouddn.com/zookeerp%E6%95%B0%E6%8D%AE%E6%A8%A1%E5%9E%8B.gif)图片来自[分布式服务框架 Zookeeper -- 管理分布式环境中的数据](https://www.ibm.com/developerworks/cn/opensource/os-cn-zookeeper/)

* **服务注册**

当每次Server启动时，想调用ZooKeeper的Client，将自身提供的服务注册到ZooKeeper中，形如：

```
    http://service.test.com/demoservice/demo_1.0.0:127.0.0.1
```

当Server Shut down 时也会将自身节点在ZooKeeper配置中进行摘除。

* **服务寻址**

Client每次请求Server之前，需要向ZooKeeper中查询该服务对应的地址，例如需要使用demoservice，查询key为http://service.test.com/demoservice/demo\_1.0.0的对应ip，即可访问对应服务。

* **服务修改广播**

当一个服务发生修改时，如服务的启动与关闭，都会将消息发送到感兴趣的Client。

ZooKeeper的原理及使用参见[ZooKeeper项目](https://zookeeper.apache.org/)

