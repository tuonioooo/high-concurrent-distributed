# 序列化

Client找到对应Server之后就需要传递参数到Server端，Server在处理完数据之后也需要将结果返回给Client。

每种序列化协议都有不同的优点和确定，一个成熟的序列化协议需要通盘考虑通用性，强健性，可调试性/可读性，性能，可扩展性以及安全性等方面。目前常见的序列化协议主要有[hessian](http://hessian.caucho.com/)，XML、JSON、[Protobuf](https://github.com/google/protobuf)、[Thrift](https://thrift.apache.org/)和[Avro](https://avro.apache.org/)。

例如，[pigeon](https://github.com/wu-xiang/pigeon)支持多种序列化方式，序列化方式只需要在客户端调用时通过serialize属性指定，一般情况推荐兼容性最好的hessian。如果需要自行设计序列化方式，可以继承com.dianping.pigeon.remoting.common.codec.DefaultAbstractSerializer类来定义自己的序列化类，并通过SerializerFactory.registerSerializer\(byte serializerType, Serializer serializer\)接口将自定义的序列化类注册进来。

