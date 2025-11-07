---
sidebar_position: 15
---

# 新华三面试

## 一、定位bug

如果测试时，没有问题。在线上出现500错误，应该如何一步步排查？

**排查思路分步骤：**

1. **查看日志（首要步骤）**
   - 先看服务器日志文件（如 Spring Boot 的 `error.log` 或控制台输出）。
   - 定位异常堆栈，找到具体的 **异常类型（NullPointerException、SQLSyntaxErrorException、FeignException等）** 和 **触发代码行**。
2. **确认版本一致性**
   - 检查线上部署的 jar 包是否和本地测试版本一致。
   - 核对是否遗漏了配置文件、静态资源或 jar 依赖。
3. **查看配置差异**
   - 检查数据库连接、Redis、第三方接口等环境变量。
   - 有可能线上和测试环境配置不同导致连接失败或权限不足。
4. **检查数据差异**
   - 测试环境数据可能是干净的，而线上数据更复杂。
   - 排查是否有空指针、越界、主键冲突等数据异常。
5. **复现问题**
   - 通过日志参数或调用链信息，构造出同样请求在测试环境复现。
   - 使用 AOP 或 SkyWalking / Zipkin / Sleuth 等工具追踪调用链。
6. **增加保护与容错**
   - 对外部接口调用加异常捕获。
   - 对关键点参数判空（例如 `Objects.requireNonNull()`）。
   - 给返回结果添加统一异常处理（`@ControllerAdvice` + `@ExceptionHandler`）。

✅ **总结一句话**：

> 先查日志 → 确定异常堆栈 → 对比配置与数据 → 复现场景 → 优化容错。

## 二、动态排名 数据结构

比如有一个一分钟的小游戏，需要对参与的人进行动态排名，从高到低。有用户名和分数。用Java如何实现？

### **常规思路**

需要支持两种操作：

1. 插入或更新玩家分数。
2. 快速获取前 N 名（高到低）。

### **实现方式一：Java 内存结构（TreeMap / PriorityQueue）**

#### ✅ TreeMap 实现（按分数排序）

```
import java.util.*;

public class RankBoard {
    private final TreeMap<Integer, Set<String>> rankMap = new TreeMap<>(Collections.reverseOrder());

    // 更新或插入分数
    public void updateScore(String user, int score) {
        // 删除旧分数
        rankMap.values().forEach(set -> set.remove(user));
        // 加入新分数
        rankMap.computeIfAbsent(score, k -> new HashSet<>()).add(user);
    }

    // 获取前 N 名
    public List<String> getTopN(int n) {
        List<String> result = new ArrayList<>();
        for (var entry : rankMap.entrySet()) {
            for (String user : entry.getValue()) {
                result.add(user + ":" + entry.getKey());
                if (result.size() >= n) return result;
            }
        }
        return result;
    }

    public static void main(String[] args) {
        RankBoard board = new RankBoard();
        board.updateScore("Alice", 90);
        board.updateScore("Bob", 95);
        board.updateScore("Tom", 88);
        board.updateScore("Alice", 97);
        System.out.println(board.getTopN(3));
    }
}
```

特点：

- 插入、更新：O(log n)
- 排名查询：O(n)
- 简洁可靠，适合小型实时排名。

------

### **实现方式二：Redis SortedSet（生产级）**

Redis 的 `ZSET` 自带排序功能：

```
ZADD game_rank 97 "Alice"
ZADD game_rank 95 "Bob"
ZADD game_rank 88 "Tom"
ZREVRANGE game_rank 0 9 WITHSCORES
```

在 Java 中：

```
redisTemplate.opsForZSet().add("game_rank", "Alice", 97);
Set<ZSetOperations.TypedTuple<String>> top10 =
    redisTemplate.opsForZSet().reverseRangeWithScores("game_rank", 0, 9);
```

优势：

- 天然支持动态排名；
- O(log n) 插入；
- 支持并发、分布式环境；
- 可用作排行榜、积分系统、热度榜等。

## 三、建表

比如淘宝、拼多多的收货地址表如何实现？一个用户可能有多个收货地址，只有一个地址设为默认地址。

### **表结构设计**

#### **用户表（user）**

| 字段名   | 类型        | 说明   |
| -------- | ----------- | ------ |
| id       | BIGINT      | 主键   |
| username | VARCHAR(50) | 用户名 |
| phone    | VARCHAR(20) | 手机号 |
| ...      | ...         | ...    |

#### **收货地址表（user_address）**

| 字段名         | 类型         | 说明                       |
| -------------- | ------------ | -------------------------- |
| id             | BIGINT       | 主键                       |
| user_id        | BIGINT       | 所属用户                   |
| receiver_name  | VARCHAR(50)  | 收件人                     |
| receiver_phone | VARCHAR(20)  | 收件电话                   |
| province       | VARCHAR(50)  | 省份                       |
| city           | VARCHAR(50)  | 城市                       |
| district       | VARCHAR(50)  | 区县                       |
| detail         | VARCHAR(200) | 详细地址                   |
| is_default     | TINYINT(1)   | 是否默认地址（0 否，1 是） |
| create_time    | DATETIME     | 创建时间                   |
| update_time    | DATETIME     | 更新时间                   |

#### **约束**

- 一个用户只能有一个默认地址，可以通过唯一索引约束实现：

```
ALTER TABLE user_address ADD CONSTRAINT uniq_user_default UNIQUE (user_id, is_default);
```

> ⚠️ 注意：由于 MySQL 的唯一约束不允许多个 `is_default=0` 的重复，需要改成 **触发器/程序控制** 实现唯一默认。