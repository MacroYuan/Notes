# Redis原理

**Redis分布式锁的问题？**

> set和setnx的问题

setnx指令获取到锁，expire指令给锁设置一个过期时间，当获取到锁后经过这么多的时间会自动释放锁。当然这样也存在一个问题，expire指令必须要在setnx指令获取到锁后才能执行，如果expire指令出错，释放锁的指令也没有正常执行，那么这个锁仍然被占用不能被释放。

Redis2.8版本中推出了set指令，可同时进行加锁和设计过期时间的原子操作。



## Redis持久化

### RDB

存储当前内存的快照，易恢复，数据丢失多

### AOF

存储redis构建指令，通过存储的指令能恢复当前内存状态，速度比rdb慢

### 混合持久化

定义一个存储节点，在节点前采用rdb恢复，节点后通过增量aof，重启效率相比aof大大提高



## 管道

管道是redis用于缓存指令，优化排序减少服务器端与客户端通信次数。Redis管道（Pipeline）本身并不是Redis服务器直接提供的技术，而是由客户端提供的，跟服务器没有什么直接的关系。
客户端通过对管道中的指令列表改变读写顺序就可以大幅节省IO时间。管道中指令越多，效果越好。



## 事务

Redis事务指令：`multi excc discard`

- `multi`指示业务的开始
- `excc`指示事务的执行
- `discard`指示事务的丢弃

事务中所有指令在excc前都不执行，而是缓存在服务器的一个事务队列中，服务器一旦收到excc指令，才开始执行整个事务队列，执行完毕后一次性返回所有指令的运行结果。Redis的单线程保证“原子性”。



# Redis集群

## CAP原理

C：Consistent，一致性

A：Availability，可用性

P：Partition tolerance，分区容忍性

在分布式中，当网络分区发生时，一致性和可用性两难全，要看具体场景取舍。



## 主从同步

Redis的主从数据是异步同步的，分布式的Redis系统并不需要满足**一致性**要求，只需保证**最终一致性**。当网络断开，主从节点由于异步的原因，可能出现大量数据不一致，但一旦网络恢复，从节点会采用多种策略努力追赶，继续尽力保证与主节点一致。

Redis支持主从同步和从从同步，从从同步是Redis后续版本新添加功能，以减轻主节点的同步负担。

### 增量同步

Redis同步的是指令流，即将对自己状态产生修改性影响的指令记录到本地的内存buffer中，然后异步将buffer中的指令同步到从节点中，从节点一边执行同步指令流来达到与主节点一样的状态，一边向主节点反馈自己同步到哪里了（偏移量）。

### 快照同步

快照同步是将当前内存的数据全部快照到磁盘文件中，然后将快照文件的内容全部传送到从节点。从节点接收完毕快照文件后，根据文件执行一次全量加载，加载之前要先把当前内存数据清空，加载完毕后再通知主节点执行增量同步。

> 感觉跟AOF和RDB的持久化，混合持久化方式有点像

### 增加从节点

当从节点刚刚加入到集群中的时候，它必须先进行一次快照同步，同步完成后再继续增量同步。

### 无盘复制

在Redis2.8.18版本开始，Redis支持无盘复制。无盘复制是指主服务器直接通过套接字Socket将快照内容发送到从节点，生成快照是一个遍历过程，主节点会一边遍历内存，一边将序列化的内容发送到从节点，从节点还跟之前一样，先将接收到的内容存储到磁盘文件中，再进行一次性加载。

### wait指令

Redis的复制是异步进行的，那么想要同步复制的时候怎么办呢？这时可以采用wait指令，让异步复制变为同步复制，确保系统的强一致性。

> `wait`指令是Redis3.0以后版本才出现的。

`wait(slave_node_number, time_t)`，第一个参数是从节点的数量，第二个参数是时间t，单位为ms，当等待wait指令之前的所有写操作同步到N个从节点（也就是确保N个从节点的同步没有滞后），最多等待t。如果t=0，表示无限等待直到N个从节点同步完成。



## Sentinel哨兵

哨兵发生在主从切换上，Sentinel负责持续监控主从节点的健康状态（这一点有点像zookeeper），当主节点挂掉时，自动选择一个最优的从节点切换为主节点。
客户端来连接集群时，会先连接上Sentinel，通过Sentinel来查询主节点的地址，然后再连接主节点进行数据交互。当主节点宕掉，客户端会重新向Sentinel要地址，Sentinel会将最新的主节点地址告诉客户端。

### 消息丢失

由于Redis主从消息同步是异步的，当主从切换的时候可能存在消息丢失的问题，从节点可能没有收到全部的同步消息。如果主从延迟比较大，那么这部分丢失的数据就会越多。Sentinel只能保证消息少丢失：

```redis
min-slaves-to-write 1
min-slaves-max-lag 10
```

第一个参数表示节点必须至少有一个从节点在进行正常复制，否则就停止对外写服务，丧失可用性。
第二个参数单位为秒，表示在10s内没有收到从节点的反馈，就意味着从节点同步不正常，要么是网络断开了，要么是一直没有反馈。



## Codis

前豌豆荚中间件团队Redis集群方案

## Squirrel

美团Redis集群方案



# Redis新特性

## Stream



# 源码解析

## 字符串

Redis的字符串结构是一个SDS(Simple Dynamic String)，是一个带长度信息的字节数组。

```c
struct SDS<T> {
    T capacity;		//数组容量
    T len;			//数组长度
    byte flags;		//特殊标志位，不用理睬
    byte[] content;	//数组内容
}
```

SDS的capacity和len使用泛型T而不是int的原因：当字符串比较短的时候，len和capacity可以使用byte和short表示，对内存做到极致的优化。

```c
sds sdacatlen(sds s, const void *t, size_t len) {
    size_t curlen = sdslen(s);		//原字符串长度
    
    // 按需调整空间，如果capacity不够容纳追加的内容，就会重新分配字节数组并复制原字符串的内容到新数组中
    s = sdsMakeRoomFor(s, len);
    if (s == NULL) return NULL;		//内存不足
    memcpy(s+curlen, t, len);		//追加目标字符串的内容到字节数组中
    sdssetlen(s, curlen+len);		//设置追加后的长度
    s[curlen+len] = '\0';			//让字符串以\0结尾，便于调试打印，还可以直接使用glibc的字符串函数进行操作
    return s;
}
```

**Redis规定字符串的长度不得超过512MB**。

### embstr VS raw

Redis的字符串有两种存储方式，在长度非常短的时候，使用embstr形式存储（embeded），而长度超过44字节时，采用raw形式存储。

## 字典

```c
struct RedisDb {
    dict* dict;
    dict* expires;
    ...
}

struct zset {
    dict *dict;
    zskiplist *zsl;
}
```

字典内部结构包含两个hashtable，通常情况下一个hashtable是有值的，但是在字典扩容缩容时，需要分配新的hashtable，然后进行渐进式搬迁，这时另一个hashtable起temp的作用。待搬迁结束后，旧的hashtable被删除，新的hashtable取而代之。

```c
struct dict {
    ...
    dictht ht[2];
}
```

hashtable的结构和Java的HashMap几乎一样。

```c
struct dictEntry {
    void* key;
    void* val;
    dictEntry* next;	//链接下一个dictEntry
}

struct dictht {
    dictEntry** table;  //二维
    long size;			//第一维数组的长度
    long used;			//hash表中的元素个数
}
```

### set

Redis里的set的底层结构实现就是字典，只不过所有的value都是NULL，其他特性和字典一模一样。

## 压缩列表

## 快速列表

## 跳表

