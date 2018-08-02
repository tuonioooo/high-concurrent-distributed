# RPC概念—— [百度百科](https://baike.baidu.com/item/%E8%BF%9C%E7%A8%8B%E8%BF%87%E7%A8%8B%E8%B0%83%E7%94%A8%E5%8D%8F%E8%AE%AE/6893245?fromtitle=RPC&fromid=609861&fr=aladdin) 

## 什么是RPC

RPC（Remote Procedure Call）—[远程过程调用](https://baike.baidu.com/item/远程过程调用)，它是一种通过[网络](https://baike.baidu.com/item/网络)从远程计算机程序上请求服务，而不需要了解底层网络技术的协议。[RPC协议](https://baike.baidu.com/item/RPC协议)假定某些[传输协议](https://baike.baidu.com/item/传输协议)的存在，如TCP或UDP，为通信程序之间携带信息数据。在OSI[网络通信](https://baike.baidu.com/item/网络通信)模型中，RPC跨越了[传输层](https://baike.baidu.com/item/传输层)和[应用层](https://baike.baidu.com/item/应用层)。RPC使得开发包括网络[分布式](https://baike.baidu.com/item/分布式)多程序在内的应用程序更加容易。

RPC采用客户机/服务器模式。请求程序就是一个客户机，而服务提供程序就是一个服务器。首先，客户机调用进程发送一个有进程参数的调用信息到服务进程，然后等待应答信息。在服务器端，进程保持睡眠状态直到调用信息到达为止。当一个调用信息到达，服务器获得进程参数，计算结果，发送答复[信息](https://baike.baidu.com/item/信息)，然后等待下一个调用信息，最后，客户端调用进程接收答复信息，获得进程结果，然后调用执行继续进行。

有多种 RPC模式和执行。最初由 Sun 公司提出。IETF ONC 宪章重新修订了 Sun 版本，使得 ONC RPC 协议成为 IETF 标准协议。现在使用最普遍的模式和执行是开放式软件基础的分布式计算[环境](https://baike.baidu.com/item/环境)（DCE）。



