---
sidebar_position: 1
---

# Redis

## 一、概述

### 1. 什么是Redis？

Redis 是速度非常快的非关系型（NoSQL）内存键值数据库，可以存储键和五种不同类型的值之间的映射。与传统数据库不同的是，Redis 的数据是保存在内存中的（内存数据库，支持持久化），因此读写速度非常快，被广泛应用于分布式缓存方向。

键的类型只能为字符串，值支持五种数据类型：字符串、列表、集合、散列表、有序集合。

Redis 支持很多特性，例如将内存中的数据持久化到硬盘中，使用复制来扩展读性能，使用分片来扩展写性能。

https://cyc2018.xyz/%E6%95%B0%E6%8D%AE%E5%BA%93/Redis.html

### 2. Redis为什么这么快？

1. Redis 基于**内存**，内存的访问速度比磁盘快很多；

2. Redis 基于 Reactor 模式设计开发了一套高效的事件处理模型，主要是**单线程事件循环和 IO 多路复用**（Redis 线程模式后面会详细介绍到）；

   1. 采用单线程，避免不必要的上下文切换可竞争条件，多线程还要考虑线程安全问题

   2. 使用IO多路复用模型，非阻塞IO

      解释IO多路复用模型：Redis是纯内存操作，执行速度非常快。它的性能瓶颈是网络延迟而不是执行速度，IO多路复用模型主要就是实现了高效的网络请求。

3. Redis 内置了**多种优化过后的数据类型/结构实现**，性能非常高。

4. Redis **通信协议**实现简单且解析高效。

### 3. 为什么不直接用 Redis 当主数据库呢？

主要是因为**内存成本太高**且 Redis 提供的数据持久化仍然有**数据丢失**的风险。

### 4. 为什么要用Redis?

**1、访问速度更快**

传统数据库数据保存在磁盘，而 **Redis 基于内存**，内存的访问速度比磁盘快很多。引入 Redis 之后，我们可以把一些高频访问的数据放到 Redis 中，这样下次就可以直接从内存中读取，速度可以提升几十倍甚至上百倍。

**2、高并发**

一般像 MySQL 这类的数据库的 QPS 大概都在 4k 左右（4 核 8g） ，但是使用 Redis 缓存之后很容易达到 5w+，甚至能达到 10w+（就单机 Redis 的情况，Redis 集群的话会更高）。

> QPS（Query Per Second）：服务器每秒可以执行的查询次数；

由此可见，直接操作缓存能够承受的数据库请求数量是远远大于直接访问数据库的，所以我们可以考虑把数据库中的部分数据转移到缓存中去，这样用户的一部分请求会直接到缓存这里而不用经过数据库。进而，我们也就提高了系统整体的并发。

**3、功能全面**

Redis 除了可以用作缓存之外，还可以用于分布式锁、限流、消息队列、延时队列等场景，功能强大！

### 5. 常见的缓存读写策略有哪些？

https://javaguide.cn/database/redis/3-commonly-used-cache-read-and-write-strategies.html#read-write-through-pattern-%E8%AF%BB%E5%86%99%E7%A9%BF%E9%80%8F

## 二、Redis应用

### 1. Redis除了做缓存，还能做什么？

**分布式锁**：通过 Redis 来做分布式锁是一种比较常见的方式。通常情况下，我们都是基于 Redisson 来实现分布式锁。关于 Redis 实现分布式锁的详细介绍：[分布式锁详解](https://javaguide.cn/distributed-system/distributed-lock.html) 。

**限流**：一般是通过 Redis + Lua 脚本的方式来实现限流。如果不想自己写 Lua 脚本的话，也可以直接利用 Redisson 中的 `RRateLimiter` 来实现分布式限流，其底层实现就是基于 Lua 代码+令牌桶算法。

**消息队列**：Redis 自带的 List 数据结构可以作为一个简单的队列使用。Redis 5.0 中增加的 Stream 类型的数据结构更加适合用来做消息队列。它比较类似于 Kafka，有主题和消费组的概念，支持消息持久化以及 ACK 机制。

**延时队列**：Redisson 内置了延时队列（基于 Sorted Set 实现的）。

**分布式 Session** ：利用 String 或者 Hash 数据类型保存 Session 数据，所有的服务器都可以访问。

**复杂业务场景**：通过 Redis 以及 Redis 扩展（比如 Redisson）提供的数据结构，我们可以很方便地完成很多复杂的业务场景比如通过 Bitmap 统计活跃用户、通过 Sorted Set 维护排行榜。

……

### 2. 如何基于Redis实现分布式锁？

https://javaguide.cn/distributed-system/distributed-lock-implementations.html

## 三、Redis数据类型

### 1. Redis常用的数据类型有哪些？

Redis 中比较常见的数据类型有下面这些：

- **5 种基础数据类型**：String（字符串）、List（列表）、Set（集合）、Hash（散列）、Zset（有序集合）。
- **3 种特殊数据类型**：HyperLogLog（基数统计）、Bitmap （位图）、Geospatial (地理位置)。

除了上面提到的之外，还有一些其他的比如 [Bloom filter（布隆过滤器）](https://javaguide.cn/cs-basics/data-structure/bloom-filter.html)、Bitfield（位域）。

