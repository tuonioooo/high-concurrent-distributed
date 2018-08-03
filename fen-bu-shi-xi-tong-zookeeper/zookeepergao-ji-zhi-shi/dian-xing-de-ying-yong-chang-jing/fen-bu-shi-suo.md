# 分布式锁

共享锁在同一个进程中很容易实现，但是在跨进程或者在不同 Server 之间就不好实现了。Zookeeper 却很容易实现这个功能，实现方式也是需要获得锁的 Server 创建一个 EPHEMERAL\_SEQUENTIAL 目录节点，然后调用[getChildren](http://hadoop.apache.org/zookeeper/docs/r3.2.2/api/org/apache/zookeeper/ZooKeeper.html#getChildren%28java.lang.String, boolean%29)方法获取当前的目录节点列表中最小的目录节点是不是就是自己创建的目录节点，如果正是自己创建的，那么它就获得了这个锁，如果不是那么它就调用[exists](http://hadoop.apache.org/zookeeper/docs/r3.2.2/api/org/apache/zookeeper/ZooKeeper.html#exists%28java.lang.String, boolean%29)\([String](http://java.sun.com/javase/6/docs/api/java/lang/String.html?is-external=true) path, boolean watch\) 方法并监控 Zookeeper 上目录节点列表的变化，一直到自己创建的节点是列表中最小编号的目录节点，从而获得锁，释放锁很简单，只要删除前面它自己所创建的目录节点就行了。

##### 图 4. Zookeeper 实现 Locks 的流程图 {#fig4}

![](https://www.ibm.com/developerworks/cn/opensource/os-cn-zookeeper/image004.gif "图 4. Zookeeper 实现 Locks 的流程图")

同步锁的实现代码如下，完整的代码请看附件：

##### 清单 4. 同步锁的关键代码 {#listing4}

```
void getLock() throws KeeperException, InterruptedException{ 
       List<String> list = zk.getChildren(root, false); 
       String[] nodes = list.toArray(new String[list.size()]); 
       Arrays.sort(nodes); 
       if(myZnode.equals(root+"/"+nodes[0])){ 
           doAction(); 
       } 
       else{ 
           waitForLock(nodes[0]); 
       } 
   } 
   void waitForLock(String lower) throws InterruptedException, KeeperException {
       Stat stat = zk.exists(root + "/" + lower,true); 
       if(stat != null){ 
           mutex.wait(); 
       } 
       else{ 
           getLock(); 
       } 
   }
```





