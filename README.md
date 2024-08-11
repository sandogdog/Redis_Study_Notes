# Redis_Study_notes
前言：在学习Redis中会使用到的简单命令，作为三狗狗的备忘录使用

---
1.启动服务:
```Redis
redis-server.exe redis.windows.conf
```

<br>

2.连接服务:
```Redis
redis-cli.exe -h localhost -p 6379 -a 123456

keys *
```

<br>

3.字符串操作命令

Redis 字符串类型常用命令：

SET key value 设置指定key的值

GET key 获取指定key的值

SETEX key seconds value 设置指定key的值，并将 key 的过期时间设为 seconds 秒

SETNX key value 只有在 key 不存在时设置 key 的值

<br>

4.哈希操作命令

Redis hash 是一个string类型的 field 和 value 的映射表，hash特别适合用于存储对象，常用命令：

HSET key field value  将哈希表 key 中的字段 field 的值设为 value

HGET key field  获取存储在哈希表中指定字段的值

HDEL key field 删除存储在哈希表中的指定字段

HKEYS key  获取哈希表中所有字段

HVALS key  获取哈希表中所有值

<br>

5.列表操作命令

Redis 列表是简单的字符串列表，按照插入顺序排序，常用命令：

LPUSH key value1 [value2]  将一个或多个值插入到列表头部(左边)

LRANGE key start stop  获取列表指定范围内的元素

(0 -1是获取列表全部元素)

RPOP key  移除并获取列表最后一个元素(右边)

LLEN key  获取列表长度

<br>

6.集合操作命令

Redis set 是string类型的无序集合。集合成员是唯一的，集合中不能出现重复的数据，常用命令：

SADD key member1 [member2]  向集合添加一个或多个成员

SMEMBERS key  返回集合中的所有成员

SCARD key  获取集合的成员数

SINTER key1 [key2]  返回给定所有集合的交集

SUNION key1 [key2]  返回所有给定集合的并集

SREM key member1 [member2]  删除集合中一个或多个成员

<br>

7.有序集合操作命令

Redis有序集合是string类型元素的集合，且不允许有重复成员。每个元素都会关联一个double类型的分数。常用命令：

ZADD key score1 member1 [score2 member2]  向有序集合添加一个或多个成员

ZRANGE key start stop [WITHSCORES]  通过索引区间返回有序集合中指定区间内的成员

ZINCRBY key increment member  有序集合中对指定成员的分数加上增量 increment

ZREM key member [member ...]  移除有序集合中的一个或多个成员

<br>

8.通用命令

Redis的通用命令是不分数据类型的，都可以使用的命令：

KEYS pattern  查找所有符合给定模式( pattern)的 key 

EXISTS key  检查给定 key 是否存在

TYPE key  返回 key 所储存的值的类型

DEL key  该命令用于在 key 存在是删除 key
