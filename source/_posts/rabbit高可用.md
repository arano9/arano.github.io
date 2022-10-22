---
layout: page
title: rabbitmq高可用
date: 2021-05-10 19:26:50
catagories: article
tags: rabbitmq
---

[TOC]



# rabbitmq高可用

说到高可用，也就离不开CAP原则，大多的中间件或者服务的高可用我都以两点去思考解决-数据扩容性，功能可用性，类似的，rabbitmq也有其对应的方案。

## 基于插件的模式

### 铲子模式（shovel plugin）

铲子模式就是一个插件，可用于跨网络，异地mq之间的数据备份，数据同步，工作模式如下图：

![image-20210512101447783](https://raw.githubusercontent.com/aranoverse/pic-host/main/img/image-20210512101447783-0785695.png)

实质即shovel插件将远端broker中的que拉过来到交换机进行分发。

### 联邦模式（federation plugin）

联邦插件有两种应用方式，分贝应用在交换器，队列上。

- 联邦交换器：使用federation link 可以将上游远端的交换机信息给放到本地队列中，给本地消费者使用
- 联邦队列：其实类似于前者，只不过可以直接从上游que中拿到消息同步。

![image-20210512110720303](https://raw.githubusercontent.com/aranoverse/pic-host/main/img/image-20210512110720303-0788843.png)





## 基于集群模式

前面提到的模式，更多的不是高可用，而是数据冗余，或者异地同步，算是对具体场景的具体应用。接下来具体说说mq场景的集群模式。

普通集群模式，每个节点都会同步其他节点的的元数据，包括：

1. 队列元数据：队列的名称及属性；

2. 交换器：交换器的名称及属性；

3. 绑定关系元数据：交换器与队列或者交换器与交换器之间的绑定关系；

4. vhost元数据：为vhost内的队列、交换器和绑定提供命名空间及安全属性。

下图中简要解释了构造（略写了其他数据），大致意思指在通过node1访问que-b的时候，会路由到node2，通过node2访问que-a会路由到node1，作为代理的角色。


![image-20210512140505941](https://raw.githubusercontent.com/aranoverse/pic-host/main/img/image-20210512140505941.png)

### 镜像队列模式

镜像队列其实相当于对前者的补充，上图中不难发现一个致命的问题，如果某个节点宕机，则该节点的数据就不可用了，对于整个集群可用性会更低，因此mq有一个镜像队列对其进行补充。在队列的维度进行数据冗余存储，动态选举master镜像，保证高可用。

值得一说的是不论读写请求，最终都会先到达master，然后master状态更新后，通过GM（Guaranteed Multicast）协议广播slave，从而保持状态一致。

下图中三个节点之间都有其队列的slave备份，通过冗余队列保持其高可用

![image-20210512142506435](https://raw.githubusercontent.com/aranoverse/pic-host/main/img/image-20210512142506435.png)

### 仲裁队列模式

镜像队列模式其实对于一般的需求都一足够，那么为什么又有一个仲裁队列模式呢？

1. 镜像队列在同步消息的时候是阻塞同步的，如果消息量过大会造成极大的负载，甚至失败，重新同步，如此循环。

2. 宕机重连的mq节点会丢失之前的所有消息，因此需要重新同步。

   

仲裁队列使用的raft共识算法，无论是消息同步还是leader选举都是需要多个副本达成一致才可进行，并且所有仲裁队列消息同步时都必须落地磁盘，这就保证了，即使宕机重连以后也只是需要同步之后的数据，解决了同步问题。

当然仲裁模式也有其缺点，其一，每个队列都持久化写入磁盘，造成了写放大 ，甚至只要新节点进来就会造成该仲裁队列的持久落地，对磁盘占用有点高；其二，仲裁队列是3.8以后的版本才有的，并且一些特性也不支持，比如非持久性信息，TTL队列等，都是不支持的。



## 小结

因此，推荐使用2n+1节点集群+镜像队列的模式，通过队列冗余，保证mq的高可用。同时使用HAproxy对mq集群进行负载均衡，再结合KeepAlived平滑切换HAProxy，保证HAproxy的高可用。



## 镜像队列集群模式docker部署

### 1. 容器创建运行

部署采用的是docker-compose，首先是环境变量文件配置

```env
#简单设置了默认用户和密码，还有其他参数可看官网
RABBITMQ_DEFAULT_USER=arano
RABBITMQ_DEFAULT_PASS=arano
```

其次是compose.yml文件

```yml
version: "3.3"
services:
  mq_1:
    hostname: mq_1
    container_name: mq_1
    image: rabbitmq:management
    env_file:
      - ./env-files/mq.env
    privileged: true
    volumes:
      - ./mq_1/config:/etc/rabbitmq
      - ./mq_1/data:/var/lib/rabbitmq
      - ./mq_1/log:/var/log/rabbitmq
    ports:
      - "15601:15672"
      - '5601:5672'
    networks:
      arano_net:
        ipv4_address: 192.168.255.11
  mq_2:
    hostname: mq_2
    container_name: mq_2
    image: rabbitmq:management
    env_file:
      - ./env-files/mq.env
    volumes:
      - ./mq_2/config:/etc/rabbitmq
      - ./mq_2/data:/var/lib/rabbitmq
      - ./mq_2/log:/var/log/rabbitmq
    privileged: true
    ports:
      - "15602:15672"
      - '5602:5672'
    networks:
      arano_net:
        ipv4_address: 192.168.255.12
  mq_3:
    hostname: mq_3
    container_name: mq_3
    image: rabbitmq:management
    env_file:
      - ./env-files/mq.env
    volumes:
      - ./mq_3/config:/etc/rabbitmq
      - ./mq_3/data:/var/lib/rabbitmq
      - ./mq_3/log:/var/log/rabbitmq
    ports:
      - "15603:15672"
      - '5603:5672'
    privileged: true
    networks:
      arano_net:
        ipv4_address: 192.168.255.13
networks:
  arano_net:
    name: arano_net
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: 192.168.255.0/24

```



### 2. 集群配置

1. 对每个mq开启management，为management创建新用户

```bash
docker exec -it {mq-container} bash
rabbitmq-plugins enable rabbitmq_management
rabbitmqctl add_user cluster cluster 
rabbitmqctl set_permissions -p / cluster ".*" ".*" ".*"
sbin/rabbitmqctl set_user_tags cluster administrator
```

2. 启动之后将各个节点中的/var/lib/rabbitmq/.erlang.cookie文件都统一为一个，因为mq集群需要erlangcookie进行统一联系

3. 将mq_2,mq_3加入mq_1所在的集群，进入容器执行以下命令

```bash
# 进入容器
docker exec -it {mq-container} bash
rabbitmqctl stop_app
rabbitmqctl reset
#加入mq_1所在集群
rabbitmqctl join_cluster rabbit@mq_1
rabbitmqctl start_app
```

4. 加入集群成功之后，随意一个容器中查看cluster情况，可以看到mq集群中的节点情况

   ```bash
   rabbitmqctl cluster_status
   ```

5. 在任意一个mq容器中，配置镜像队列参数：

   ```bash
   # 这里采用1个master2个slave的模式 ，具体参数组合可见官网
   rabbitmqctl set_policy ha-triple "^" '{"ha-mode":"exactly","ha-params":3}'
   #成功以后显示如下
   setting policy "ha-triple" for pattern "^" to "{"ha-mode":"exactly","ha-params": 3}" with priority "0" for vhost "/" ...
   ```

   

可看到management中node情况

![image-20210512160412605](https://raw.githubusercontent.com/aranoverse/pic-host/main/img/image-20210512160412605.png)

创建一个demo测试情况，通过mq_2创建队列结果mq_1,mq_3都有队列，通过mq_1发送消息，可以从mq_2消费，说明集群创建成功。

![image-20210512161313339](https://raw.githubusercontent.com/aranoverse/pic-host/main/img/image-20210512161313339.png)

![image-20210512161419964](https://raw.githubusercontent.com/aranoverse/pic-host/main/img/image-20210512161419964.png)

后续HAProxy+Keepalived可以再结合配置一下使用。使用上的区别在于，有统一HAproxy ip做负载均衡