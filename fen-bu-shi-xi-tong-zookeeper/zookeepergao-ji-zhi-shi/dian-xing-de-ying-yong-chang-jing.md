# 典型的应用场景

[分布式配置中心](/fen-bu-shi-xi-tong-zookeeper/zookeepergao-ji-zhi-shi/dian-xing-de-ying-yong-chang-jing/fen-bu-shi-pei-zhi-zhong-xin.md)

[分布式锁](/fen-bu-shi-xi-tong-zookeeper/zookeepergao-ji-zhi-shi/dian-xing-de-ying-yong-chang-jing/fen-bu-shi-suo.md)

[集群管理（Group Membership）](/fen-bu-shi-xi-tong-zookeeper/zookeepergao-ji-zhi-shi/dian-xing-de-ying-yong-chang-jing/ji-qun-de-rong-cuo.md)

[统一的命名服务](/fen-bu-shi-xi-tong-zookeeper/zookeepergao-ji-zhi-shi/dian-xing-de-ying-yong-chang-jing/ming-ming-fu-wu.md) 

[zookeeper的注意事项](/fen-bu-shi-xi-tong-zookeeper/zookeepergao-ji-zhi-shi/dian-xing-de-ying-yong-chang-jing/zkde-zhu-yi-shi-xiang.md) 

[队列管理](/fen-bu-shi-xi-tong-zookeeper/zookeepergao-ji-zhi-shi/dian-xing-de-ying-yong-chang-jing/dui-lie-guan-li.md) 



## 总结 {#major4}

Zookeeper 作为 Hadoop 项目中的一个子项目，是 Hadoop 集群管理的一个必不可少的模块，它主要用来控制集群中的数据，如它管理 Hadoop 集群中的 NameNode，还有 Hbase 中 Master Election、Server 之间状态同步等。

本文介绍的 Zookeeper 的基本知识，以及介绍了几个典型的应用场景。这些都是 Zookeeper 的基本功能，最重要的是 Zoopkeeper 提供了一套很好的分布式集群管理的机制，就是它这种基于层次型的目录树的数据结构，并对树中的节点进行有效管理，从而可以设计出多种多样的分布式的数据管理模型，而不仅仅局限于上面提到的几个常用应用场景。

