---
sidebar_position: 4
---

# 天融信一面

## 1. 抽象类

### 对抽象类的理解

在 Java 中，**抽象类（abstract class）** 是一种 **不能被实例化** 的类，用于被其他类继承，**定义通用行为并约束子类实现特定方法**。
 它既可以包含**抽象方法（没有方法体）**，也可以包含**具体方法（有方法体）**。

| 特点                     | 说明                                           |
| ------------------------ | ---------------------------------------------- |
| 不能实例化               | 只能被继承，不能 `new` 创建对象                |
| 可包含构造方法           | 用于子类初始化                                 |
| 可包含成员变量和具体方法 | 子类可以继承或重写                             |
| 包含抽象方法             | 子类必须实现所有抽象方法（除非子类也是抽象类） |
| 支持多层继承             | 抽象类可以继承另一个抽象类                     |

### 如何使用抽象类

#### ✅ 抽象类定义：

```
java复制编辑public abstract class Animal {
    protected String name;

    public Animal(String name) {
        this.name = name;
    }

    // 抽象方法：强制子类实现
    public abstract void makeSound();

    // 具体方法：子类可以直接继承
    public void sleep() {
        System.out.println(name + " is sleeping...");
    }
}
```

#### ✅ 子类实现抽象类：

```
java复制编辑public class Dog extends Animal {

    public Dog(String name) {
        super(name);
    }

    @Override
    public void makeSound() {
        System.out.println(name + " says: Woof!");
    }
}
```

#### ✅ 调用：

```
java复制编辑public class TestAbstract {
    public static void main(String[] args) {
        Animal dog = new Dog("Buddy");
        dog.makeSound();  // Buddy says: Woof!
        dog.sleep();      // Buddy is sleeping...
    }
}
```

### 抽象类 vs 接口的区别（常见面试点）

| 对比点     | 抽象类                   | 接口（interface）                 |
| ---------- | ------------------------ | --------------------------------- |
| 方法类型   | 可包含抽象方法和具体方法 | Java 8 起可包含默认方法和静态方法 |
| 成员变量   | 可包含成员变量           | 只能是 `public static final` 常量 |
| 构造器     | 有构造函数               | 无构造函数                        |
| 多继承支持 | 单继承                   | 支持多继承（实现多个接口）        |
| 使用场景   | 表示“是什么”（is-a）     | 表示“能做什么”（can-do）          |

### 应用场景举例：

| 场景               | 使用理由                            |
| ------------------ | ----------------------------------- |
| 模板方法模式       | 抽象类定义模板方法，子类扩展实现    |
| 通用实体类         | 抽象类定义通用字段，如 `BaseEntity` |
| 多个子类有共同特征 | 抽象类封装共同代码，避免重复        |

### ✅ 总结

- 抽象类是实现 **继承共性代码** 和 **强制子类实现特定方法** 的重要机制。
- 抽象类不能实例化，只能作为子类的模板。
- 实际开发中常与设计模式（如模板方法模式）结合使用。

## 2. 设计模式

### 策略模式

策略模式用于**封装一系列可互换的算法或行为**，让它们可以在运行时自由切换，**避免冗长的 if-else 或 switch 语句**。

使用场景

- 多种支付方式（微信、支付宝、银联）
- 多种排序算法或优惠计算方式
- 登录认证方式切换（账号密码、短信、第三方登录）

```java
// 抽象策略
public interface PayStrategy {
    void pay(double amount);
}

// 具体策略
public class WeChatPay implements PayStrategy {
    public void pay(double amount) {
        System.out.println("使用微信支付：" + amount + "元");
    }
}

public class AliPay implements PayStrategy {
    public void pay(double amount) {
        System.out.println("使用支付宝支付：" + amount + "元");
    }
}

// 上下文类
public class PayContext {
    private PayStrategy strategy;

    public PayContext(PayStrategy strategy) {
        this.strategy = strategy;
    }

    public void execute(double amount) {
        strategy.pay(amount);
    }
}

// 调用方式
public class StrategyTest {
    public static void main(String[] args) {
        PayContext context = new PayContext(new WeChatPay());
        context.execute(100.0);  // 输出：使用微信支付：100.0元
    }
}
```

### 工厂方法模式

工厂方法模式用于**将对象的创建延迟到子类中**，父类提供一个抽象方法，由子类来决定具体实例化哪个产品。

使用场景

- 日志框架中 Logger 工厂
- JDBC 中 DriverManager 获取连接
- 根据配置创建不同类型对象（例如：导出 PDF、Excel）

```java
// 产品接口
public interface Product {
    void show();
}

// 具体产品
public class Phone implements Product {
    public void show() {
        System.out.println("我是手机产品");
    }
}

public class Laptop implements Product {
    public void show() {
        System.out.println("我是笔记本产品");
    }
}

// 工厂接口
public interface ProductFactory {
    Product createProduct();
}

// 具体工厂
public class PhoneFactory implements ProductFactory {
    public Product createProduct() {
        return new Phone();
    }
}

public class LaptopFactory implements ProductFactory {
    public Product createProduct() {
        return new Laptop();
    }
}

// 测试
public class FactoryTest {
    public static void main(String[] args) {
        ProductFactory factory = new PhoneFactory();
        Product product = factory.createProduct();
        product.show(); // 输出：我是手机产品
    }
}
```

### 责任链模式

责任链模式将多个处理器（Handler）连接成一条链，**每个处理器自行决定是否处理请求或传递给下一个处理器**，适用于**请求可由多个对象处理但具体处理者不明确的情况**。

使用场景

- Java Servlet 过滤器链
- Spring Security 过滤器链
- OA 审批流程、异常处理链

```java
// 抽象处理器
public abstract class Handler {
    protected Handler next;

    public void setNext(Handler next) {
        this.next = next;
    }

    public abstract void handleRequest(int level);
}

// 具体处理器
public class Manager extends Handler {
    public void handleRequest(int level) {
        if (level <= 1) {
            System.out.println("经理处理了请求");
        } else if (next != null) {
            next.handleRequest(level);
        }
    }
}

public class Director extends Handler {
    public void handleRequest(int level) {
        if (level <= 2) {
            System.out.println("主管处理了请求");
        } else if (next != null) {
            next.handleRequest(level);
        }
    }
}

// 测试
public class ChainTest {
    public static void main(String[] args) {
        Handler manager = new Manager();
        Handler director = new Director();
        manager.setNext(director);

        manager.handleRequest(1); // 输出：经理处理了请求
        manager.handleRequest(2); // 输出：主管处理了请求
    }
}
```

## 3. AOP

### 对AOP的理解

AOP 是一种编程思想，用来将横切关注点（**cross-cutting concerns**）从业务逻辑中分离出来。

在实际开发中，**日志记录、权限校验、事务管理、安全控制、异常处理**等，往往散布在多个模块中，如果都写在业务逻辑里会造成代码重复和耦合性高。

AOP 就是为了解决这个问题：
 它在 **不修改原有代码逻辑** 的前提下，**将这些通用功能抽离出来并统一管理**。

### AOP实现记录日志

定义切面类

```
@Aspect
@Component
public class LogAspect {

    @Pointcut("execution(* com.example.service..*(..))")
    public void logPointcut() {}

    @Before("logPointcut()")
    public void before(JoinPoint joinPoint) {
        System.out.println("方法开始执行：" + joinPoint.getSignature());
    }

    @AfterReturning(pointcut = "logPointcut()", returning = "result")
    public void afterReturn(JoinPoint joinPoint, Object result) {
        System.out.println("方法执行结束：" + joinPoint.getSignature() + " 返回值：" + result);
    }

    @AfterThrowing(pointcut = "logPointcut()", throwing = "ex")
    public void afterThrowing(JoinPoint joinPoint, Exception ex) {
        System.out.println("方法异常：" + joinPoint.getSignature() + " 异常信息：" + ex.getMessage());
    }
}
```

- `@Aspect`：声明当前类为切面类。
- `@Pointcut`：定义切点表达式，拦截哪些方法。
- `@Before`、`@AfterReturning`、`@AfterThrowing`：定义增强逻辑，在方法调用前后、抛异常时插入日志逻辑。

### AOP使用到什么设计模式

| 设计模式                                 | 作用描述                                                     |
| ---------------------------------------- | ------------------------------------------------------------ |
| **代理模式** (Proxy Pattern)             | AOP 的底层就是通过代理对象增强目标方法的功能（Spring AOP 默认基于 JDK 动态代理或 CGLIB） |
| **策略模式** (Strategy Pattern)          | Spring AOP 中，Advice 的执行策略是可配置的，比如执行顺序、异常处理策略等 |
| **责任链模式** (Chain of Responsibility) | 多个 Advice（通知）按顺序执行，构成一个调用链条              |

### AOP实现机制

| 项目     | 实现方式                                  |
| -------- | ----------------------------------------- |
| 基于接口 | 使用 JDK 动态代理，接口为目标对象的父类   |
| 基于类   | 使用 CGLIB 字节码生成代理子类（无接口时） |

## 4. 数据库表一对多和多对多的区别

### 一对多

一个表中的一条记录对应另一个表中的多条记录。

假设有两个实体：

- 用户表（user）
- 订单表（order）

一个用户可以有多个订单，但一个订单只能属于一个用户。

```
-- 用户表
CREATE TABLE user (
    id INT PRIMARY KEY,
    username VARCHAR(50)
);

-- 订单表（外键指向用户表）
CREATE TABLE orders (
    id INT PRIMARY KEY,
    order_no VARCHAR(50),
    user_id INT,
    FOREIGN KEY (user_id) REFERENCES user(id)
);
```

特点：

- **外键通常存在于“多”的那一方**（上例中是 orders 表）。
- 数据查询相对简单，可以用 JOIN 关联查询。

### 多对多

一个表中的多条记录可以对应另一个表中的多条记录，反之亦然。

假设有两个实体：

- 学生表（student）
- 课程表（course）

一个学生可以选多门课程，一门课程也可以被多个学生选。

需要一个**中间表（关联表）**来维护这种多对多关系：

```
-- 学生表
CREATE TABLE student (
    id INT PRIMARY KEY,
    name VARCHAR(50)
);

-- 课程表
CREATE TABLE course (
    id INT PRIMARY KEY,
    name VARCHAR(50)
);

-- 学生课程关联表
CREATE TABLE student_course (
    student_id INT,
    course_id INT,
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (student_id) REFERENCES student(id),
    FOREIGN KEY (course_id) REFERENCES course(id)
);
```

特点：

- **必须引入中间关联表**。
- 查询时需要用两个 JOIN 才能得到关联数据（例如：查一个学生选了哪些课程）。
- 常用于社交关系、标签系统、收藏等功能。

| 比较项     | 一对多                 | 多对多                 |
| ---------- | ---------------------- | ---------------------- |
| 定义       | 一条记录对应多条记录   | 多条记录对应多条记录   |
| 表结构     | 外键在“多”方表中       | 引入第三张“关联表”     |
| 举例       | 用户和订单、分类和文章 | 学生和课程、用户和角色 |
| 查询复杂度 | 相对简单               | 稍复杂，需使用中间表   |
| 数据维护   | 简单                   | 需要同步维护中间表记录 |
