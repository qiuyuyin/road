# Redis基础

## Redis数据结构

（[官网信息](https://www.redis.com.cn/redis-strings.html#redis-%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%91%BD%E4%BB%A4-)）

首先对redis来说，所有的key（键）都是字符串。我们在谈基础数据结构时，讨论的是存储值的数据类型，主要包括常见的5种数据类型，分别是：String、List、Set、Zset、Hash。

![img](Redis基础.assets/db-redis-ds-1.jpeg)

| 结构类型         | 结构存储的值                               | 结构的读写能力                                               |
| ---------------- | ------------------------------------------ | ------------------------------------------------------------ |
| **String字符串** | 可以是字符串、整数或浮点数                 | 对整个字符串或字符串的一部分进行操作；对整数或浮点数进行自增或自减操作； |
| **List列表**     | 一个链表，链表上的每个节点都包含一个字符串 | 对链表的两端进行push和pop操作，读取单个或多个元素；根据值查找或删除元素； |
| **Set集合**      | 包含字符串的无序集合                       | 字符串的集合，包含基础的方法有看是否存在添加、获取、删除；还包含计算交集、并集、差集等 |
| **Hash散列**     | 包含键值对的无序散列表                     | 包含方法有添加、获取、删除单个元素                           |
| **Zset有序集合** | 和散列一样，用于存储键值对                 | 字符串成员与浮点数分数之间的有序映射；元素的排列顺序由分数的大小决定；包含方法有添加、获取、删除单个元素以及根据分值范围或成员来获取元素 |

> 内容其实比较简单，我觉得理解的重点在于这个结构怎么用，能够用来做什么？所以我在梳理时，围绕**图例**，**命令**，**执行**和**场景**来阐述。@pdai

### 1.String 字符串

> String是redis中最基本的数据类型，一个key对应一个value。

String类型是二进制安全的，意思是 redis 的 string 可以包含任何数据。如数字，字符串，jpg图片或者序列化的对象。

这个字符串不是正常意义上的字符串，而是以一种键值对的形式存在。

一般来说key值是一个字符串，而value值可以是任意的一种结构。

以下是一些用于在 Redis 中管理字符串的基本命令的列表：

| 命令                                                         | 描述                                                        |
| ------------------------------------------------------------ | ----------------------------------------------------------- |
| [SET](https://www.redis.com.cn/commands/set.html)            | 设置指定 key 的值                                           |
| [GET](https://www.redis.com.cn/commands/get.html)            | 获取指定 key 的值                                           |
| [GETRANGE](https://www.redis.com.cn/commands/getrange.html)  | 返回 key 中字符串值的子字符                                 |
| [GETSET](https://www.redis.com.cn/commands/getset.html)      | 将给定 key 的值设为 value ，并返回 key 的旧值 ( old value ) |
| [GETBIT](https://www.redis.com.cn/commands/getbit.html)      | 对 key 所储存的字符串值，获取指定偏移量上的位 ( bit )       |
| [MGET](https://www.redis.com.cn/commands/mget.html)          | 获取所有(一个或多个)给定 key 的值                           |
| [SETBIT](https://www.redis.com.cn/commands/setbit.html)      | 对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)    |
| [SETEX](https://www.redis.com.cn/commands/setex.html)        | 设置 key 的值为 value 同时将过期时间设为 seconds            |
| [SETNX](https://www.redis.com.cn/commands/setnx.html)        | 只有在 key 不存在时设置 key 的值                            |
| [SETRANGE](https://www.redis.com.cn/commands/setrange.html)  | 从偏移量 offset 开始用 value 覆写给定 key 所储存的字符串值  |
| [STRLEN](https://www.redis.com.cn/commands/strlen.html)      | 返回 key 所储存的字符串值的长度                             |
| [MSET](https://www.redis.com.cn/commands/mset.html)          | 同时设置一个或多个 key-value 对                             |
| [MSETNX](https://www.redis.com.cn/commands/msetnx.html)      | 同时设置一个或多个 key-value 对                             |
| [PSETEX](https://www.redis.com.cn/commands/psetex.html)      | 以毫秒为单位设置 key 的生存时间                             |
| [INCR](https://www.redis.com.cn/commands/incr.html)          | 将 key 中储存的数字值增一                                   |
| [INCRBY](https://www.redis.com.cn/commands/incrby.html)      | 将 key 所储存的值加上给定的增量值 ( increment )             |
| [INCRBYFLOAT](https://www.redis.com.cn/commands/incrbyfloat.html) | 将 key 所储存的值加上给定的浮点增量值 ( increment )         |
| [DECR](https://www.redis.com.cn/commands/decr.html)          | 将 key 中储存的数字值减一                                   |
| [DECRBY](https://www.redis.com.cn/commands/decrby.html)      | 将 key 所储存的值减去给定的减量值 ( decrement )             |
| [APPEND](https://www.redis.com.cn/commands/append.html)      | 将 value 追加到 key 原来的值的末尾                          |

### 2.Hashes 哈希表

**Redis哈希命令**：

| 命令                                                         | 描述                                                      |
| ------------------------------------------------------------ | --------------------------------------------------------- |
| [HDEL](https://www.redis.com.cn/commands/hdel.html)          | 删除一个或多个哈希表字段                                  |
| [HEXISTS](https://www.redis.com.cn/commands/hexists.html)    | 查看哈希表 key 中，指定的字段是否存在                     |
| [HGET](https://www.redis.com.cn/commands/hget.html)          | 获取存储在哈希表中指定字段的值                            |
| [HGETALL](https://www.redis.com.cn/commands/hgetall.html)    | 获取在哈希表中指定 key 的所有字段和值                     |
| [HINCRBY](https://www.redis.com.cn/commands/hincrby.html)    | 为哈希表 key 中的指定字段的整数值加上增量 increment       |
| [HINCRBYFLOAT](https://www.redis.com.cn/commands/hincrbyfloat.html) | 为哈希表 key 中的指定字段的浮点数值加上增量 increment     |
| [HKEYS](https://www.redis.com.cn/commands/hkeys.html)        | 获取所有哈希表中的字段                                    |
| [HLEN](https://www.redis.com.cn/commands/hlen.html)          | 获取哈希表中字段的数量                                    |
| [HMGET](https://www.redis.com.cn/commands/hmget.html)        | 获取所有给定字段的值                                      |
| [HMSET](https://www.redis.com.cn/commands/hmset.html)        | 同时将多个 field-value (域-值)对设置到哈希表 key 中       |
| [HSET](https://www.redis.com.cn/commands/hset.html)          | 将哈希表 key 中的字段 field 的值设为 value                |
| [HSETNX](https://www.redis.com.cn/commands/hsetnx.html)      | 只有在字段 field 不存在时，设置哈希表字段的值             |
| [HVALS](https://www.redis.com.cn/commands/hvals.html)        | 获取哈希表中所有值                                        |
| [HSCAN](https://www.redis.com.cn/commands/hscan.html)        | 迭代哈希表中的键值对                                      |
| [HSTRLEN](https://www.redis.com.cn/commands/hstrlen.html)    | 返回哈希表 key 中， 与给定域 field 相关联的值的字符串长度 |

```xml
> hset user one 1
1
> hget user one
"1"
```

### 3.List 列表

使用List结构，我们可以轻松地实现最新消息排队功能（比如新浪微博的TimeLine）。List的另一个应用就是消息队列，可以利用List的 PUSH 操作，将任务存放在List中，然后工作线程再用 POP 操作将任务取出进行执行。

下表列出了列表相关命令：

| 命令                                                         | 描述                                                     |
| ------------------------------------------------------------ | -------------------------------------------------------- |
| [BLPOP](https://www.redis.com.cn/commands/blpop.html)        | 移出并获取列表的第一个元素                               |
| [BRPOP](https://www.redis.com.cn/commands/brpop.html)        | 移出并获取列表的最后一个元素                             |
| [BRPOPLPUSH](https://www.redis.com.cn/commands/brpoplpush.html) | 从列表中弹出一个值，并将该值插入到另外一个列表中并返回它 |
| [LINDEX](https://www.redis.com.cn/commands/lindex.html)      | 通过索引获取列表中的元素                                 |
| [LINSERT](https://www.redis.com.cn/commands/linsert.html)    | 在列表的元素前或者后插入元素                             |
| [LLEN](https://www.redis.com.cn/commands/llen.html)          | 获取列表长度                                             |
| [LPOP](https://www.redis.com.cn/commands/lpop.html)          | 移出并获取列表的第一个元素                               |
| [LPUSH](https://www.redis.com.cn/commands/lpush.html)        | 将一个或多个值插入到列表头部                             |
| [LPUSHX](https://www.redis.com.cn/commands/lpushx.html)      | 将一个值插入到已存在的列表头部                           |
| [LRANGE](https://www.redis.com.cn/commands/lrange.html)      | 获取列表指定范围内的元素                                 |
| [LREM](https://www.redis.com.cn/commands/lrem.html)          | 移除列表元素                                             |
| [LSET](https://www.redis.com.cn/commands/lset.html)          | 通过索引设置列表元素的值                                 |
| [LTRIM](https://www.redis.com.cn/commands/ltrim.html)        | 对一个列表进行修剪(trim)                                 |
| [RPOP](https://www.redis.com.cn/commands/rpop.html)          | 移除并获取列表最后一个元素                               |
| [RPOPLPUSH](https://www.redis.com.cn/commands/rpoplpush.html) | 移除列表的最后一个元素，并将该元素添加到另一个列表并返回 |
| [RPUSH](https://www.redis.com.cn/commands/rpush.html)        | 在列表中添加一个或多个值                                 |
| [RPUSHX](https://www.redis.com.cn/commands/rpushx.html)      | 为已存在的列表添加值                                     |

### 4.Set 集合

下表列出了 Redis 集合相关命令：

| 命令                                                         | 描述                                                |
| ------------------------------------------------------------ | --------------------------------------------------- |
| [SADD](https://www.redis.com.cn/commands/sadd.html)          | 向集合添加一个或多个成员                            |
| [SCARD](https://www.redis.com.cn/commands/scard.html)        | 获取集合的成员数                                    |
| [SDIFF](https://www.redis.com.cn/commands/sdiff.html)        | 返回给定所有集合的差集                              |
| [SDIFFSTORE](https://www.redis.com.cn/commands/sdiffstore.html) | 返回给定所有集合的差集并存储在 destination 中       |
| [SINTER](https://www.redis.com.cn/commands/sinter.html)      | 返回给定所有集合的交集                              |
| [SINTERSTORE](https://www.redis.com.cn/commands/sinterstore.html) | 返回给定所有集合的交集并存储在 destination 中       |
| [SISMEMBER](https://www.redis.com.cn/commands/sismember.html) | 判断 member 元素是否是集合 key 的成员               |
| [SMEMBERS](https://www.redis.com.cn/commands/smembers.html)  | 返回集合中的所有成员                                |
| [SMOVE](https://www.redis.com.cn/commands/smove.html)        | 将 member 元素从 source 集合移动到 destination 集合 |
| [SPOP](https://www.redis.com.cn/commands/spop.html)          | 移除并返回集合中的一个随机元素                      |
| [SRANDMEMBER](https://www.redis.com.cn/commands/srandmember.html) | 返回集合中一个或多个随机数                          |
| [SREM](https://www.redis.com.cn/commands/srem.html)          | 移除集合中一个或多个成员                            |
| [SUNION](https://www.redis.com.cn/commands/sunion.html)      | 返回所有给定集合的并集                              |
| [SUNIONSTORE](https://www.redis.com.cn/commands/sunionstore.html) | 所有给定集合的并集存储在 destination 集合中         |
| [SSCAN](https://www.redis.com.cn/commands/sscan.html)        | 迭代集合中的元素                                    |

### 5.Sorted Sets 有序集合

有序集合就是将HashMap中的value值修改为一个double类型的分数，可以通过分数对集合进行排序，得到系列的结果。

下表列出了 Redis 有序集合的基本命令

| 命令                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [ZADD](https://www.redis.com.cn/commands/zadd.html)          | 向有序集合添加一个或多个成员，或者更新已存在成员的分数       |
| [ZCARD](https://www.redis.com.cn/commands/zcard.html)        | 获取有序集合的成员数                                         |
| [ZCOUNT](https://www.redis.com.cn/commands/zcount.html)      | 计算在有序集合中指定区间分数的成员数                         |
| [ZINCRBY](https://www.redis.com.cn/commands/zincrby.html)    | 有序集合中对指定成员的分数加上增量 increment                 |
| [ZINTERSTORE](https://www.redis.com.cn/commands/zinterstore.html) | 计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 key 中 |
| [ZLEXCOUNT](https://www.redis.com.cn/commands/zlexcount.html) | 在有序集合中计算指定字典区间内成员数量                       |
| [ZRANGE](https://www.redis.com.cn/commands/zrange.html)      | 通过索引区间返回有序集合成指定区间内的成员                   |
| [ZRANGEBYLEX](https://www.redis.com.cn/commands/zrangebylex.html) | 通过字典区间返回有序集合的成员                               |
| [ZRANGEBYSCORE](https://www.redis.com.cn/commands/zrangebyscore.html) | 通过分数返回有序集合指定区间内的成员                         |
| [ZRANK](https://www.redis.com.cn/commands/zrank.html)        | 返回有序集合中指定成员的索引                                 |
| [ZREM](https://www.redis.com.cn/commands/zrem.html)          | 移除有序集合中的一个或多个成员                               |
| [ZREMRANGEBYLEX](https://www.redis.com.cn/commands/zremrangebylex.html) | 移除有序集合中给定的字典区间的所有成员                       |
| [ZREMRANGEBYRANK](https://www.redis.com.cn/commands/zremrangebyrank.html) | 移除有序集合中给定的排名区间的所有成员                       |
| [ZREMRANGEBYSCORE](https://www.redis.com.cn/commands/zremrangebyscore.html) | 移除有序集合中给定的分数区间的所有成员                       |
| [ZREVRANGE](https://www.redis.com.cn/commands/zrevrange.html) | 返回有序集中指定区间内的成员，通过索引，分数从高到底         |
| [ZREVRANGEBYSCORE](https://www.redis.com.cn/commands/zrevrangebyscore.html) | 返回有序集中指定分数区间内的成员，分数从高到低排序           |
| [ZREVRANK](https://www.redis.com.cn/commands/zrevrank.html)  | 返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序 |
| [ZSCORE](https://www.redis.com.cn/commands/zscore.html)      | 返回有序集中，成员的分数值                                   |
| [ZUNIONSTORE](https://www.redis.com.cn/commands/zunionstore.html) | 计算一个或多个有序集的并集，并存储在新的 key 中              |
| [ZSCAN](https://www.redis.com.cn/commands/zscan.html)        | 迭代有序集合中的元素（包括元素成员和元素分值）               |

