# 常见RPC技术

* [Hessian](http://hessian.caucho.com/)

除了hessian协议之外，还提供了通过servlet方式实现的RPC框架——[百度百科](#)

* [Thrift](https://thrift.apache.org/)

Thrift是一个跨语言的服务部署框架，最初由Facebook于2007年开发，2008年进入Apache开源项目。Thrift通过一个中间语言\(IDL, 接口定义语言\)来定义RPC的接口和数据类型，然后通过一个编译器生成不同语言的代码（目前支持C++,Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C\#, Cocoa, Smalltalk和OCaml）,并由生成的代码负责RPC协议层和传输层的实现。——[百度百科](https://baike.baidu.com/item/thrift)

* [Finagle](https://twitter.github.io/finagle/)

Finagle是Twitter基于Netty开发的支持容错的、协议无关的RPC框架，该框架支撑了Twitter的核心服务。

* [Dubbo](https://github.com/alibaba/dubbo)

Dubbo 是阿里巴巴公司开源的一个高性能优秀的服务框架，使得应用可通过高性能的 RPC 实现服务的输出和输入功能，可以和 Spring框架无缝集成。

* [gRPC](http://www.grpc.io/)

gRPC是一个高性能、通用的开源RPC框架，其由Google主要面向移动应用开发并基于HTTP/2协议标准而设计，基于ProtoBuf\(Protocol Buffers\)序列化协议开发，且支持众多开发语言。gRPC提供了一种简单的方法来精确地定义服务和为iOS、Android和后台支持服务自动生成可靠性很强的客户端功能库。客户端充分利用高级流和链接功能，从而有助于节省带宽、降低的TCP链接次数、节省CPU使用、和电池寿命。

