# RPC核心过程/RPC需要解决的问题

## 通信协议

RPC框架与具体的协议无关。服务通信主要是Client和Server之间建立网络连接，所有交换的数据都在这个连接里传输，连接可以是按需连接，调用结束后就断掉，也可以是长连接，多个远程过程调用共享同一个连接。RPC 可基于**HTTP 或 TCP 协议**，Web Service 就是基于 HTTP 协议的 RPC，它具有良好的跨平台性，但其性能却不如基于 TCP 协议的 RPC，最早的CORBA，Java RMI，Web Service的RPC风格，Hessian，Thrift，甚至Restful API，目前pigeon使用的有TCP和HTTP协议。在java领域一些工具和框架已经封装对底层协议的使用进行了封装例如比较著名的[Netty](http://netty.io/)\(这也是pigeon在使用的通信框架），[Apache MINA](https://mina.apache.org/)。

1. **TCP/HTTP**：众所周知，TCP 是传输层协议，HTTP 是应用层协议，而传输层较应用层更加底层，在数据传输方面，越底层越快，因此，在一般情况下，TCP 一定比 HTTP 快。
2. **消息ID**：RPC 的应用场景实质是一种**可靠的请求应答消息流**，和 HTTP 类似。因此选择长连接方式的 TCP 协议会更高效，与 HTTP 不同的是在协议层面我们**定义了每个消息的唯一 id**，因此可以更容易的复用连接。
3. **IO方式**：为了支持高并发，传统的阻塞式 IO 显然不太合适，因此我们需要**异步的 IO**，即 NIO。Java 提供了 NIO 的解决方案，Java 7 也提供了更优秀的 NIO.2 支持。
4. **多连接**：既然使用长连接，那么第一个问题是到底 client 和 server 之间需要多少根连接？实际上单连接和多连接在使用上没有区别，对于**数据传输量较小**的应用类型，**单连接**基本足够。单连接和多连接最大的区别在于，每根连接都有自己私有的发送和接收缓冲区，因此**大数据量传输时分散在不同的连接缓冲区会得到更好的吞吐效率**。所以，如果你的数据传输量不足以让单连接的缓冲区一直处于饱和状态的话，那么使用多连接并不会产生任何明显的提升，反而会增加**连接管理**的开销。
5. **心跳**：连接是由 client 端发起建立并维持。如果 client 和 server 之间是直连的，那么连接一般不会中断（当然物理链路故障除外）。如果 client 和 server 连接经过一些负载中转设备，有可能连接一段时间不活跃时会被这些中间设备中断。为了保持连接有必要定时为每个连接发送心跳数据以维持连接不中断。心跳消息是 RPC 框架库使用的内部消息，在前文协议头结构中也有一个专门的**心跳位**，就是用来标记心跳消息的，它对业务应用透明。

## 寻址

### 概述

每次Client向Server端发起请求之前，需要知道该向哪个Server发请求，也就涉及到服务寻址的问题。最简单的方式是直接指定Server的ip,访问特定机器上的服务。

但是在分布式环境中，通过在代码中指定ip的方式是不合适的，所以现在的RPC框架基本都是使用配置的方式来实现服务的寻址。配置方式下涉及Server端服务的注册和Client端的服务寻址，这里就成为了Zookeeper应用的绝佳场景。

### ZooKeeper 管理分布式服务配置

ZooKeeper 分布式服务框架是 Apache Hadoop 的一个子项目，它主要是用来解决分布式应用中经常遇到的一些数据管理问题，如：统一命名服务、状态同步服务、集群管理、分布式应用配置项的管理等。ZooKeeper的架构通过冗余服务实现高可用性。因此，如果第一次无应答，客户端就可以询问另一台ZooKeeper主机。ZooKeeper节点将它们的数据存储于一个分层的命名空间，非常类似于一个文件系统或一个前缀树结构。客户端可以在节点读写，从而以这种方式拥有一个共享的配置服务。![](http://7xkbey.com1.z0.glb.clouddn.com/zookeerp数据模型.gif)

图片来自[分布式服务框架 Zookeeper -- 管理分布式环境中的数据](https://www.ibm.com/developerworks/cn/opensource/os-cn-zookeeper/)

* **服务注册**

当每次Server启动时，想调用ZooKeeper的Client，将自身提供的服务注册到ZooKeeper中，形如：

```
    http://service.test.com/demoservice/demo_1.0.0:127.0.0.1
```

当Server Shut down 时也会将自身节点在ZooKeeper配置中进行摘除。

* **服务寻址**

Client每次请求Server之前，需要向ZooKeeper中查询该服务对应的地址，例如需要使用demoservice，查询key为[http://service.test.com/demoservice/demo\_1.0.0的对应ip，即可访问对应服务。](http://service.test.com/demoservice/demo_1.0.0的对应ip，即可访问对应服务。)

* **服务修改广播**

当一个服务发生修改时，如服务的启动与关闭，都会将消息发送到感兴趣的Client。

ZooKeeper的原理及使用参见[ZooKeeper项目](https://zookeeper.apache.org/)

## 序列化

Client找到对应Server之后就需要传递参数到Server端，Server在处理完数据之后也需要将结果返回给Client。

每种序列化协议都有不同的优点和确定，一个成熟的序列化协议需要通盘考虑通用性，强健性，可调试性/可读性，性能，可扩展性以及安全性等方面。目前常见的序列化协议主要有[hessian](http://hessian.caucho.com/)，XML、JSON、[Protobuf](https://github.com/google/protobuf)、[Thrift](https://thrift.apache.org/)和[Avro](https://avro.apache.org/)。

例如，[pigeon](https://github.com/wu-xiang/pigeon)支持多种序列化方式，序列化方式只需要在客户端调用时通过serialize属性指定，一般情况推荐兼容性最好的hessian。如果需要自行设计序列化方式，可以继承com.dianping.pigeon.remoting.common.codec.DefaultAbstractSerializer类来定义自己的序列化类，并通过SerializerFactory.registerSerializer\(byte serializerType, Serializer serializer\)接口将自定义的序列化类注册进来。

## 负载均衡

负载平衡（Load balancing）是一种计算机网络技术，用来在多个计算机（计算机集群）、网络连接、CPU、磁盘驱动器或其他资源中分配负载，以达到最佳化资源使用、最大化吞吐率、最小化响应时间、同时避免过载的目的。负载均衡器有各种各样的工作排程算法（用于决定将前端用户请求发送到哪一个后台服务器），最简单的是随机选择和轮询。更为高级的负载均衡器会考虑其它更多的相关因素，如后台服务器的负载，响应时间，运行状态，活动连接数，地理位置，处理能力，或最近分配的流量。

例如，[pigeon](https://github.com/wu-xiang/pigeon)支持random、[roundRobin](https://zh.wikipedia.org/wiki/循環制)、weightedAutoware这几种类型，默认weighted Autoware策略。在pigeon框架中在每一次发送请求时都会有在线程中计算请求的返回时间，weighted Autoware策略是根据各个线程统计的响应时间来判断该服务的负载情况，响应时间越长说明该机器的负载越重。

