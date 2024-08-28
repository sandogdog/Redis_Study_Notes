# Redis_Study_notes
前言：在学习Redis中会使用到的简单命令，作为三狗狗的备忘录使用

---
#### 1.启动服务:
```Redis
redis-server.exe redis.windows.conf
```

<br>

#### 2.连接服务:
```Redis
redis-cli.exe -h localhost -p 6379 -a 123456

keys *
```

<br>

#### 3.字符串操作命令

Redis 字符串类型常用命令：

SET key value 设置指定key的值

GET key 获取指定key的值

SETEX key seconds value 设置指定key的值，并将 key 的过期时间设为 seconds 秒

SETNX key value 只有在 key 不存在时设置 key 的值

<br>

#### 4.哈希操作命令

Redis hash 是一个string类型的 field 和 value 的映射表，hash特别适合用于存储对象，常用命令：

HSET key field value  将哈希表 key 中的字段 field 的值设为 value

HGET key field  获取存储在哈希表中指定字段的值

HDEL key field 删除存储在哈希表中的指定字段

HKEYS key  获取哈希表中所有字段

HVALS key  获取哈希表中所有值

<br>

#### 5.列表操作命令

Redis 列表是简单的字符串列表，按照插入顺序排序，常用命令：

LPUSH key value1 [value2]  将一个或多个值插入到列表头部(左边)

LRANGE key start stop  获取列表指定范围内的元素

(0 -1是获取列表全部元素)

RPOP key  移除并获取列表最后一个元素(右边)

LLEN key  获取列表长度

<br>

#### 6.集合操作命令

Redis set 是string类型的无序集合。集合成员是唯一的，集合中不能出现重复的数据，常用命令：

SADD key member1 [member2]  向集合添加一个或多个成员

SMEMBERS key  返回集合中的所有成员

SCARD key  获取集合的成员数

SINTER key1 [key2]  返回给定所有集合的交集

SUNION key1 [key2]  返回所有给定集合的并集

SREM key member1 [member2]  删除集合中一个或多个成员

<br>

#### 7.有序集合操作命令

Redis有序集合是string类型元素的集合，且不允许有重复成员。每个元素都会关联一个double类型的分数。常用命令：

ZADD key score1 member1 [score2 member2]  向有序集合添加一个或多个成员

ZRANGE key start stop [WITHSCORES]  通过索引区间返回有序集合中指定区间内的成员

ZINCRBY key increment member  有序集合中对指定成员的分数加上增量 increment

ZREM key member [member ...]  移除有序集合中的一个或多个成员

<br>

#### 8.通用命令

Redis的通用命令是不分数据类型的，都可以使用的命令：

KEYS pattern  查找所有符合给定模式( pattern)的 key 

EXISTS key  检查给定 key 是否存在

TYPE key  返回 key 所储存的值的类型

DEL key  该命令用于在 key 存在是删除 key

<br>

#### 9.项目开发中的实例：
```Java
//将所有的菜品缓存数据清理掉，所有以dish_开头的key
cleanCache("dish*");

private void cleanCache(String pattern) {
  Set keys = redisTemplate.keys(pattern);
  redisTemplate.delete(keys);
    }
```
删除dish_开头key是使用dish*的pattern。

<br>

#### 10.Redis 分布式锁

分布式锁的作用:

分布式锁的目的是在分布式系统中协调多个进程或线程对共享资源的访问，确保同一时间只有一个进程能访问某个资源，防止并发冲突。

分布式锁的实现原理:

Redis 的分布式锁利用了 SETNX（Set if Not Exists） 和 EXPIRE（设置过期时间）来实现。典型的步骤如下：

1)尝试获取锁：客户端使用 SETNX 命令向 Redis 发送请求，只有当该键不存在时才能成功设置，从而获取锁。

2)设置过期时间：同时设置过期时间 EXPIRE，以防止因程序意外崩溃或网络问题导致锁无法释放。

3)释放锁：操作完成后，通过 DEL 命令删除锁，释放资源。

redis分布式锁的代码示例：
```Java
// 获取锁
boolean lockAcquired = redisTemplate.opsForValue().setIfAbsent("myLockKey", "lockValue", 10, TimeUnit.SECONDS);
if (lockAcquired) {
    try {
        // 进行业务操作
    } finally {
        // 释放锁
        redisTemplate.delete("myLockKey");
    }
}
```

<br>

### 11.Redisson 是一个基于 Redis 的 Java 驱动程序和工具集，它提供了比普通 Redis 客户端更多的高级功能，特别是适用于分布式系统中的各种需求。Redisson 提供了易于使用的 Redis 接口，支持多种 Redis 数据结构和分布式工具，如分布式锁、分布式集合、阻塞队列等。

#### Redisson 的核心功能

1)分布式数据结构 Redisson 为 Java 提供了丰富的分布式数据结构，包括：

·分布式对象（如 RMap、RSet、RList、RQueue 等）：这些对象在多个 Redis 节点之间共享，并且操作这些对象就像操作普通 Java 集合一样。

·分布式锁（RLock）：用于在分布式系统中实现锁机制，以避免并发访问同一个资源时产生的竞争条件。

·分布式信号量（RSemaphore） 和 分布式读写锁（RReadWriteLock）：用于更高级的分布式控制。

2)分布式锁 Redisson 提供了多种分布式锁的实现，例如：

·公平锁：保证锁是按照请求顺序公平分配的。

·可重入锁：同一个线程可以多次获得同一把锁。

·读写锁：可以分别处理读锁和写锁，适用于读多写少的场景。

·红锁：一种基于 Redis 实现的分布式锁算法，适用于多节点集群。

使用 Redisson 的分布式锁，可以在分布式系统中确保不同的服务实例在操作共享资源时避免竞争。

示例：使用 Redisson 分布式锁
```Java
RLock lock = redissonClient.getLock("myLock");
lock.lock();
try {
    // 进行临界区操作
} finally {
    lock.unlock();
}
```
3）分布式队列 Redisson 提供了分布式队列（如 RQueue、RBlockingQueue、RDeque 等），它们可以在分布式系统中用于消息队列、任务调度等场景。

4）分布式对象桶（Bucket） Redis 中的 String 类型可以通过 Redisson 的 RBucket 来操作，允许存储和获取简单的键值对。

5）分布式任务调度 Redisson 提供了类似于 Java Executor Service 的功能，支持在 Redis 集群上调度和执行分布式任务。这些任务可以是异步执行的，并支持失败重试等机制。

6）分布式缓存 Redisson 可以作为分布式缓存解决方案，支持 TTL（过期时间）、LRU、LFU 等常见的缓存过期策略。它还提供了像 本地缓存与分布式缓存相结合 的解决方案，适用于高性能、高并发的场景。

<br>

### 12.Redlock 是一种分布式锁算法，由 Redis 的作者 Antirez 提出，用来确保在分布式环境中多个 Redis 实例下的锁安全性和可靠性。

#### 为什么需要 Redlock？

在分布式系统中，如果你只有一个 Redis 实例用于锁操作（如 SETNX），可能会因为单点故障导致锁机制失效。这就是为什么我们需要一种更安全的分布式锁算法来确保锁在多个 Redis 实例中是有效的和一致的。

#### Redlock 的核心原理:

Redlock 的核心思路是在多个独立的 Redis 实例上获取锁，确保即使有些实例不可用或失效，锁的整体逻辑仍然可以正常运行。

#### Redlock 的步骤:

1)在 N 个 Redis 实例上尝试获取锁：N 通常是一个奇数（如 3 或 5），这些实例彼此独立。

2)生成唯一的锁标识：每个锁请求会生成一个全局唯一的标识符（通常使用 UUID）。

3)在每个 Redis 实例上设置锁：

·使用命令 SET key value NX PX ttl，NX 保证只有当键不存在时才设置锁，PX 设置锁的过期时间。

·要求锁在每个 Redis 实例上的设置时间要非常短，通常需要在网络往返时间内完成。

4)锁成功的条件：

·当且仅当在大多数（即 N/2 + 1）Redis 实例上成功获取锁时，认为锁获取成功。

·例如，在 5 个实例中，至少需要在 3 个实例上获取到锁。

5)锁的超时时间：

·锁的过期时间需要足够短，以确保锁在失效或出现网络分区时能够自动释放。

·锁的超时时间必须比完成实际业务逻辑所需的最长时间要长。

锁的释放：

6)锁的释放也是通过向各个 Redis 实例发送删除命令 DEL key，且要保证只能删除由自己持有的锁。通常会在删除时比对锁的标识符，确认是当前持有者才能删除。

Redlock 的优点:

·容错性强：即使某些 Redis 实例不可用，算法仍能确保大多数实例上锁的一致性，从而避免了单点故障问题。

·防止死锁：通过锁的过期时间，确保锁不会因为意外情况（如进程崩溃或网络分区）而永久占用。

·一致性：通过在大多数节点上获取锁来保证分布式系统中的一致性。

Redlock 的应用场景

Redlock 适用于以下场景：

·需要高可靠性的分布式锁，并且不能容忍单点故障的业务场景。

·分布式系统中共享资源的竞争问题，如跨多个节点进行库存扣减等操作。

Redlock 的局限性

·时间敏感性：Redlock 依赖于锁的过期时间和节点响应时间，网络延迟、时钟漂移等问题可能影响锁的正确性。

·实现复杂：相比简单的 Redis 锁，Redlock 的实现和使用更加复杂，可能需要更细致的调试和配置。

Redlock 示例

以下是一个使用 Redlock 机制的简化代码示例，说明如何在多个 Redis 实例上获取分布式锁：
```Java
// 假设有多个 Redis 实例
List<RedisInstance> redisInstances = Arrays.asList(redis1, redis2, redis3, redis4, redis5);

// 全局唯一锁标识
String lockId = UUID.randomUUID().toString();
boolean isLocked = true;

// 在 N/2 + 1 个实例上获取锁
int successCount = 0;
for (RedisInstance redis : redisInstances) {
    if (redis.setLock(key, lockId, ttl)) {
        successCount++;
    }
}

// 如果在大多数实例上获得锁，认为成功
if (successCount >= redisInstances.size() / 2 + 1) {
    // 执行业务逻辑
} else {
    isLocked = false;
    // 获取锁失败，释放已经获取到的锁
    for (RedisInstance redis : redisInstances) {
        redis.releaseLock(key, lockId);
    }
}

// 业务逻辑结束后，释放锁
if (isLocked) {
    for (RedisInstance redis : redisInstances) {
        redis.releaseLock(key, lockId);
    }
}
```

<br>

#### 13.“缓存三兄弟”指的是常见的三种缓存策略或方案，它们分别是：

缓存穿透（Cache Penetration）：

问题：缓存穿透是指查询的数据既不存在于缓存中，也不存在于数据库中。这种情况下，每次请求都会直接查询数据库，导致缓存失效，从而使得数据库承受大量无效查询压力。

解决方案：

缓存空值：如果查询数据库后确定数据不存在，可以将这个结果（如 null）也放入缓存中，并设置较短的过期时间，防止频繁的无效查询。

布隆过滤器（Bloom Filter）：在查询缓存或数据库之前，使用布隆过滤器判断键是否存在，避免无效查询直接进入数据库。

缓存击穿（Cache Breakdown）：

问题：缓存击穿是指某些缓存项在某一时刻失效，大量并发请求同时涌入查询同一条数据，由于缓存失效，这些请求都直接访问数据库，可能导致数据库瞬时压力过大。

解决方案：

互斥锁（Mutex Lock）：在缓存失效时，让第一个请求去查询数据库并设置缓存，其他请求等待该缓存更新完毕，防止大量并发访问同一数据库资源。

提前预热缓存：对于热点数据，可以在缓存失效前主动刷新缓存，或者延长热点数据的过期时间。

缓存雪崩（Cache Avalanche）：

问题：缓存雪崩是指大量缓存同时失效，导致大量请求直接打到数据库，导致数据库压力骤增，甚至引发系统崩溃。

解决方案：

缓存过期时间错峰：设置不同的缓存过期时间，避免大量缓存同时失效。

双缓存策略：在缓存即将失效时，使用后台任务自动延长热点数据的缓存时间，或者使用双缓存策略，保证缓存数据的持续可用。

限流和降级：当发现缓存雪崩的情况时，可以通过限流机制减少请求的数量，或者启用降级方案，返回默认值或部分降级数据，减少数据库压力。
