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

## 路由和负载均衡的实现

当服务越来越多，规模越来越大，机器的数量也会越来越大，如果纯靠人工来管理和维护服务及地址的信息，已经越来越困难了。并且，依赖单一的硬件负载均衡设备或者使用LVS和Ngnix等软件方案来解决进行路由和负载均衡调度，一旦服务路由或者服务器宕机，所依赖的所有服务都将失效。如果采用双机高可用的部署方案，使用一台服务器“stand by”，能部分解决问题，但是鉴于负载均衡设备的昂贵成本，也难以全面推广。

一旦服务器与Zookeeper集群断开连接，节点就不存在了，通过注册相应的watcher，服务消费者能够第一时间获知提供者机器信息的变更。利用其znode的特点和watcher记住，将作为动态注册和获取服务信息的配置中心，统一管理服务名称和其对应的服务列表消息。我们近乎实时的感知到后端服务器的状态（上线、下线、宕机）。Zookeeper集群之间通过zab协议，服务配置信息能够保持一致，而Zookeeper本身的容错特性和leader选举机制，能保障我们方便地进行扩容。

```
//基于Zookeeper所实现的服务消费者获取服务提供者地址列表的关键代码 消费者
        ZkClient zkClient = new ZkClient("127.0.0.1:2181");
        String serviceName = "service-B";
        String zkServiceList = "127.0.0.1:2181";

        String SERVICE_PATH = "/configcenter/"+serviceName;
        //判断是否存在
        boolean serviceExists = zkClient.exists(SERVICE_PATH);
        //如果存在，获取地址列表
        if(serviceExists){
            serviceList = zkClient.getChildren(SERVICE_PATH);
        }else{
            throw new RuntimeException("节点不存在");
        }
        //注册事件监听
        zkClient.subscribeChildChanges(SERVICE_PATH, new IZkChildListener() {

            @Override
            public void handleChildChange(String arg0, List<String> currentChilds) throws Exception {
                // TODO Auto-generated method stub
                //返回新的列表
                serviceList = currentChilds;
            }
        });
```

这段代码实例化了一个zkClient对象，判断服务名称是否已经注册，如果还没有注册该节点，则表明没有该服务，继而抛出异常。如果存在则获取其所有子节点获取地址后就可以通过负载均衡算法，选出一台服务器，发起远程调用。服务器列表缓存在本地只有当地址列表发生变化时，才需要更新该列表，以降低网络开销。这就是注册一个 subscribeChildChanges 的作用。

## 服务提供者想Zookeeper集群注册的部分关键代码

```
//服务提供者想Zookeeper集群注册服务的关键代码  服务者
        ZkClient zkClient = new ZkClient("127.0.0.1:2181");
        String PATH = "/configcenter";//根节点路径
        //判断是否存在
        boolean rootExists = zkClient.exists(PATH);
        //如果存在，获取地址列表
        if(!rootExists){
            zkClient.createPersistent(PATH);
        }
        String serviceName = "service-c";
        boolean serviceExists = zkClient.exists(PATH+"/"+serviceName);
        if(!serviceExists){
            zkClient.createPersistent((PATH+"/"+serviceName));
        }
        InetAddress address  = InetAddress.getLocalHost();
        //创建当前服务器节点
        String ip = address.getHostAddress().toString();
        //创建当前服务器节点
        zkClient.createEphemeral(PATH+"/"+serviceName+"/"+ip);
        
        for (String item : zkClient.getChildren("/configcenter")) {
            System.out.println(item);
        }
```



