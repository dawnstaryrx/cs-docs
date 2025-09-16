---
sidebar_position: 6
---

# Java框架

## 一、Spring

### 1. `@Controller`和`@RestController`有什么区别？

####  `@Controller`

- `@Controller` 是 Spring MVC 提供的用于定义控制器的注解，主要用于返回 **视图**（HTML、JSP 等）。
- 需要配合 `@ResponseBody` 注解，才能返回 JSON 数据。

####  `@RestController`

- `@RestController` 是 `@Controller` 和 `@ResponseBody` 的组合注解，专门用于 **返回 JSON 数据**，用于 RESTful API 开发。
- **默认所有方法的返回值都是 JSON 格式**，不需要手动添加 `@ResponseBody`。

| 注解              | 适用场景                                                     |
| ----------------- | ------------------------------------------------------------ |
| `@Controller`     | 主要用于返回页面视图（如 HTML、JSP），也可以结合 `@ResponseBody` 返回 JSON 数据。 |
| `@RestController` | 主要用于 RESTful API 开发，默认返回 JSON 数据，适用于前后端分离项目。 |

### 2. `@Autowired`和`@Resource`有什么区别？

- `@Autowired` 是 Spring 提供的注解，`@Resource` 是 JDK 提供的注解。
- `@Autowired` 默认的注入方式为`byType`（根据类型进行匹配），`@Resource`默认注入方式为 `byName`（根据名称进行匹配）。
- 当一个接口存在多个实现类的情况下，`@Autowired` 和`@Resource`都需要通过名称才能正确匹配到对应的 Bean。`@Autowired` 可以通过 `@Qualifier` 注解来显式指定名称，`@Resource`可以通过 `name` 属性来显式指定名称。
- `@Autowired` 支持在构造函数、方法、字段和参数上使用。`@Resource` 主要用于字段和方法上的注入，不支持在构造函数或参数上使用。

### 3. Spring框架中的单例Bean是线程安全的吗？

#### Spring框架中的Bean是单例的吗？

是的。Spring框架中有一个注解`@Scope`，设置当前的Bean是不是单例的，默认是Singleton。

```java
@Service
@Scope("singleton")
public class UserServiceImpl implements UserService{
    ......
}
```

- singleton：Bean在每个Spring IOC 容器中只有一个实例
- prototype：一个Bean的定义可以有多个实例

#### 是线程安全的吗？

不是线程安全的。

因为一般在Spring的Bean中都是注入无状态的对象，没有线程安全问题，所以在某种程度上说Spring的单例Bean是线程安全的。

如果在Bean中定义了可修改的成员变量，是要考虑线程安全问题的，可以使用多例或者加锁来解决。

### 4. 什么是AOP，你的项目中有没有使用到AOP？

AOP称为面向切面编程，用于将那些与业务无关，但却对多个对象产生影响的公共行为和逻辑，抽取并封装为一个可重用的模块，这个模块被命名为“切面”Aspect，减少了系统中的重复代码，降低了模块间的耦合度，同时提高了系统的可维护性。

常见的AOP使用场景：

- 记录操作日志
  - 获取用户名、请求方式、访问地址、操作时间等，记录到数据库日志表
  - 使用AOP的环绕通知+切点表达式（找到要记录日志的方法），通过环绕通知的参数获取方法的参数（类、方法、注解、请求方式等），获取到这些参数以后，保存到数据库。
  - https://www.bilibili.com/video/BV1yT411H7YK?spm_id_from=333.788.player.switch&vd_source=514b283567840d8ef60eac89dd18b14b&p=37
- 缓存处理
- Spring中内置的事务处理

### 5. Spring中的事务是如何实现的？

Spring支持编程式事务管理和声明式事务管理两种方式。

- 编程式事务管理：需要使用TransactionTemplate来进行实现，对业务代码有侵入性，项目中很少使用。
- 声明式事务管理：声明式事务管理建立在AOP之上。其**本质是通过AOP功能，对方法前后进行拦截，将事务处理的功能编织到拦截的方法中，也就是在目标方法开始之前加入一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。**

### 6. Spring中事务失效的场景有哪些？

- 异常捕获处理
  - 原因：事务通知只有捉到了目标抛出的异常，才能进行后续的回滚处理，如果目标自己处理掉异常，事务通知无法知悉。
  - 解决：在catch块添加throw new RuntimeException(e)抛出
- 抛出检查异常
  - 原因：Spring默认只会回滚非检查异常
  - 解决：配置rollbackFor属性，`@Transactional(rollbackFor=Exception.class)`
  - 检查异常是编译时异常，在编译阶段 Java 强制要求开发者**必须**对其进行处理，否则代码无法编译通过。
  - 非检查异常是在运行时抛出的异常，**编译器不会强制要求开发者处理**，如果不进行处理，程序可能在运行时崩溃。
- 非public方法导致的事务失效
  - 原因：Spring为方法创建代理、添加事务通知，前提条件都是该方法是public的
  - 解决：改为public

### 7. Spring Bean的生命周期

1. 通过BeanDefination获取Bean的定义信息；
2. 调用构造函数实例化Bean；
3. Bean的依赖注入；
4. 处理Aware接口；
5. Bean的后置处理器BeanPostProcessor-前置
6. 初始化方法（InitializingBean、init-method）
7. Bean的后置处理器BeanPostProcessor-后置
8. 销毁Bean

### 8. Spring中的循环引用

- 循环依赖：循环依赖就是循环引用，也就是两个或两个以上的Bean互相持有对方，最终形成闭环。比如A依赖于B，B依赖于A。
- 循环依赖在Spring中是允许存在的，Spring框架依据三级缓存已经解决了大部分循环依赖
  - 一级缓存：单例池，缓存已经经历了完整的生命周期，已经初始化完成的Bean对象
  - 二级缓存：缓存早期的Bean对象（生命周期还没走完）
  - 三级缓存：缓存的是ObjectFactory，表示对象工厂，用来创建某个对象

构造方法出现循环依赖怎么解决？

由于在Bean的生命周期中构造函数是第一个执行的，Spring框架并不能解决构造函数的依赖注入

解决：使用@Lazy进行懒加载，什么时候需要Bean对象再进行Bean对象的创建

### 9. SpringMVC执行流程

使用SpringMVC后，所有的请求都需要经过DispatcherServlet前端控制器，该类提供了一个doDispatcher方法，有关请求处理和结果响应的所有流程都在该方法中完成。

1. 用户发送请求到前端控制器DispatcherServlet
2. DispatcherServlet收到请求调用HandlerMapping（处理器映射器）
3. HandlerMapping找到具体的处理器，生成处理器对象及处理器拦截器（如果有），再一起返回给DispatcherServlet
4. DispatcherServlet调用HandlerAdapter（处理器适配器）
5. HandlerAdapter经过适配调用具体的处理器（Handler/Controller）
6. 方法添加了@ResponseBody
7. 通过HttpMessageConverter来返回结果转换为JSON并响应、

### 10. Spring框架常见注解

Spring常见注解

![image-20250326161325848](.\assets\image-20250326161325848.png)

SpringMVC常见注解

![image-20250326161544396](.\assets\image-20250326161544396.png)

SpringBoot常见注解

![image-20250326161609211](.\assets\image-20250326161609211.png)

### 11. Spring、SpringMVC、Springboot和SpringCloud的区别？

 **Spring Framework**

- **概述**：Spring 是一个开源的企业级应用程序开发框架，提供了全面的基础设施支持。它的核心特性包括依赖注入（DI）和面向切面编程（AOP）。
- 功能
  - 提供了 IoC（控制反转）容器，帮助管理对象的生命周期。
  - 支持事务管理。
  - 提供与不同持久化技术（如 JDBC、JPA、Hibernate）的集成。
  - 提供了丰富的模块，如 Spring Security、Spring Data、Spring Batch 等。

**Spring MVC**

- **概述**：Spring MVC 是 Spring 框架的一部分，专注于构建基于 Web 的应用程序。它实现了 MVC（模型-视图-控制器）设计模式。
- 功能
  - 处理 HTTP 请求和响应。
  - 提供了控制器、视图解析器和模型对象的支持。
  - 支持 RESTful Web 服务的构建。
  - 集成了数据绑定和验证功能。

**Spring Boot**

- **概述**：Spring Boot 是 Spring 框架的一个扩展，旨在简化 Spring 应用程序的开发过程。它提供了一种快速、无配置的方式来创建独立的、生产级别的 Spring 应用程序。
- 功能
  - 通过约定优于配置的原则，减少了大量的配置工作。
  - 提供了内嵌的应用服务器（如 Tomcat、Jetty），使得应用程序可以独立运行。
  - 提供了自动配置功能，能够根据项目的依赖自动配置 Spring 组件。
  - 提供了 Spring Boot Starter，简化了依赖管理。

**SpringCloud**

- 概述：Spring Cloud 是一个为分布式系统提供开发工具的框架，主要用于构建微服务架构。它基于 Spring Framework，并与 Spring Boot 紧密集成。
- 特性
  - **服务发现**：通过 Eureka 或 Consul 等服务注册与发现工具，微服务可以动态地找到彼此。
  - **负载均衡**：使用 Ribbon 或 Spring Cloud LoadBalancer 实现客户端负载均衡。
  - **API 网关**：使用 Spring Cloud Gateway 或 Zuul 作为 API 网关，处理路由、过滤、负载均衡等。
  - **配置管理**：使用 Spring Cloud Config 进行集中配置管理，支持动态刷新配置。
  - **断路器**：通过 Hystrix 或 Resilience4j 实现断路器模式，提高系统的容错能力。
  - **分布式追踪**：使用 Spring Cloud Sleuth 和 Zipkin 进行分布式系统的追踪，帮助监控和调试。
  - **消息驱动微服务**：集成 RabbitMQ、Kafka 等消息中间件，支持异步消息传递。

**总结**

- **Spring** 是一个完整的企业级应用程序开发框架。
- **Spring MVC** 是 Spring 的一个模块，专注于构建 Web 应用程序。
- **Spring Boot** 是一个用于简化 Spring 应用程序开发的框架，使得构建和部署变得更加简单。
- **Spring Cloud** 是一个用于构建和管理微服务架构的框架，提供了服务发现、负载均衡、配置管理、断路器等功能。它与 **Spring** 和 **Spring Boot** 紧密集成，简化了微服务的开发和运维。

## 二、SpringBoot

### 1. SpringBoot启动流程

![image-20250327155248311](.\assets\image-20250327155248311.png)

### 2. IOC容器初始化流程

![image-20250916093315729](.\assets\image-20250916093315729.png)

### 3. Bean生命周期

![image-20250916093439277](.\assets\image-20250916093439277.png)

### 4. Bean循环依赖

![image-20250916094310813](.\assets\image-20250916094310813.png)

### 5. SpringMVC执行流程

![image-20250916094341685](.\assets\image-20250916094341685.png)

### 6. SpringBoot自动配置原理

1. 在Springboot项目中的引导类上有一个注解@SpringBootApplication，这个注解是对三个注解进行了封装，分别是：
   1. @SpringBootConfiguration
   2. @EnableAutoConfiguration
   3. @ComponentScan
2. 其中，@EnableAutoConfiguration是实现自动化配置的核心注解。该注解通过@Import注解导入对应的配置选择器。内部就是读取了该项目和该项目引用的Jar包的classpath路径下META-INF/spring.factories文件中的所配置的类的全类名。在这些配置类中所定义的Bean会根据条件注解所指定的条件来决定是否需要将其导入到Spring容器中。
3. 条件判断会有像@ConditionalOnClass这样的注解，判断是否有相对应的class文件，如果有则加载该类，把这个配置类的所有Bean放入spring容器中使用。

