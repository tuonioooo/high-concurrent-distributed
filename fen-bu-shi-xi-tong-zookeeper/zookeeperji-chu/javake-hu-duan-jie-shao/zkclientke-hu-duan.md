# zkClient客户端

## 概述

ZkClient解决了watcher的一次性注册问题，将znode的事件重新定义为子节点的变化、数据的变化、连接状态的变化三类，有ZkClient统一将watcher的WatchedEvent转换到以上三种情况中去处理，watcher执行后重新读取数据的同时，在注册新的相同的watcher。

## zkClient简单实例

```
public static void main( String[] args )
    {
        //连接Zookeeper
        ZkClient zkClient = new ZkClient("127.0.0.1:2181");
        //创建节点
        zkClient.create("/root", "server data", CreateMode.PERSISTENT);
        //创建子节点
        zkClient.create("/root/server", "server data", CreateMode.EPHEMERAL);
        //获取子节点
        List<String> children = zkClient.getChildren("/root");
        //编辑子节点
        for (String item : children) {
            System.out.println(item);
        }
        //获取子节点的数量
        Integer number = zkClient.countChildren("/root");
        //判断节点是否存在
        if(zkClient.exists("/root")){
            System.out.println("存在");
        };
        //写入数据
        zkClient.writeData("/root/child", "哈哈哈");
        //读取节点数据
        Object object = zkClient.readData("/root/child");
        //删除节点
        zkClient.delete("/root/child");
    }
```

ZkClient将Zookeeper的watcher机制转化为一种更加容易理解的订阅形式，并且这种关系是可以保持的，而非一次性的。也就是说子节点的变化、数据的变化、状态的变化是可以订阅的。当watcher使用完后，zkClient会自动增加一个相同的watcher。

```
zkClient.subscribeChildChanges("/root", new IZkChildListener() {
            
            @Override
            public void handleChildChange(String arg0, List<String> arg1) throws Exception {
                // TODO Auto-generated method stub
                System.out.println("子节点的变化");
            }
        });
        zkClient.subscribeDataChanges("/root", new IZkDataListener() {
            
            @Override
            public void handleDataDeleted(String arg0) throws Exception {
                // TODO Auto-generated method stub
                System.out.println("数据被删除");
            }
            
            @Override
            public void handleDataChange(String arg0, Object arg1) throws Exception {
                // TODO Auto-generated method stub
                System.out.println("数据被更改");
            }
        });
        zkClient.subscribeStateChanges(new IZkStateListener() {
            
            @Override
            public void handleStateChanged(KeeperState arg0) throws Exception {
                // TODO Auto-generated method stub
                System.out.println("数据状态改变");
            }
            
            @Override
            public void handleNewSession() throws Exception {
                // TODO Auto-generated method stub
                System.out.println("当session expire异常重新连接时，由于原来的所有的watcher和EPHEMERAL节点都已失效，可以在这里进行相应的容错处理");
            }
        });
```



