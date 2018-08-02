# Zookeeper leader选举

让我们分析如何在ZooKeeper集合中选举leader节点。考虑一个集群中有N个节点。leader选举的过程如下：



* 所有节点创建具有相同路径 /app/leader\_election/guid\_ 的顺序、临时节点。
 
* ZooKeeper集合将附加10位序列号到路径，创建的znode将是 /app/leader\_election/guid\_0000000001，/app/leader\_election/guid\_0000000002等。
 
* 对于给定的实例，在znode中创建最小数字的节点成为leader，而所有其他节点是follower。
 
* 每个follower节点监视下一个具有最小数字的znode。例如，创建znode/app/leader\_election/guid\_0000000008的节点将监视znode/app/leader\_election/guid\_0000000007，创建znode/app/leader\_election/guid\_0000000007的节点将监视znode/app/leader\_election/guid\_0000000006。
 
* 如果leader关闭，则其相应的znode/app/leader\_electionN会被删除。
 
* 下一个在线follower节点将通过监视器获得关于leader移除的通知。
 
* 下一个在线follower节点将检查是否存在其他具有最小数字的znode。如果没有，那么它将承担leader的角色。否则，它找到的创建具有最小数字的znode的节点将作为leader。
 
* 类似地，所有其他follower节点选举创建具有最小数字的znode的节点作为leader。
 



leader选举是一个复杂的过程，但ZooKeeper服务使它非常简单。让我们在下一章中继续学习ZooKeeper安装，以用于开发目的。

