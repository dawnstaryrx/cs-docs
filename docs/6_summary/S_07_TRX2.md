---
sidebar_position: 7
---

# 天融信二面

下面是对你列出的 Java 面试题的完整回答，涵盖 Java 基础、并发、Spring、MySQL、Redis 等多个维度：

------

### 1. **if 和 switch 的区别**

- `if` 可以用于各种类型的条件判断（如布尔、比较表达式）。
- `switch` 通常用于多分支的相等判断，适用于 `byte`, `short`, `char`, `int`, `String`, `enum` 类型。
- `switch` 在分支多时结构更清晰，性能略优于大量的 `if-else if`。

------

### 2. **重载（Overload）和重写（Override）的区别**

| 区别       | 重载                       | 重写                              |
| ---------- | -------------------------- | --------------------------------- |
| 定义       | 同类中方法名相同，参数不同 | 子类中重写父类的方法              |
| 参数       | 不同                       | 相同                              |
| 返回类型   | 可以不同                   | 通常相同（Java 5 后可返回子类型） |
| 访问修饰符 | 不受限制                   | 子类方法不能低于父类权限          |

------

### 3. **对泛型的理解，泛型擦除**

- 泛型：是 Java 提供的参数化类型机制，提高代码的类型安全性和可读性。
- 泛型擦除：Java 编译后泛型信息被擦除为原始类型（如 `List<String>` 编译后变成 `List`），并通过桥方法和类型检查保证安全。

------

### 4. **Error 和 Exception 的区别**

- `Error`：严重问题，如内存溢出（OutOfMemoryError），程序无法处理。
- `Exception`：程序可以处理的问题，包括运行时异常（如 NullPointerException）和受检异常（如 IOException）。

------

### 5. **String 是不是基本数据类型？**

不是。`String` 是 Java 中的引用类型，是 `java.lang.String` 类的对象。

------

### 6. **final 修饰符能修饰变量和类吗？修饰类有什么效果**

- `变量`：表示常量，只能赋值一次。
- `类`：表示该类不能被继承（如 `java.lang.String` 是 final 类）。

------

### 7. **Java 中的容器包括哪两部分**

1. **Collection**：存储单个元素，如 `List`, `Set`, `Queue`。
2. **Map**：存储键值对，如 `HashMap`, `TreeMap`。

------

### 8. **Collection 和 Collections 的区别**

- `Collection`：接口，集合类的父接口。
- `Collections`：工具类，提供集合相关的静态方法，如排序、同步等。

------

### 9. **HashMap 和 TreeMap 的区别**

| 特性     | HashMap      | TreeMap                 |
| -------- | ------------ | ----------------------- |
| 顺序     | 无序         | 按 key 排序（红黑树）   |
| 性能     | 更快（O(1)） | 较慢（O(log n)）        |
| 接口实现 | Map          | NavigableMap, SortedMap |

------

### 10. **对迭代器的理解**

- 迭代器（Iterator）用于遍历集合元素。
- 提供方法：`hasNext()`, `next()`, `remove()`。
- `foreach` 底层也是基于 `Iterator` 实现。

------

### 11. **队列的 poll 和 remove 方法的区别**

- `poll()`：获取并移除队头元素，队列为空返回 `null`。
- `remove()`：功能相同，但队列为空时抛出 `NoSuchElementException`。

------

### 12. **并行和并发的区别**

- 并发（Concurrency）：多个任务在同一时间段交替执行。
- 并行（Parallelism）：多个任务在同一时间**同时执行**，需要多核 CPU 支持。

------

### 13. **线程和进程的区别**

| 特性 | 线程                   | 进程             |
| ---- | ---------------------- | ---------------- |
| 定义 | 程序执行的最小单位     | 程序执行的实例   |
| 内存 | 共享同一进程的内存空间 | 拥有独立内存空间 |
| 创建 | 快                     | 慢               |
| 开销 | 小                     | 大               |

------

### 14. **如何创建一个线程**

1. 实现 `Runnable` 接口。
2. 继承 `Thread` 类。
3. 使用线程池 `ExecutorService`。

```java
// Runnable 实现
new Thread(() -> System.out.println("Hello")).start();
```

------

### 15. **线程的状态**

- NEW
- RUNNABLE
- BLOCKED
- WAITING
- TIMED_WAITING
- TERMINATED

------

### 16. **线程池的 submit 方法**

- `submit()` 提交任务，返回 `Future` 对象。
- 可获取任务执行结果或异常，适合异步任务处理。

------

### 17. **浅拷贝和深拷贝**

- **浅拷贝**：拷贝对象，但对象中的引用字段仍指向原对象。
- **深拷贝**：不仅拷贝对象，还递归拷贝所有引用对象。

------

### 18. **序列化和反序列化**

- **序列化**：将对象转换为字节流（`ObjectOutputStream`）。
- **反序列化**：将字节流还原为对象（`ObjectInputStream`）。
- 需实现 `Serializable` 接口。

------

### 19. **Stream 提取以 1 开头的数字**

```java
List<String> result = list.stream()
    .filter(s -> s.startsWith("1"))
    .collect(Collectors.toList());
```

------

### 20. **application.yml 和 bootstrap.yml 的区别**

- `bootstrap.yml`：用于项目启动初期加载，适合配置中心（如 Nacos）。
- `application.yml`：加载在 `bootstrap` 之后，项目的主要配置文件。

------

### 21. **Spring Boot 和 Spring 的区别**

- `Spring`：基础框架，需手动配置 XML 或 JavaConfig。
- `Spring Boot`：快速开发框架，约定优于配置，内置容器，自动装配。

------

### 22. **Spring Boot 的核心注解**

- `@SpringBootApplication`
- `@EnableAutoConfiguration`
- `@ComponentScan`
- `@Configuration`

------

### 23. **自动装配的流程**

1. 启动类加载 `@SpringBootApplication`。
2. `@EnableAutoConfiguration` 导入 `META-INF/spring.factories` 中的配置类。
3. 使用条件注解（`@ConditionalOn...`）判断是否加载 Bean。

------

### 24. **从输入一个 URL 到页面出现的流程**

1. 浏览器解析 URL。
2. DNS 解析获得 IP。
3. 建立 TCP 连接（握手）。
4. 发出 HTTP 请求。
5. 服务器处理请求，返回 HTML。
6. 浏览器渲染页面。

------

### 25. **MySQL 事务隔离级别**

- Read Uncommitted
- Read Committed
- Repeatable Read（MySQL 默认）
- Serializable

------

### 26. **可重复读是怎么实现的**

- 基于多版本并发控制（MVCC），读取的是事务开始时的数据快照。
- 通过 undo log 保证一致性。

------

### 27. **Redis 的 ZSET，底层数据结构是什么**

- 跳表（SkipList）+ 哈希表。
- 哈希表用于快速查找，跳表用于按 score 排序。

------

### 28. **IO 和 NIO 的区别**

| 特性     | IO     | NIO           |
| -------- | ------ | ------------- |
| 模型     | 阻塞   | 非阻塞        |
| 面向     | 流     | 缓冲区        |
| 多路复用 | 不支持 | 支持 Selector |

------

### 29. **Java 常用的数据结构有哪些**

- List：ArrayList, LinkedList
- Set：HashSet, TreeSet
- Map：HashMap, TreeMap
- Stack, Queue（LinkedList/Deque）
- PriorityQueue, ConcurrentHashMap

------

### 30. **Redis 持久化的方式**

- RDB（快照）
- AOF（追加日志）
- 混合持久化（RDB + AOF）

------

### 31. **微服务分布式锁的实现方式**

- 基于 Redis（setnx + 过期时间 + Lua 脚本）
- 基于 Zookeeper（临时有序节点）
- 基于数据库唯一约束（不推荐）

------

### 32. **堆和栈的区别**

| 特性     | 堆             | 栈                 |
| -------- | -------------- | ------------------ |
| 存储     | 对象实例       | 方法调用、局部变量 |
| 分配     | 手动（new）    | 自动               |
| 生命周期 | 手动控制（GC） | 随方法调用释放     |

------

### 33. **栈和队列的区别**

- 栈：先进后出（LIFO），如 `Stack`, `Deque`
- 队列：先进先出（FIFO），如 `Queue`, `LinkedList`, `PriorityQueue`
