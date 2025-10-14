---
sidebar_position: 12
---

# 金蝶面试

## 一、数据库三大范式

1. **第一范式（1NF）**：
    要求数据库表的每一列都是不可再分的原子值，不能有重复组或数组。
    ✅ *例子*：不能在一个字段中存多个手机号，应该拆成多行或子表。
2. **第二范式（2NF）**：
    在满足1NF的基础上，要求**每一列都完全依赖于主键**，不能只依赖主键的一部分（针对联合主键）。
    ✅ *例子*：如果主键是（学生ID，课程ID），那么“学生姓名”不应只依赖学生ID，而应拆出去。
3. **第三范式（3NF）**：
    在满足2NF的基础上，要求非主键列**不能传递依赖**主键。
    ✅ *例子*：订单表中的“客户地址”不应该通过“客户ID”间接依赖订单ID，应该单独存客户信息。

👉 **总结**：

- 1NF：列不可分。
- 2NF：不能部分依赖。
- 3NF：不能传递依赖。

## 二、SQL优化的方式

**索引优化**

- 给高频查询条件（WHERE、JOIN、ORDER BY、GROUP BY）列加索引
- 使用联合索引（注意最左前缀原则）
- 避免索引失效（如对字段使用函数、隐式类型转换等）

**SQL语句优化**

- 避免 `SELECT *`，只查需要的列
- 减少子查询，优先考虑 `JOIN`
- 尽量减少 `OR`，可以用 `IN` 或 `UNION` 替代
- 控制返回数据量，分页查询加限制条件

**执行计划分析**

- 使用 `EXPLAIN` 查看执行计划
- 判断是否使用索引、扫描类型（全表扫or索引扫）

**表结构优化**

- 适当分表分库
- 使用合适的数据类型（`int`优于`varchar`）

## 三、说出三个设计模式

**单例模式（Singleton）**
 确保一个类只有一个实例，并提供全局访问点。
 📌 *应用场景*：Spring中的`Bean`默认单例。

**工厂模式（Factory）**
 由工厂类来负责对象的创建，客户端无需关心实例化逻辑。
 📌 *应用场景*：`Spring Bean`的创建由IOC容器完成。

**观察者模式（Observer）**
 当一个对象状态发生改变时，自动通知依赖它的对象。
 📌 *应用场景*：Spring事件机制、消息队列订阅。

## 四、高内聚低耦合

- **高内聚**：一个模块内部的功能应该紧密相关、单一职责，模块内的改动不影响其他模块。
   ✅ *例子*：订单模块只处理订单逻辑。
- **低耦合**：模块与模块之间尽量减少依赖，修改一个模块不应牵连多个模块。
   ✅ *例子*：通过接口或事件进行解耦。

👉 *意义*：提高系统的可维护性、扩展性、复用性。

## 五、用一句话说，分布式架构是什么

分布式架构就是**把一个系统拆分成多个独立的服务模块，分布在不同的服务器上协同工作**，以提升系统的**性能、可靠性和可扩展性**。

## 六、消息队列是什么？在分布式系统里面的作用

**定义**：消息队列（MQ）是一种**异步通信机制**，通过生产者-消费者模型实现服务解耦。

**常用产品**：RabbitMQ、Kafka、RocketMQ、ActiveMQ。

**在分布式系统的作用**：

1. **异步处理**：比如下单后发送短信，不必同步等待。
2. **削峰填谷**：缓解高并发压力，防止系统崩溃。
3. **服务解耦**：生产者和消费者独立，不直接依赖。
4. **可靠通信**：保证消息不丢失（配合持久化机制）。

## 七、Spring Bean的作用域？

| 作用域        | 描述                                            |
| ------------- | ----------------------------------------------- |
| `singleton`   | 单例（默认），整个Spring容器中只有一个Bean实例  |
| `prototype`   | 多例，每次获取Bean都会创建新的实例              |
| `request`     | 每次HTTP请求创建一个Bean实例（Web环境）         |
| `session`     | 每个HTTP Session对应一个Bean实例（Web环境）     |
| `application` | 整个ServletContext中共享一个Bean实例（Web环境） |

## 八、SpringMVC全局异常处理怎么做？优点

### 1. **使用 @ControllerAdvice + @ExceptionHandler**

```
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(Exception.class)
    @ResponseBody
    public Result handleException(Exception e) {
        return Result.error("服务器错误：" + e.getMessage());
    }
}
```

### 2. **使用 @RestControllerAdvice（返回JSON更方便）**

```
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(RuntimeException.class)
    public Result handleRuntimeException(RuntimeException e) {
        return Result.error(e.getMessage());
    }
}
```

### ✅ 优点

- 集中管理异常，代码更简洁
- 解耦业务逻辑与异常处理
- 统一返回格式，便于前端处理
- 提高可维护性和系统健壮性

