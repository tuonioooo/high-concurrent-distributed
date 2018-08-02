# 常见RPC技术

* [Hessian](http://hessian.caucho.com/)

除了hessian协议之外，还提供了通过servlet方式实现的RPC框架——[百度百科](#)

* [Thrift](https://thrift.apache.org/)

Thrift是一个跨语言的服务部署框架，最初由Facebook于2007年开发，2008年进入Apache开源项目。Thrift通过一个中间语言\(IDL, 接口定义语言\)来定义RPC的接口和数据类型，然后通过一个编译器生成不同语言的代码（目前支持C++,Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C\#, Cocoa, Smalltalk和OCaml）,并由生成的代码负责RPC协议层和传输层的实现。——[百度百科](https://baike.baidu.com/item/thrift)

* [Finagle](https://twitter.github.io/finagle/)

Finagle是Twitter基于Netty开发的支持容错的、协议无关的RPC框架，该框架支撑了Twitter的核心服务。

* [Dubbo](https://github.com/alibaba/dubbo)

Dubbo 是阿里巴巴公司开源的一个高性能优秀的服务框架，使得应用可通过高性能的 RPC 实现服务的输出和输入功能，可以和 Spring框架无缝集成。——[百度百科](https://baike.baidu.com/item/Dubbo/18907815?fr=aladdin)

* [gRPC](http://www.grpc.io/)

gRPC是一个高性能、通用的开源RPC框架，其由Google主要面向移动应用开发并基于HTTP/2协议标准而设计，基于ProtoBuf\(Protocol Buffers\)序列化协议开发，且支持众多开发语言。gRPC提供了一种简单的方法来精确地定义服务和为iOS、Android和后台支持服务自动生成可靠性很强的客户端功能库。客户端充分利用高级流和链接功能，从而有助于节省带宽、降低的TCP链接次数、节省CPU使用、和电池寿命。

相关教程文档：[http://doc.oschina.net/grpc?t=60134](http://doc.oschina.net/grpc?t=60134) 

* JavaRMI 

[RMI](https://baike.baidu.com/item/RMI)

（Remote Method Invocation，

[远程](https://baike.baidu.com/item/%E8%BF%9C%E7%A8%8B)

方法调用）是用Java在JDK1.2中实现的，它大大增强了Java开发

[分布式应用](https://baike.baidu.com/item/%E5%88%86%E5%B8%83%E5%BC%8F%E5%BA%94%E7%94%A8)

的能力。Java作为一种风靡一时的网络开发语言，其巨大的威力就体现在它强大的开发分布式网络应用的能力上，而RMI就是开发百分之百纯Java的网络分布式应用系统的核心解决方案之一。其实它可以被看作是RPC的Java版本。但是传统

[RPC](https://baike.baidu.com/item/RPC)

并不能很好地应用于

[分布式对象](https://baike.baidu.com/item/%E5%88%86%E5%B8%83%E5%BC%8F%E5%AF%B9%E8%B1%A1)

系统。而Java RMI 则支持存储于不同

[地址空间](https://baike.baidu.com/item/%E5%9C%B0%E5%9D%80%E7%A9%BA%E9%97%B4)

的程序级对象之间彼此进行通信，实现远程对象之间的无缝远程调用。



