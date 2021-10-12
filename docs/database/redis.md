

### 运维常用

#### 常用配置设置

```
127.0.0.1:6379> CONFIG GET *
127.0.0.1:6379> config get maxmemory
127.0.0.1:6379> config set maxmemory 4000000000 #4G
```



#### docker快速启动

```bash
#https://hub.docker.com/_/redis/
docker run --name redis -p 6379:6379 -d redis:3.2 redis-server
docker run --name ardb -p 16379:16379 -d lupino/ardb-server
```



#### yum安装

```bash
#Myenv:Amazon Linux AMI release 2017.09
#https://redis.io/download
rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
yum --enablerepo=epel -y install redis
```



#### rocksdb参数(ardb乱入)

> - [https://rocksdb.org.cn/doc/Write-Ahead-Log.html](https://rocksdb.org.cn/doc/Write-Ahead-Log.html)

```
rocksdb.options               write_buffer_size=2048M;max_write_buffer_number=5;min_write_buffer_number_to_merge=2;compression=kSnappyCompression;\
                              bloom_locality=1;memtable_prefix_bloom_size_ratio=0.1;\
                              block_based_table_factory={block_cache=512M;filter_policy=bloomfilter:10:true};\
                              create_if_missing=true;max_open_files=60000;rate_limiter_bytes_per_sec=200M;\
                              use_direct_io_for_flush_and_compaction=true;use_adaptive_mutex=true;max_total_wal_size=20480M
```



----



### 数据结构

------

Redis的5种数据结构。（STRING、LIST、SET、HASH、ZSET）

| 类型   | 存储                                                         | 结构的读写能力                                               |
| :----- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| STRING | 可以是字符串、整数或浮点数                                   | 对整个字符串或者字符串的一部分执行操作； 对整数和浮点数执行自增（increment）或自减（decrement）操作 |
| LIST   | 一个链表，链表上的每个节点都包含了一个字符串                 | 从链表的两端推入或者弹出元素； 根据偏移量对链表进行修剪（trim）； 读取单个或多个元素； 根据值查找或移除元素 |
| SET    | 包含字符串的无序收集器（unordered collection），并且被包含的每个字符串都是不同的 | 添加、获取、移除单个元素； 检查一个元素是否存在于集合中； 计算交集、并集、差集； 从集合里面随机获得元素 |
| HASH   | 包含键值对的无序散列表                                       | 添加、获取、删除单个键值对； 获取所有键值对                  |
| ZSET   | 字符串成员（member）与浮点数分值（score）之间的有序映射，元素的排列顺序由分值的大小决定 | 添加、获取、删除单个元素； 根据分值范围（range）或者成员来获取元素 |



### 基础命令

#### string命令(可以存储字节串、整数、浮点数）

```
set hello world
get hello
del hello
```

#### extend

```
conn = redis.Redis()
conn.get('key')
comm.incr('key', 15) #+15
conn.decr('key', 5)  #-5
```

#### list命令

```
rpush list-key item
rpush list-key item2
lrange list-key 0 -1
lindex list-key 1
lpop list-key
lrange list-key 0 -1
```

BLPOP/BRPOP，对于阻塞弹出命令和弹出并推入命令，最常用的就是消息传递和任务队列（task queue）。

set命令,set与list的不同，list可以存储相同的字符串

```
sadd set-key item
sadd set-key item2
sadd set-key item3
smembers set-key
sismember set-key item
srem set-key item2
smembers set-key
```

#### hash命令

```
hset hash-key sub-key1 value1
hset hash-key sub-key2 value2
hset hash-key sub-key3 value3
hgetall hash-key #返回散列包含的所有键值对
hdel hash-key sub-key2
hgetall hash-key
hmget key-name key1 key2
hmset key-name key1 value1 key2 value2
hlen key-name #返回散列包含的键值对数量
hexists key-name key #检查给定键是否存在于散列
hkeys key-name #获取散列包含的所有键
hvals key-name #获取散列包含的所有值
hincrby key-name key increment #将键key存储的值加上整数incremenet
hincrbyfloat key-name key increment #将键key存储的值加上浮点数incremenet
```

#### zset命令

```
zadd zset-key 728 member1
zadd zset-key 982 member0
zrange zset-key 0 -1 withscores
zrangebyscore zset-key 0 800 withscores
zrem zset-key member1
zrange zset-key 0 -1 withscores
```



### 应用

一般用来登录的cookie，有两种类型

- 签名cookie（singed cookie）
- 令牌cookie

| cooked类型 | 优点                                                         | 缺点                                                         |
| :--------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 签名cookie | 验证cookie所需的一切信息都存储在cookie里面。cookie可以包含额外的信息，并且对这些信息进行签名也很容易 | 正确的处理签名很难。很容易忘记对数据进行签名，或者忘记验证数据的签名，从而造成安全漏洞 |
| 令牌cookie | 添加信息非常容器。cookie的体积非常小，因此移动终端和速度较慢的客户端可以更快的发送请求 | 需要在服务器端存储更多的信息。如果使用的是关系型数据库，那么存储和载入cookie的代价可能会很高。 |



### 发布和订阅

即publish/subscribe

一般来说，发布和订阅的特点是订阅者（listener）负责订阅频道（channel），发送者（publisher）负责向频道发送二进制字符串消息（binary string message）。每当有消息被发送给指定频道时，频道的所有订阅者都会收到消息。

Reids提供的5个发布与订阅命令

| 命令         | 用例                                 | 描述                                                         |
| :----------- | :----------------------------------- | :----------------------------------------------------------- |
| SUBSCRIBE    | subscribe channel [channel ...]      | 订阅给定的一个或多个频道                                     |
| UNSUBSCRIBE  | unsubscribe [channel [channel ...]]  | 退订给定的一个或多个频道，如果执行时没有给定任何频道，那么退订所有的频道 |
| PUBLISH      | publish channel message              | 向给定频道发送消息                                           |
| PSUBSCRIBE   | psubscribe pattern [pattern ...]     | 订阅与给定模式相匹配的所有频道                               |
| PUNSUBSCRIBE | punsubscribe [pattern [pattern ...]] | 退订给定模式，如果执行时没有给定模式，MAME退订所有模式       |



### Redis事务

Redis的基本事务（basic transaction）需要用到MULTI命令和EXEC命令，这种事务可以让一个客户端在不被其他客户端打断的情况下执行多个命令。与关系型数据库的可以在执行过程中进行回滚（rollback）的事务不同，在redis里，被multi和exec命令包围的所有命令会一个借一个的执行，直到所有命令执行完毕为止。当一个事务执行完毕后，Reids才会处理其他客户端的命令。



### 键的过期时间

用于处理过期时间的redis命令

| 命令      | 示例                                      | 描述                           |
| :-------- | :---------------------------------------- | :----------------------------- |
| PERSIST   | persist key-name                          | 移除键的过期时间               |
| TTL       | ttl key-name                              | 查看指定键距离过期还有多少秒   |
| EXPIRE    | expire key-name seconds                   | 让指定键在指定的秒数之后过期   |
| EXPOREAT  | expireat key-name timestamp               | 让指定键在指定的unix时间戳过期 |
| PTTL      | pttl key-name                             | 查看给定键距离过期还有多少毫秒 |
| PEXPIRE   | pexpire key-name milliseconds             | 指定毫秒后过期                 |
| PEXPIREAT | pexpireat key-name timestamp-milliseconds | 毫秒级的unix时间戳过期         |



### 安全及持久化

持久化

- point-in-time dump（即快照snapshotting RDB）。
  - 指定时间内有指定的操作时执行
  - 通过调用两条转储到硬盘(dump-to-disk)命令中的任意一条
- append-only（即AOF）。
  - 可设置从不同步、每秒一次、每个写命令一次

快照持久化选项

```
save 60 1000
stop-writes-on-bgsave-error no #持久化失败后是否仍然继续执行写命令
rdbcompression yes
dbfilename dump.rdb
```

创建快照的常见方法

- BGSAVE命令：windows平台不支持。redis会调用fork来创建一个子进程，然后子进程负责将快照写入硬盘，而父进程则继续处理命令请求
- SAVE命令：接到save命令的redis服务器在快照创建完毕之前将不再响应任何其他命令。save命令并不常用，通常在没有足够内存去执行bgsave命令的情况下，或者即使等待持久化操作执行完毕也无所谓的情况下才会去使用
- 配置设置的save配置选项：如`save 60 10000`，表示从redis最近一次创建快照之后开始算起，当”60秒之内有10000次写入“这个条件被满足时，redis就会自动触发bgsave命令。如果有多个save配置，那么任一条件满足就会触发一次bgsave
- 当redis通过shutdown命令接收到关闭服务器请求时，或接收到标准的TERM信号时，会执行一个save命令，阻塞所有客户端，不再执行客户端发送的任何命令，并在save命令执行完毕之后关闭服务器
- 当一个redis服务器连接另一个redis服务器，并向对方发送SYNC命令来开始一次复制操作的时候，如果主redis目前没有在执行bgsave操作，或主redis并非刚刚执行完bgsave操作，那么主服务器就会执行bgsave

> 执行bgsave而导致的停顿时间取决于redis所在的系统

- 真实硬件、VMware、KVM：Redis进程每占用1GB内存，创建该进程的子进程所需要的时间就要增加10-20毫秒
- Xen虚拟机：每1GB，增加时间200-400毫秒

AOF持久化选项

```
appendonly no
appendfsync everysec
no-appendfsync-on-rewrite no
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

#当aof文件体积大于64MB，并且aof文件的体积比上一次重写之后的体积大了至少1（100%）倍的时候，redis将执行bgrewriteaof命令
```

appendfsync的选项及同步频率

| 选项     | 同步频率                                                     |
| :------- | :----------------------------------------------------------- |
| always   | 每个Redis写命令都要同步写入硬盘。这样会严重降低redis的速度，不推荐 |
| everysec | 每秒执行1次同步，显式的将多个写命令同步到硬盘，建议用这个    |
| no       | 让操作系统来决定应该何时进行同步，不推荐                     |

> 如果选择always选项，一般机械硬盘每秒只能处理200个写命令，固态硬盘每秒大概也只能处理几万个写命令

AOF的主要问题是备份文件的大小会越来越大。可以向redis发送`BGREWRITEAOF`命令，这个命令会通过移除AOF文件中的冗余命令来重写AOF文件，使AOF文件的体积变的尽可能的小。



### Redis主从

注意

- 实际中最好还是让主服务器只适用50%-65%的内存，留下30%-45%的内存用于执行bgsave命令和创建记录写命令的缓冲区
- 从服务器在进行同步时会清空自己的所有数据
- redis不支持主主复制

同步过程

| 步骤 | 主服务器操作                                                 | 从服务器操作                                                 |
| :--- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 1    | （等待命令）                                                 | 连接主服务器，发送sync命令                                   |
| 2    | 开始执行bgsave，且使用缓冲区记录bgsave之后执行的所有写命令   | 根据配置选项来决定是继续使用现有数据处理请求，还是向请求者返回错误 |
| 3    | bgsave完成，向从服务器发送rdb文件，且在发送期间依然用缓冲区记录发送期间的写命令 | 丢弃所有旧数据，开始载入主服务器发来的rdb                    |
| 4    | 开始将缓冲区里的写命令发送给从服务器                         | rdb载入完成，开始接受命令请求                                |
| 5    | 缓冲区命令发送完毕；然后每执行一个新的写命令都会向从服务器发送相同的命令 | 执行主服务器发来的缓冲区所有命令，并从现在起，接受并执行主服务器发来的每个写命令 |

配置文件中配置SLAVEOF和命令行SLAVEOF的区别：

- SLAVEOF配置：会首先载入当前可用的RDB或者AOF，载入后才开始同步过程
- SLAVEOF命令：直接开始尝试连接主服务器，并开始同步过程

主从链：从服务器可以拥有自己的从服务器，这就构成了主从链。如果从X拥有从Y，那么当从X在跟自己master同步时，会断开与从Y的连接，导致从Y需要重新连接并重新同步（rsync）。主从链主要为了减轻多从服务器时主服务器的同步压力。

可以通过INFO命令的输出结果`aof_pending_bio_fsync`如果是0，则表示服务器已经将已知的数据都保存到硬盘了。

验证备份文件

```
redis-check-aof file.aof
redis-check-dump dump.rdb
```

备份文件修复，只能修复aof，rdb不支持 修复命令`redis-check-aof --fix file.aof`，原理就是删除出错写指令及之后的所有写指令，一般也就是删除aof文件末位的不完整写命令

为什么Redis没有典型的加锁功能？不是没有锁，只是锁是乐观锁

- 传统关系型数据库的锁功能，缺点在于持有锁的的客户端越慢，等待解锁的客户端被阻塞的时间越长
- redis为了尽可能的减少客户端的等待时间，并不会在执行watch命令时对数据进行加锁。相反，redis只会在数据已经被其他客户端抢先修改的情况下，通知执行了watch命令的客户端，这是乐观锁（optimistic locking）。

redis性能测试命令

```
redis-benchmark -c 1 -q
```

关于测试结果

- 一般不使用pipeline流水线的话，python客户端的性能大概是redis-benchmark显示性能的50%-60%
- 若自己客户端性能只有redis-benchmark的30%以下或者返回`Cannot assign requested address`错误，可能是每次发送命令时都创建了新的连接。



### 应用示例

传统日志记录

- 直接写log文件
- 通过系统syslog服务

syslog的守护进程推荐使用syslog-ng替代rsyslogd
