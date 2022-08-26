### REDIS数据库

**redis特点：

```
1. 开源项目，使用C编写，给予内存且支持持久化
2. 高性能key-Value的NoSQL数据库
3. 支持数据类型丰富
4. 支持多种编程语言
5. 单进程单线程
```

**Redis应用场景**

```
1. 缓存
2. 并发计数  点赞/秒杀/带有效期验证码
3. 排行榜
4. 生产者消费者模式
```

**Redis附加功能**

```
1. 持久化
   将内存数据保存到磁盘中，保证数据安全，方便进行数据备份和恢复
2. 过期键功能
3. 事务功能（弱项）
4. 主从复制
5. sentine哨兵
```

### Redis安装和配置

```
安装：sudo apt-get install redis-server
服务启动 sudo /etc/init.d/redis-server    start|stop|restart
客户端链接：redis-cli -h ip地址 -p 6379 -a 密码  
```

```
配置文件位置：/etc/redis/redis.conf
增加密码：requirepass  123456
----增加远程连接----
1. 远程链接：# bind 127.0.0.1 ::1  注释掉可以远程
2. 关闭保护模式：protected-mode no   yes改为no
3. 重启服务
```

```
redis-cli 进入后 ping一下确认连接上
```

### 常用命令

| select number      | 切换库（db0~db15）                      |
| ------------------ | --------------------------------------- |
| set 键 值          | 设置键值                                |
| set 键 值 nx       | 如果存在值则设置不成功                  |
| set 键 值 ex       | 设置过期事件 秒单位                     |
| mset 键 值 键 值   | 设置多个键值                            |
| get 键             | 获取值                                  |
| mget  键  键       | 获取多个值                              |
| keys *（表达式）   | 查看键（*通配符，慎用）                 |
| del 键             | 删除键                                  |
| type 键            | 数据类型键                              |
| rename 键 新的健名 | 重命名                                  |
| exist 键           | 键是否存在                              |
| flushdb            | 清除当前库所有数据（慎用）              |
| del 键             | 删除键                                  |
| flushall           | 清除所有库的数据（慎用）                |
| rename 键 新的健名 | 键重命名                                |
| flushdb            | 清除当前库所有数据（慎用）              |
| flushall           | 清除所有库的数据（慎用）                |
| ttl  键            | 查看数据存活时间 -1表示长期  -2表示过期 |

### 数据类型

**1 字符串，数字都会转换成字符串存储**

| strlen 键             | 获取长度         |
| --------------------- | ---------------- |
| getrange 键 开始 结束 | 切片获取         |
| setrange 键 下标 新值 | 下标替换         |
| expire 键             | 已存在指定有效期 |
| PERSIST 键            | 删除过期键       |
|                       |                  |

**2 整数类型**

| incrby key 步长 | 增量，每执行一次增加步长，键不存在会自动创建 |
| --------------- | -------------------------------------------- |
| decrby key 步长 | 减量                                         |
| incr key        | 默认+1                                       |
| decr key        | 默认-1                                       |



**3 列表**

列表元素是字符串类型，列表头尾增删快，中间增删慢，增删元素是常态，元素不可重复，最多包含2^32 -1 索引如python列表；

**列表常用命令：**

左头右尾，头朝上

| 命令                              | 功能                                                        |
| --------------------------------- | ----------------------------------------------------------- |
| LPUSH 键  值  值                  | 从列表头部压入元素，返回长度                                |
| RPUSH 键   值  值                 | 从列表尾部压入元素，返回长度                                |
| RPOPLPUSH  列表1 列表2            | 从列表1尾部删除，添加进列表2头部  返回被弹出的元素          |
| LRANGE  键  开始 结束             | 遍历列表 0  -1                                              |
| LINSERT 键 after\|before  值 新值 | 在值后面或前面插入新值  after后 before前                    |
| llen                              | 获取列表长度                                                |
| LPOP 键                           | 从头部删除一个元素                                          |
| RPOP 键                           | 从尾部删除一个元素                                          |
| BLPOP 键  超时时间                | 以阻塞形式弹出头部   多个阻塞先到先得 返回被弹出的键，元素  |
| BRPOP 键   超时时间               | 以阻塞形式弹出尾部  返回被弹出的元素                        |
| LREM  键  count 要删除的元素      | 删除制定元素，count>0 头部向尾部开始搜索                    |
|                                   | count<0 尾部向头部搜索   count=0 移除所有与要删除相等的元素 |
| LTRIM 键 开始 结束                | 保留范围内的元素                                            |
| LSET 键 下标 新值                 | 按下标更改新值                                              |

**生产者消费者模式**

list 任务队列/消息队列，放任务的是生产者，取任务的是消费者，消息队列保存任务

生产者 —》消息队列—》消费者

其他消息队列软件：rabbitmq

### python控制

```
安装命令：sudo pip3 install redis
查看安装：sudo pip3 freeze|grep -i 'redis'
```

```
import redis

import redis
#创建redis数据库链接对象
r=redis.Redis(password='123456',db=15)
#使用字符串，返回的是字节串
print(r.keys("*").decode())
print(r.mset({"ze":3,'ce':4}))  
print(r.mget(['ze','ce']))

总结：python操作与redis操作命令一致！
```



### 位图操作

位图操作任然数据字符串操作，只不过操作的粒度更细了，一般字符串操作最小的单位是字节1byt=8bit。字符串类型中的位图操作，最小单位为bit位。使用位图极大地节省内存空间。适用于开关量。采用二进制

| 命令                | 功能                               |
| ------------------- | ---------------------------------- |
| SETBIT  键  位置 值 | 更改值的二进制位                   |
| GETBIT 键           |                                    |
| BITCOUNT 键 区间    | 统计有多少个0或1（区间只能是0或1） |
|                     |                                    |

### Hash散列

| 基本命令                    | 功能             |
| --------------------------- | ---------------- |
| HSET 键  字段  值           | 设置单个字段     |
| HSETNX  键  字段  值        | 当键不存在时设置 |
| HMSET  键 字段  值 字段  值 | 设置多个值       |
| HLEN 键                     | 查看有多少字段   |
| HGET 键  内键               | 获取制定的值     |
| HGETALL  键                 | 获取所有字段和值 |
| HKEYS 键                    | 返回所有的字段   |
| HVALS 键                    | 返回所有的值     |
| HDEL  键 字段               | 删除指定字段     |
| HINCREBY  键 字段 增量      | 对字段整数增量   |
| HINCRBYFOLAT  键 字段 增量  | 对浮点数增增量   |
|                             |                  |

**优点**: 节省空间，特定条件下，字段小于512个，value不能超过64字节

理解：字典类型的数据，相当于字典内再套一层字典。过期键只能功能只能对键操作，不能对散列字段设置过期

### 无需集合

特点：无序，去重

| 命令                         | 功能                                                  |
| ---------------------------- | ----------------------------------------------------- |
| SADD 键 值1 值2              | 增加1个或多个元素，自动去重，返回值插入集合的元素个数 |
| SMEMBERS 键                  | 查看集合所有元素                                      |
| SREM 键 值1 值2              | 删除1个或多个元素                                     |
| SRANDMEMBER 键               | 随机返回集合内一个元素                                |
| SISMEMBER  键                | 是否存在于集合中，返回1，0                            |
| SPOP 键 数量                 | 随机删除                                              |
| SCARD 键                     | 返回集合中元素个数                                    |
| SMOVE  源  目标  值          | 从一个集合移动到另外一个集合                          |
| SDIFF  键1 键2               | 差集（去除交集后的值）前后有差别                      |
| SUNION  键1 键2              | 并集（两组所有的值）                                  |
| SINTER 键1 键2               | 交集（两组交集部分）                                  |
| SUNIONSTORE  存储键  键1 键2 | 保存并集结果到新的集合                                |

### 有序集合

| 命令                                                         | 功能                                       |
| ------------------------------------------------------------ | ------------------------------------------ |
| ZADD 键  分值 值                                             | 添加有序集合，重复添加返回0                |
| ZRANGE 键  开始 结束  是否显示分值                           | 指定区间升序                               |
| ZREVRANGGE 键 开始 结束  是否显示分值                        | 指定区间降序                               |
| ZSCORE 键 值                                                 | 查看指定元素的分值                         |
| zranggebysore  键  最小值  最大值 [withscores] [limit offset 跳过的数量]（从第几个开始，每次取几个） | 返回指定区间元素，加入边界在值前面或后面（ |
| ZREM  键  值                                                 | 删除值，返回删除后的升序排列               |
| ZINCRBY 键  增加数 值                                        | 增加减少分值                               |
| zrank 键 值                                                  | 返回元素的排名                             |
| zrevrank 键 值                                               | 返回元素的反向排名                         |
| zremrangebyscore 键  最小值  最大值                          | 删除指定范围内的元素                       |
| zcard 键                                                     | 返回集合中元素个数                         |
| zcount  键  键  小值  大值                                   | 返回指定范围中元素的个数                   |
| zunionstore 新键 参与数 键1 键2 [weights 权重值 权重值] aggregate  sum\|min\|max | 并集运算，权重值（分值与权重值相乘）       |

### 事务命令

不保证原子性，执行失败，其他命令依然会被执行，没有回滚机制。redis事务是对任务放入队列中，统一提交到redis进行逐一处理。

| 命令    | 功能                                     |
| ------- | ---------------------------------------- |
| MULTI   | 开启事务                                 |
| EXEC    | 提交到数据库执行                         |
| DISCARD | 取消事务（取消命令的执行，删除任务队列） |

客户端使用pipeline流水线，可以减少io。打包发送请求。

### 连接池

### watch-乐观锁

事务过程中，可对指定的key进行监听，命令提交时若被监听的键值未被修改时，事务方可提交成功，否则失败。事务开启前监控键。

ps：监听功能，如果被监控的键的值改变了，就不会执行成功。仅监听，不会锁定值，

**python中使用**

```python
import time
import redis
# 创建链接池
pool=redis.ConnectionPool(password='123456')  
# 连接进入池
r=redis.Redis(connection_pool=pool)
def double_account(user_id):
    key='account_%s'% user_id
    # 进入管道，处理事务
    with r.pipeline(transaction=True) as pipe:
        while True:
            try:
                # 监听键
                pipe.watch(key)
                value=int(r.get(key))
                value*=2
                print('新的值%s'%value)
                print('----sleep is start')
                time.sleep(20)
                print('----sleep is over')
                # 开启事务模式
                pipe.multi()
                pipe.set(key,value)
                # 执行事务队列
                pipe.execute()
                break
            except redis.WatchError:
                print('------值发生了改变------')
                continue
    return int(r.get(key))

if __name__ == '__main__':
    double_account('tedu')
```



### 数据持久化：

#### rdb模式：

1、保存真实的数据

2、将服务器包含的所有数据以二进制形式保存在硬盘

3、默认文件名：/var/lib/redis/dump.rdb

 如果不存在使用命令：

save   项目部署状态下不适用。会造成redis服务被阻塞。如果文件已存在会替代旧的rdb文件； 

DGSAVE   redis会创建子进程处理，不会导致redis阻塞，建议使用这个。执行备份后rdb文件会被覆盖然后重新读取。



配置文件相关

/etc/redis/redis.conf

263 行  文件存储路径

253行 文件名称

该模式为快照备份模式，



方式二（使用最多）：

自动备份：系统默认会自动备份

218行 save 900 1

219行 save 300 10

表示300秒内修改大于10次 自动备份  900秒内修改不小于1次 自动备份。

**rdb模式缺点**：

rdb模式是全量备份，不要过于频繁执行，会严重影响服务器性能。任然有可能丢失数据。使用redis结合mysql做缓存情况下，可以忽略这个问题，因为数据本来就在mysql里面。

#### AOF模式：

1、存储的是命令而不是真实数据

2、默认不开启

开启方式: /etc/redis/redis.conf

672行：appendonly  yes  #默认no

500行： requirepass  密码注销掉

优点：redis在遭遇意外宕机时，不会丢失任何数据，或者只丢失1秒数据。



策略配置：

701 alwarys    每写一行存储一次

702 everysec  服务器每一秒存储一次命令

703 no   不主动写入硬盘，由系统决定合适将缓冲区命令写入硬盘

AOF是增量存储，一次保存一个命令，可以频繁执行。

**AOF重写：**

命令方式：BGREWRITEAOF

配置自动执行：

auto-aof-rewrite-percentage  100

auto-aof-rewrite-min-size   64mb

解释：当aof文件增量大于100%时才进行重写，也就是大一倍时才出发，redis来存储真是数据，每一条都不丢失，都要用alwarys。rdb和aof根据业务场景选择。文件内有rdb和aof两个文件时先找aof



### redis主从复制

1、一个redis服务可以有多个该服务的复制品。这个redis服务成为master，其他复制品成为slaves

2、master 会一直将自己的数据同步给slaves，保持同步

3、只有master 可以执行写命令，slave只能执行读命令

作用

分担读的压力实现高并发

```
实现方式：

方式1：
linx命令行：redis-server   --port 6300  --slaveof 127.0.0.1 6379
链接：redis-cli -p 6300 

方式2：
命令启动新服务：redis-server   --port 6301
进入服务：redis-cli -p 6301
输入命令：slaveof 127.0.0.1 6379 

slaveof no one  脱离再次转换成主

第三种方式：
6379 -> /etc/redis/redis.conf
6302 -> /home/用户目录/redis_6300.conf

1.创建配置文件：
vi redis_6300.conf
slaveof 127.0.0.1 6379   --》指定主
port 6302               --》指定自己的端口号
直接进入vi编写新文件。
2.启动服务
命令启动 redis-server  文件位置 
```

解释：可以开启多个redis服务分担压力，新开启的服务指定主redis进行复制。从只能进行读操作。

#### master挂了怎么办：

没有哨兵情况下：

1.一个master 可以有多个slaves 

2.slaves挂了只会影响读的效率

3.master挂了会影响写

3.当master挂了，可以给其中一个slaves 执行 slaveof no one

4.其他slaves执行 slaveof 127.0.0.1 新主

### redis哨兵：

哨兵模式会一直监控主和从的状态，可以监控任意多个主从。只存在一个哨兵有可能出现误判，哨兵数量为奇数。

```
安装哨兵模块：sudo apt install redis-sentinel
验证：sudo /etc/init.d/redis-sentinel stop

新建配置文件sentinel.conf
port 26379
sentinel monitor tedu 127.0.0.1 6379 1

启动：redis-sentinel sentinel.conf
启动2：redis-server sentinel.conf --sentinel
```

```
哨兵配置详解：
port XXX 监听端口
sentinel monitor 主名称 ip 端口 条数
sentinel auth-pass 主名称 密码     # 有密码的情况加入
sentinel down-after-milliseconds 主名称 时长   # 主失去链接多久判定为死亡
```

