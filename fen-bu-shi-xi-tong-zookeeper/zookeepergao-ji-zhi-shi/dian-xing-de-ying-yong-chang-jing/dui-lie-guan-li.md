# 队列管理

Zookeeper 可以处理两种类型的队列：

1. 当一个队列的成员都聚齐时，这个队列才可用，否则一直等待所有成员到达，这种是同步队列。
2. 队列按照 FIFO 方式进行入队和出队操作，例如实现生产者和消费者模型。

同步队列用 Zookeeper 实现的实现思路如下：

创建一个父目录 /synchronizing，每个成员都监控标志（Set Watch）位目录 /synchronizing/start 是否存在，然后每个成员都加入这个队列，加入队列的方式就是创建 /synchronizing/member\_i 的临时目录节点，然后每个成员获取 / synchronizing 目录的所有目录节点，也就是 member\_i。判断 i 的值是否已经是成员的个数，如果小于成员个数等待 /synchronizing/start 的出现，如果已经相等就创建 /synchronizing/start。

用下面的流程图更容易理解：

##### 图 5. 同步队列流程图 {#fig5}

![](https://www.ibm.com/developerworks/cn/opensource/os-cn-zookeeper/image005.gif "图 5. 同步队列流程图")

同步队列的关键代码如下，完整的代码请看附件：

##### 清单 5. 同步队列 {#listing5}

```
void addQueue() throws KeeperException, InterruptedException{ 
       zk.exists(root + "/start",true); 
       zk.create(root + "/" + name, new byte[0], Ids.OPEN_ACL_UNSAFE, 
       CreateMode.EPHEMERAL_SEQUENTIAL); 
       synchronized (mutex) { 
           List<String> list = zk.getChildren(root, false); 
           if (list.size() < size) { 
               mutex.wait(); 
           } else { 
               zk.create(root + "/start", new byte[0], Ids.OPEN_ACL_UNSAFE,
                CreateMode.PERSISTENT); 
           } 
       } 
}
```

当队列没满是进入 wait\(\)，然后会一直等待 Watch 的通知，Watch 的代码如下：

```
public void process(WatchedEvent event) { 
       if(event.getPath().equals(root + "/start") &&
        event.getType() == Event.EventType.NodeCreated){ 
           System.out.println("得到通知"); 
           super.process(event); 
           doAction(); 
       } 
   }
```

FIFO 队列用 Zookeeper 实现思路如下：

实现的思路也非常简单，就是在特定的目录下创建 SEQUENTIAL 类型的子目录 /queue\_i，这样就能保证所有成员加入队列时都是有编号的，出队列时通过 getChildren\( \) 方法可以返回当前所有的队列中的元素，然后消费其中最小的一个，这样就能保证 FIFO。

下面是生产者和消费者这种队列形式的示例代码，完整的代码请看附件：

##### 清单 6. 生产者代码 {#listing6}

```
boolean produce(int i) throws KeeperException, InterruptedException{ 
       ByteBuffer b = ByteBuffer.allocate(4); 
       byte[] value; 
       b.putInt(i); 
       value = b.array(); 
       zk.create(root + "/element", value, ZooDefs.Ids.OPEN_ACL_UNSAFE, 
                   CreateMode.PERSISTENT_SEQUENTIAL); 
       return true; 
   }
```

##### 清单 7. 消费者代码 {#listing7}

```
int consume() throws KeeperException, InterruptedException{ 
       int retvalue = -1; 
       Stat stat = null; 
       while (true) { 
           synchronized (mutex) { 
               List<String> list = zk.getChildren(root, true); 
               if (list.size() == 0) { 
                   mutex.wait(); 
               } else { 
                   Integer min = new Integer(list.get(0).substring(7)); 
                   for(String s : list){ 
                       Integer tempValue = new Integer(s.substring(7)); 
                       if(tempValue < min) min = tempValue; 
                   } 
                   byte[] b = zk.getData(root + "/element" + min,false, stat); 
                   zk.delete(root + "/element" + min, 0); 
                   ByteBuffer buffer = ByteBuffer.wrap(b); 
                   retvalue = buffer.getInt(); 
                   return retvalue; 
               } 
           } 
       } 
}
```



