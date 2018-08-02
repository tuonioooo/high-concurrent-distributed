# RPC概念

## RPC介绍

RPC（Remote Procedure Call）—[远程过程调用](https://baike.baidu.com/item/远程过程调用)，它是一种通过[网络](https://baike.baidu.com/item/网络)从远程计算机程序上请求服务，而不需要了解底层网络技术的协议。[RPC协议](https://baike.baidu.com/item/RPC协议)假定某些[传输协议](https://baike.baidu.com/item/传输协议)的存在，如TCP或UDP，为通信程序之间携带信息数据。在OSI[网络通信](https://baike.baidu.com/item/网络通信)模型中，RPC跨越了[传输层](https://baike.baidu.com/item/传输层)和[应用层](https://baike.baidu.com/item/应用层)。RPC使得开发包括网络[分布式](https://baike.baidu.com/item/分布式)多程序在内的应用程序更加容易。

RPC采用客户机/服务器模式。请求程序就是一个客户机，而服务提供程序就是一个服务器。首先，客户机调用进程发送一个有进程参数的调用信息到服务进程，然后等待应答信息。在服务器端，进程保持睡眠状态直到调用信息到达为止。当一个调用信息到达，服务器获得进程参数，计算结果，发送答复[信息](https://baike.baidu.com/item/信息)，然后等待下一个调用信息，最后，客户端调用进程接收答复信息，获得进程结果，然后调用执行继续进行。

有多种 RPC模式和执行。最初由 Sun 公司提出。IETF ONC 宪章重新修订了 Sun 版本，使得 ONC RPC 协议成为 IETF 标准协议。现在使用最普遍的模式和执行是开放式软件基础的分布式计算[环境](https://baike.baidu.com/item/环境)（DCE）。

远程过程调用是一个_**分布式计算的客户端-服务器（Client/Server）的例子**_，它简单而又广受欢迎。 远程过程调用总是由客户端对服务器发出一个执行若干过程请求，并用客户端提供的参数。执行结果将返回给客户端。 由于存在各式各样的变体和细节差异，对应地派生了各式远程过程调用协议，而且它们并不互相兼容。

为了允许不同的客户端均能访问服务器，许多标准化的 RPC 系统应运而生了。其中大部分采用接口描述语言（Interface Description Language，IDL），方便跨平台的远程过程调用。

![](https://smallnest.gitbooks.io/go-rpc-programming-guide/content/ch0-RPC.gif)

从上图可以看出, RPC 本身是 client-server模型,也是一种 request-response 协议。

有些实现扩展了远程调用的模型，实现了双向的服务调用，但是不管怎样，调用过程还是由一个客户端发起，服务器端提供响应，基本模型没有变化。

服务的调用过程为：

1. client调用client stub，这是一次本地过程调用
2. client stub将参数打包成一个消息，然后发送这个消息。打包过程也叫做 marshalling
3. client所在的系统将消息发送给server
4. server的的系统将收到的包传给server stub
5. server stub解包得到参数。 解包也被称作 unmarshalling
6. 最后server stub调用服务过程. 返回结果按照相反的步骤传给client  

## 参考文档

[百度百科](https://baike.baidu.com/item/远程过程调用协议/6893245?fromtitle=RPC&fromid=609861&fr=aladdin)

