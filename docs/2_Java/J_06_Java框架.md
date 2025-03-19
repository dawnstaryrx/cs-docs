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
- `Autowired` 默认的注入方式为`byType`（根据类型进行匹配），`@Resource`默认注入方式为 `byName`（根据名称进行匹配）。
- 当一个接口存在多个实现类的情况下，`@Autowired` 和`@Resource`都需要通过名称才能正确匹配到对应的 Bean。`Autowired` 可以通过 `@Qualifier` 注解来显式指定名称，`@Resource`可以通过 `name` 属性来显式指定名称。
- `@Autowired` 支持在构造函数、方法、字段和参数上使用。`@Resource` 主要用于字段和方法上的注入，不支持在构造函数或参数上使用。

