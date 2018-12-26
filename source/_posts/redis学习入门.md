---
title: redis学习入门
date: 2018-09-19 23:50:36
tags: database
catagories: article
cover: https://images.unsplash.com/photo-1537326823431-816e18a6eb65?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=5dea620e6f587d3c1ebbf680709f8e43&auto=format&fit=crop&w=500&q=60
author: 
  nick: Vincent
  link: bloger.top

# 如果文章为转载文章，需要多加文章出处项
editor:
  name: Vincent
  link: https://bloger.top

# 首页每篇文章的子标题
subtitle: redis入门
---
# Redis使用入门
## Redis数据结构  
redis支持的数据类型：  
- string（字符串）   
    1. string是redis**基本数据类型**，一个key对应一个value，最大可以存储512MB  
    2. **是二进制安全的**，可以包含任何数据，比如图片比如序列化对象 
    3. 使用如下：
```cmd
redis 127.0.0.1:6379> SET testkey" testValue"
OK
redis 127.0.0.1:6379> GET testKey
"testValue"
```
    
- Hash（哈希）  
    1. 是键值对key->value的集合  
    2. 即string类型的filed和value的映射表，适合存储对象。
    3. 使用如下：  
```cmd
redis> HMSET myhash testfield1 "testValue" testField2 "testValue2"
"OK"
redis> HGET myhash testField1
"testValue"
```  
- List(列表)  
就是简单的字符串列表，但是可以方便地添加在列表头或者尾。使用如下：  
```cmd
redis 127.0.0.1:6379> lpush testList element1
(integer) 1
redis 127.0.0.1:6379> lpush testList element2
(integer) 2
redis 127.0.0.1:6379> lpush testList element3
(integer) 3
redis 127.0.0.1:6379> lrange runoob 0 4
1) "element3"
2) "element2"
3) "element1"
redis 127.0.0.1:6379>
```  
- Set(集合)  
    string类型的无序集合通过哈希表实现，时间复杂度都是O(1),使用如下：  
```cmd
redis 127.0.0.1:6379> sadd test redis
(integer) 1
redis 127.0.0.1:6379> sadd test mongodb
(integer) 1
redis 127.0.0.1:6379> smembers test
1) "redis"
2) "mongodb"
```
- zSet(sorted set: 有序集合)  
    与set的区别主要在于有序，每一个元素都会关联一个double对类型的分数，redis通过这个分数为集合中的成员进行升序排序，其中的成员必须唯一的，但是其分数（score）却是可以重复的，以下查看使用区别：  
```cmd
redis 127.0.0.1:6379> zadd testZset 0 redis
(integer) 1
redis 127.0.0.1:6379> zadd testZset 0 mongodb
(integer) 1
redis 127.0.0.1:6379> > ZRANGEBYSCORE testZset 0 1000
1) "mongodb"
3) "redis"
```  
## Redis命令  
redis命令需要redis客户端环境才可以使用。其客户端语法为：
```cmd
redis-cli //此命令即可进入redis客户端环境
```
- 在远程服务上使用命令  
```cmd
redis-cli -h host -p port -a password//该形式即利用redis-cli客户端远程使用其他主机的redis服务
```
- 还有许多关乎集合的命令网上的手册。

### redis HyPerLogLog  
用于做基数统计的算法，优点是在输入元素的数量或者体积特别大的时候，计算基数所偶需要的空间总是固定的，并且很小。HyperLogLog只会根据输入元素来计算基数，而不会存储输入元素本身，所以不能返回各个元素。使用如下：
```cmd
redis 127.0.0.1:6379> PFADD runoobkey "redis"

1) (integer) 1

redis 127.0.0.1:6379> PFADD runoobkey "mongodb"

1) (integer) 1

redis 127.0.0.1:6379> PFADD runoobkey "mysql"

1) (integer) 1

redis 127.0.0.1:6379> PFCOUNT runoobkey

```
### redis 发布订阅  
Redis 发布订阅(pub/sub)是一种消息通信模式：发送者(pub)发送消息，订阅者(sub)接收消息。

Redis 客户端可以订阅任意数量的频道。使用如下：  
```cmd
//首先订阅redisChat频道
redis 127.0.0.1:6379> SUBSCRIBE redisChat

Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "redisChat"
3) (integer) 1  

//重开一个redis客户端，然后在同一频道redisChat发布两次消息，订阅者就额可以收到
redis 127.0.0.1:6379> PUBLISH redisChat "Redis is a great caching technique"

(integer) 1

redis 127.0.0.1:6379> PUBLISH redisChat "Learn redis by runoob.com"

(integer) 1



//订阅者的客户端会显示如下消息
1) "message"
2) "redisChat"
3) "Redis is a great caching technique"
1) "message"
2) "redisChat"
3) "Learn redis by runoob.com"
```  
### redis事务
1. 批量操作在发送 EXEC 命令前被放入队列缓存。
2. 收到 EXEC 命令后进入事务执行，事务中任意命令执行失败，其余的命令依然被执行。
3. 在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中
4. 使用如下：
```cmd
redis 127.0.0.1:6379> MULTI
OK

redis 127.0.0.1:6379> SET book-name "Mastering C++ in 21 days"
QUEUED

redis 127.0.0.1:6379> GET book-name
QUEUED

redis 127.0.0.1:6379> SADD tag "C++" "Programming" "Mastering Series"
QUEUED

redis 127.0.0.1:6379> SMEMBERS tag
QUEUED

redis 127.0.0.1:6379> EXEC
1) OK
2) "Mastering C++ in 21 days"
3) (integer) 3
4) 1) "Mastering Series"
   2) "C++"
   3) "Programming"
```
**值得注意的是虽然单个redis的命令执行是原子性的，但是其事务却不是原子性的**