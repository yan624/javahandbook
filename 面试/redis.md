## redis和memcached区别
Redis 用的是“单线程+多路IO复用”机制，支持持久化，支持多种数据类型。

memcached 用的是“多线程+锁”机制，不支持持久化，只支持一种数据类型。

## redis针对键的命令
1. keys *：查询数据库中所有的键
2. exists [key]：判断key是否存在
3. type [key]：查看key的类型
4. del [key]：删除key，立即删除
5. unlink [key]：非阻塞删除 key，仅将 key 从 keyspace 元数据中删除，真正的删除会在后续执行异步操作
6. expire [key] [过期时间]：给 key 设置过期时间，单位秒
7. ttl [key]：查看 key 还剩几秒过期，-1 表示永不过期，-2 表示已过期
8. select 1：切换 redis 数据库
9. dbsize：查看数据库中有多少个 key
10. flushdb：清空当前数据库
11. flushdb：清空全部数据库

## redis字符串（string）
String 类型是二进制安全的，意味着 redis 的 String 可以包含任意的数据。比如图片、序列化的对象、视频等，只要把它们表示成二进制的字符串即可。

String 是 redis 的基本数据类型，字符串**值**（value）的最大存储内存是 512 M。

1. set [key] [value]：存储
2. get [key]：读取
3. append [key] [value]：在 key 后面追加 value，并返回字符串总长度
4. strlen [key]：返回字符串长度
5. setnx [key] [value]：只有在 key 不存在时，才会设置值
6. incr [key]：将 key 对应的数字值加 1，如果为空，新增值为 1
7. decr [key]：
8. incrby [key] [步长]：结果为数字值加上步长
9. decrby [key]：

**原子性**

incr 是原子操作，不会被**线程调度机制**打断。这里的原子操作是 redis 的特有名词。

1. mset [key1] [value1] [key1] [value1]：设置多个键值对
2. mget [key1] [key2]：通过多个 key 获得多个值
3. msetnx [key1] [value1] [key1] [value1]：只有在这些 key 不存在时，才会同时成功设置这些值。具有**原子性**，只要有一个 key 存在，就全部设置失败。
4. getrange [key] [起始位置] [结束位置]：获取某个范围的值，类似于 java 的 substring。前闭后闭。
5. setrange [key] [起始位置] [value]：在某个位置开始覆盖值
6. setex [key] [过期时间] [value]：设置键值对的同时，设置过期时间，单位秒
7. getset [key] [value]：以新值换旧值，设置新值同时返回旧值

String 的数据结构类似于 ArrayList，在空间不够时会自动扩容。

一般来说 capacity（容量）会比 len（字符串长度）稍微长一点。但是当超过 1 兆时，每次扩容会加一倍。例如当容量为 1 兆时，扩容后的容量是 2 兆；容量为 2 兆，扩容后就是 4 兆。最大只能是 512 兆。

## redis列表（List）
redis 列表是一个简单的字符串列表，按照插入顺序排序。你可以将元素添加到列表的头部或尾部。

redis 列表实际上就是一个双向列表。

1. lpush / rpush [key] [value1] [value2]：从左侧或右侧压入值，`lpush k1 v1 v2 v3`，那么列表中的值就是 v3 v2 v1
2. lpop / rpop [key]：从左侧或右侧弹出值。当值全部弹出时，键就不存在了
3. rpoplpush [key1] [key2]：从 key1 的右边弹出一个值，压入 key2 的左边
4. lrange [key] [start] [stop]：按索引获取值，如果索引时 0 -1 就获得所有数据
5. lindex [key] [index]：获取指定索引的值
6. llen [key]：获取列表的长度
7. linsert [key] before / after [value] [newvalue]：在 value 的前面或后面插入 newvalue
8. lrem [key] [n] [value]：从左删除 n 个 value
9. lset [key] [index] [balue]：将下标为 index 的值替换为 value


List 的数据结构是快速列表（quicklist）。

在列表元素较少的时候会使用一块连续的内存，称之为压缩列表（ziplist）。当数据量比较大时，才会使用 quicklist。

这是因为普通的链表需要很多额外的指针空间，比较浪费内存。

下面的就叫做 quicklist：

ziplist <-> ziplist <-> ziplist <-> ziplist

## redis集合（set）
Redis set 与 list 类似，区别在于它具有自动排重的功能，并且是无序的。

1. sadd [key] [value1] [value2]：将一个或多个元素加入集合
2. smembers [key]：取出该集合中所有的值
3. sismember [key] [value]：判断该集合中是否存在 value
4. scard [key]：返回该集合中的个数
5. srem [key] [value1] [value2]：删除集合中的某些元素
6. spop [key]：随机从集合中弹出一个值，如果集合中没有值了，那么集合也就不存在了
7. srandmember [key] [n]：随机从该集合中取出 n 个值，但是不会删除
8. smove [source] [destination] [value]：把集合中的一个值移动到另一个集合
9. sinter [key1] [key2]：返回两个集合的交集
10. sunion [key1] [key2]：返回两个集合的并集
11. sdiff [key1] [key2]：返回两个集合的差集

set 的数据结构是哈希表。

## redis哈希（hash）
Redis hash 是一个键值对集合。存储的是 field 和 value 的映射表，特别适合存储对象。

类似于 java 的 Map<String, Object>。

比如有一个对象 {id: 1, name: zhangsan, age: 20}。现在要把它存储到 redis。

第一种（把对象视为字符串）：user = {id: 1, name: zhangsan, age: 20}     不太好修改某个属性

第二种（分开存储）：user:id 1 | user:name zhangsan | user:age 20        数据太分散

第三种（用 hash 存储）： 

```
            id   1
user  ->		name 张三
						age  20
```

1. hset [key] [field] [value]：给哈希表中的 field 键赋值 value
2. hget [key] [field]：取值
3. hmset [key] [field1] [value1] [field2] [value2]：批量
4. hexists [key] [field]：查找 field 是否存在
5. hkeys [key]：查询所有 field
6. hvals [key]：查询所有 value
7. hincrby [key] [field] [increment]：加上增量
8. hsetnx [key] [field] [value]：仅当 field 不存在时，给哈希表中的 field 键赋值 value

hash 的数据结构有两种：压缩列表（ziplist）和哈希表（hashtable）。当 field-value 长度较短且个数较少时，使用 ziplist；否则使用 hashtable。

## redis有序集合（zset）
Redis zset 和普通集合 set 非常相似，区别是 zset 中的每个成员都关联了一个评分（score）。集合中的成员唯一，但评分可重复。

1. zadd [key] [score1] [value1] [score2] [value2]：
2. zrange [key] [start] [stop] [withscores]：
3. zrangebyscore [key] [min] [max] [withscores] [limit offset count]：zrangebyscore topn 300 500，从小到大，包括 min 和 max 的成员
4. zrevrangebyscore [key] [max] [min] [withscores] [limit offset count]
5. zincrby [key] [increment] [value]：为成员增加分数
6. zrem [key] [value]：删除值
7. zcount [key] [min] [max]：统计区间内的元素个数
8. zrank [key] [value]：返回该值在集合中的排名

zset 的数据结构包括两种：

1. hash：关联 value 和 score，保证 value 的唯一性，并且可以通过 value 找到 score
2. 跳跃表：给 value 排序

## 发布和订阅

## 新数据类型
1. Bitmaps
2. HyperLogLog
3. Geographic

## jedis
记得把服务器的端口打开。可以直接把防火墙关了，但是最好上腾讯云把端口打开。在“轻量应用服务器”->“服务器”->“更多”->“管理”->“防火墙”。

### jedis模拟验证码发送

















