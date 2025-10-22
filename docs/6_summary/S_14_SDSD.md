---
sidebar_position: 14
---

# 四达时代面试

## 一、==和equals的区别

**`==`**

- 对于基本类型：比较的是**数值是否相等**。
- 对于引用类型：比较的是**两个引用是否指向同一个对象**（即内存地址是否相同）。

**`equals`**

- `equals` 是 `Object` 类中的一个方法，默认实现是和 `==` 一样，比较内存地址。
- 一般在业务中我们会**重写 `equals` 方法**，比如 `String`、`Integer` 等类，都是重写了 `equals`，用于比较对象内容的相等性。

### equals一定要重写hashcode方法吗？为什么？

如果重写了 `equals` 方法，就必须也重写 `hashCode` 方法。

因为根据 Java 规范：

- 如果两个对象通过 `equals` 判断相等，那么它们的 `hashCode` 必须也相等。
- 否则在使用 `HashMap`、`HashSet` 等基于哈希的集合中会出问题。

### 有遇到过重写hashcode的场景吗？

有，比如：

- 在使用 `HashSet` 去重对象时，重写 `equals` 和 `hashCode` 保证业务字段相同的对象不会重复。
- 在使用 `HashMap` 作为缓存时，需要基于业务字段来唯一标识对象。

```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    User user = (User) o;
    return Objects.equals(username, user.username);
}

@Override
public int hashCode() {
    return Objects.hash(username);
}
```

## 二、JVM让线程停顿的原因？

JVM 让线程停顿（Stop The World, STW）常见有以下几种场景👇

1. **垃圾回收（GC）**
   - 不论是 Minor GC 还是 Full GC，JVM 都会暂停所有用户线程，腾出 CPU 给 GC 线程。
   - 特别是 Full GC，停顿时间可能很长。
2. **安全点机制（Safepoint）**
   - 当 JVM 需要进行一些全局性的操作（如 GC、类卸载、偏向锁撤销）时，需要所有线程都到达安全点，然后停顿。
   - 线程在执行特定位置才可以停下来，比如方法调用、循环跳转、异常抛出等地方。
3. **类加载、偏向锁撤销、Deoptimization（反优化）**
   - JIT 优化被撤销或 HotSpot 进行类元数据操作时，也会导致短暂的 STW。
4. **Dump 线程 / Heap 操作**
   - 比如我们在用 `jstack`、`jmap`、`arthas` dump 线程或堆时，也会触发短暂的 STW。

✅ 解决方案：

- 使用 G1、ZGC 等低停顿垃圾回收器。
- 调优对象分配、减少 Full GC 发生频率。
- 合理控制堆内存大小。

## 三、工作中和别人的看法不一致，怎么解决？

**先倾听对方的意见**

- 充分了解对方的出发点，不急着反驳。
- 有时候只是信息不对称。

**清晰表达自己的观点**

- 用事实和数据说话，而不是情绪。
- 例如：性能测试结果、线上日志、监控指标等。

**聚焦问题本身，而不是个人对错**

- 避免情绪冲突，保持专业。

**必要时引入第三方 / 团队讨论**

- 请资深同事或 leader 来参与，集体决策。

**达成共识 / 保留分歧但先推进**

- 项目优先，有时也可以先按照一方方案实施，再验证效果。

## 四、开放性需求题

数据库里有一个用户表，用户有有效和无效状态，一个用户可以订购多个产品；用户订购的产品表记录了这个产品所属的用户，产品有有效和无效状态，并且记录了产品的失效时间。产品到期需要将产品设置成无效，如果一个用户的所有产品都无效了，那么这个用户也设置成无效。库里大概有4000万条数据，每天大概到期50万条。如何去实现？

#### 1. **批量更新产品状态**

- 可以使用一个定时任务（如 Quartz / XXL-Job / Spring 定时任务）。
- 每天定时将 `失效时间 <= 当前时间` 的产品状态批量更新为无效。

```
UPDATE product
SET status = 'INVALID'
WHERE expire_time <= NOW() AND status = 'VALID';
```

- ✅ 建议对 `expire_time` 和 `status` 建立联合索引，加快更新效率。

------

#### 2. **更新用户状态**

当产品失效后，需要判断用户是否还有其他有效产品：

```
UPDATE user u
SET u.status = 'INVALID'
WHERE NOT EXISTS (
  SELECT 1 FROM product p
  WHERE p.user_id = u.id
  AND p.status = 'VALID'
);
```

或者更高效的做法👇

- 将当天失效的产品用户ID收集成集合（比如临时表或队列）。
- 批量查询这些用户是否还有其他有效产品，再更新。

#### 伪代码：

```
// 1. 找出今天过期的产品
List<Long> userIds = productMapper.findUserIdsByExpireTime(now);

// 2. 批量更新产品状态
productMapper.batchUpdateExpired(now);

// 3. 检查这些用户是否还有有效产品
List<Long> invalidUserIds = userMapper.findUserIdsAllProductInvalid(userIds);

// 4. 批量更新用户状态
userMapper.batchUpdateUserInvalid(invalidUserIds);
```

------

#### 3. **异步/分批处理机制**

- 50 万条数据不建议一次性处理，可分批：
  - 例如每批 1 万条，分 50 批执行，避免锁表。
- 可使用 MQ（如 RabbitMQ/Kafka）异步解耦。

------

#### 4. **优化点**

- ✅ 索引：`product(expire_time, status)`、`product(user_id, status)`。
- ✅ 分区表：按月份/季度分区 product 表，加快失效查询。
- ✅ 批处理 + 异步：减少单次 SQL 压力。
- ✅ 只更新有变化的数据，减少不必要的 update。

------

### 📈 额外扩展：

- 可以设置**状态冗余字段**：
  - 在用户表中维护一个 `valid_product_count`。
  - 每次产品状态变更时增减计数；
  - 当计数变为 0 时，直接标记用户无效，无需再查子表。
- 优点：不需要频繁 `NOT EXISTS` 子查询，效率更高。

------

✅ **总结实现流程**：

1. 每天定时任务扫描过期产品 → 批量设置无效。
2. 获取受影响用户 ID → 判断其是否还有有效产品。
3. 对没有有效产品的用户 → 批量更新为无效。
4. 大规模数据处理时注意：
   - SQL 优化
   - 分批异步处理
   - 合理建索引
   - 冗余字段提高效率。