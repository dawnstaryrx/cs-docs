---
sidebar_position: 5
---

# 亚信万联

## 1. 介绍一下Spring常用注解

Spring常见注解

![image-20250326161325848](.\assets\image-20250326161325848.png)

SpringMVC常见注解

![image-20250326161544396](.\assets\image-20250326161544396.png)

SpringBoot常见注解

![image-20250326161609211](.\assets\image-20250326161609211.png)

## 2. 分页查询

**语法：**

```
SELECT * FROM table_name 
ORDER BY column_name 
LIMIT offset, page_size;
```

- `offset`：跳过的记录数（`(page_number - 1) * page_size`）。
- `page_size`：每页显示的数量。

**示例：查询第 2 页，每页 10 条数据**

```
SELECT * FROM users 
ORDER BY id 
LIMIT 10, 10; -- 跳过 10 条数据，获取 10 条
```

💡 **优化建议**：

- `LIMIT` + `OFFSET` 方式适用于小数据量，如果数据量大，**使用索引**或**覆盖索引**优化。
- 可以用 **`WHERE id > 上一页最大 ID`** 的方式进行高效分页。

## 3. 介绍一下项目中的SpringSecurity

Spring Security 是用于 **认证（Authentication）** 和 **授权（Authorization）** 的安全框架。本项目中的 Spring Security 核心职责是：

1. **用户登录认证处理（基于自定义 filter 和 provider）**
2. **基于 JWT 的鉴权机制（用户、管理员权限隔离）**
3. **统一异常处理（认证异常、权限异常、自定义系统异常）**
4. **多过滤链支持路径隔离处理**



## 4. Vue和React的区别

语法与设计理念

| 对比点     | Vue                                            | React                          |
| ---------- | ---------------------------------------------- | ------------------------------ |
| 组件写法   | HTML、CSS、JS 三者分离 (`.vue` 单文件组件)     | 使用 JSX（JS + HTML 混写）     |
| 数据绑定   | 双向绑定（`v-model`）                          | 单向数据流                     |
| 模板语法   | 模板指令（如：`v-if`, `v-for`）                | JSX 语法，表达式放在 `{}` 中   |
| 响应式系统 | Vue2 用 `Object.defineProperty`，Vue3 用 Proxy | React 通过状态更新触发重新渲染 |



## 5. 项目中多线程怎么使用的

根据用户提交的标题和描述内容，调用 AI 接口生成对应的 HTML 页面。为了加快生成速度，采用了 **多线程并发执行每个页面的生成操作**。

### 5.1 使用线程池

```
ExecutorService executor = Executors.newFixedThreadPool(6);
```

- 创建一个固定大小的线程池；
- 避免了每次都新建线程的性能开销；
- 保证最多并发处理 x 个任务，不会造成线程数量无限增长。

### 5.2 使用 `CompletableFuture.supplyAsync(...)` 异步执行任务

```
CompletableFuture<Pair<Integer, String>> future = CompletableFuture.supplyAsync(() -> { ... }, executor);
```

- 使用 `CompletableFuture` 将每个 HTML 页面的生成任务交给线程池异步执行；
- 每个任务负责处理一个标题和描述，最终生成一个 HTML 页面内容；
- 每个任务将 `Pair<index, html>` 作为结果返回，以便最后可以按原顺序排序。

### 5.3 使用 `CompletableFuture.join()` 等待所有任务完成

```
List<Pair<Integer, String>> results = futures.stream()
    .map(CompletableFuture::join)
    .sorted(Comparator.comparingInt(Pair::getKey))
    .toList();
```

- 使用 `join()` 阻塞等待每个任务完成，收集结果；
- 使用 `index` 排序，确保输出结果的顺序与输入顺序一致（即 HTML1 到 HTML6）；

| 技术点                            | 说明                           |
| --------------------------------- | ------------------------------ |
| `ExecutorService`                 | 创建固定线程池，提高资源利用   |
| `CompletableFuture.supplyAsync()` | 将任务交由线程池异步执行       |
| `join()`                          | 阻塞等待任务完成               |
| 排序与索引                        | 保证多线程执行结果的顺序一致性 |
| 异常处理                          | 防止单个任务失败影响整个流程   |

## 6. 什么是无头浏览器，项目中怎么用的

无头浏览器（Headless Browser）是指没有图形用户界面（GUI）的浏览器，它可以在命令行或代码中运行，执行和真实浏览器一样的操作，如加载网页、点击按钮、执行 JavaScript 脚本、截屏等，但不会显示界面。

常见的无头浏览器包括：

- `Puppeteer`（基于 Chromium）

**优点**：

- 更轻量，资源占用小；
- 更适合自动化测试、页面抓取、内容截图等任务；
- 可用于服务端环境如服务器、Docker容器等。

> 无头浏览器是一种不显示 UI 的浏览器，适合在服务器上进行自动化操作。在我的项目中，我使用 Puppeteer（无头 Chrome）将动态生成的 HTML 页面转换为图片，用于导出分享或预览。整个过程包括 Node.js 脚本编写、服务端部署、Postman 接口测试等，解决了高性能服务器截图的问题。
