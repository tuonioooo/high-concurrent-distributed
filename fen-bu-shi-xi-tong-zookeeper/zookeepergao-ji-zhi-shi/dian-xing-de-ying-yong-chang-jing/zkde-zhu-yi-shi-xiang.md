# zookeeper的注意事项

* **事务和快照日志**

zookeeper集群不会自动清理事务和快照日志，需要在zookeeper配置文件中配置日志的清理机制。

zookeeper 从3.4.0开始，zookeeper提供了自动清理snapshot和事务日志的功能，通过配置 autopurge.snapRetainCount 和 autopurge.purgeInterval 这两个参数能够实现定时清理了。这两个参数都是在zoo.cfg中配置的：

> **autopurge.purgeInterval** ：
>
> 这个参数指定了清理频率，单位是小时，需要填写一个1或更大的整数，默认是0，表示不开启自己清理功能。
>
> **autopurge.snapRetainCount：**
>
> 这个参数和上面的参数搭配使用，这个参数指定了需要保留的文件数目。默认是保留3个。





