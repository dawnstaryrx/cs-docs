---
sidebar_position: 13
---

# 津云首创面试

## 一、JRE和JDK的区别

**JRE（Java Runtime Environment）**：Java 运行时环境，包含 JVM（Java Virtual Machine）、核心类库（rt.jar 等）和运行 Java 程序所需的其它组件。用于**运行** Java 程序。

**JDK（Java Development Kit）**：Java 开发工具包，包含完整的 JRE + 开发工具（如 `javac`、`jar`、`javadoc`、`jdb` 等）。用于**开发**和编译 Java 程序。

**关系**：JDK ⊃ JRE。开发者安装 JDK，生产环境通常只需安装 JRE（或更现代的 JRE 已被 JDK 的运行时模块取代，但概念相同）。

补充：

- JDK 提供 `javac`（编译器）、`jconsole`、`jmap`、`jstack` 等工具用于开发与诊断。
- JRE 负责把字节码交给 JVM 解释/执行，并提供标准类库支持。

## 二、==和equals的区别

- `==`：比较**引用**（对象的内存地址）。对于基本类型比较值（如 `int`），对于引用类型比较是否是同一对象引用。
- `equals()`：在 `Object` 类中默认实现也是比较引用（同 `==`），但多数类（如 `String`、`Integer`、自定义类）会重写 `equals()` 来比较**逻辑相等**（即内容或业务标识相等）。

## 三、如何降低SQL注入的风险

1. **使用预编译语句（PreparedStatement / 参数化查询）**：避免拼接 SQL 字符串，所有外部输入作为参数绑定。
2. **使用 ORM 框架**（如 MyBatis、Hibernate）并使用其参数占位符，防止直接拼接。
3. **白名单校验**：对于列名、排序字段等无法用参数绑定的值，采用白名单校验。
4. **最小权限原则**：数据库账号只分配必要权限（比如只读、仅对特定表有 insert/update 权限）。
5. **输入校验**：对输入长度、类型、格式进行校验（但不能仅依赖于校验防注入）。
6. **使用 Web 应用防火墙（WAF）** 作为补充防护。
7. **安全日志与审计**：记录可疑 SQL 与异常行为，便于追踪。
8. **避免显示数据库错误给用户**：防止泄露 SQL 结构或表名等信息。

## 四、如何解决跨域

**背景**：浏览器出于安全，会阻止不同源（协议、域名或端口任一不同）的前端代码访问后端接口。解决跨域有多种方法，分为后端配置、前端代理等。

**常用方案**

1. **后端设置 CORS 响应头（推荐）**

   - `Access-Control-Allow-Origin`：允许的来源（可为具体域名或 `*`，注意带凭证时不能用 `*`）。
   - `Access-Control-Allow-Methods`：允许的方法（GET, POST, PUT, DELETE...）。
   - `Access-Control-Allow-Headers`：允许的请求头。
   - `Access-Control-Allow-Credentials`：是否允许携带凭证（cookies、Authorization），值为 `true` 时 `Access-Control-Allow-Origin` 必须为具体域名。
   - `Access-Control-Max-Age`：预检请求缓存时间。

   **Spring Boot 示例**

   ```
   // 全局 CORS 配置（WebMvcConfigurer）
   @Configuration
   public class CorsConfig implements WebMvcConfigurer {
       @Override
       public void addCorsMappings(CorsRegistry registry) {
           registry.addMapping("/**")
               .allowedOrigins("https://example.com") // 或 .allowedOriginPatterns("*")
               .allowedMethods("GET","POST","PUT","DELETE","OPTIONS")
               .allowedHeaders("*")
               .allowCredentials(true)
               .maxAge(3600);
       }
   }
   ```

   或使用注解：

   ```
   @CrossOrigin(origins = "https://example.com", allowCredentials = "true")
   @RestController
   public class MyController { ... }
   ```

2. **前端代理（开发环境常用）**

   - 在开发阶段使用 webpack devServer、Vite 或 create-react-app 的 proxy，将请求转发到后端，避免浏览器跨域限制。
   - 仅用于开发，不适合生产。

3. **JSONP（仅限 GET）**

   - 旧方案，安全性和限制较多，现在基本不用。

4. **反向代理 / 同源部署**

   - 使用 Nginx 反向代理后端接口，使前端与接口同源（同域名/端口），消除跨域。
   - 生产中常用此方法（Nginx 转发、API 网关）。

5. **使用 postMessage（跨文档通信）**

   - 当前端在不同 iframe/window 间需要通信时使用，不用于一般 API 跨域。

## 五、如何实现用户和权限的多对多

**数据库表设计（示例：User - Role）**

- `user` 表：`id, username, password, ...`
- `role` 表：`id, name, description, ...`
- `user_role` 关联表：`user_id, role_id` （联合主键或自增长 id + 唯一索引）

```
CREATE TABLE user (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  username VARCHAR(100) NOT NULL UNIQUE,
  password VARCHAR(255) NOT NULL,
  ... 
);

CREATE TABLE role (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(100) NOT NULL UNIQUE,
  description VARCHAR(255)
);

CREATE TABLE user_role (
  user_id BIGINT NOT NULL,
  role_id BIGINT NOT NULL,
  PRIMARY KEY (user_id, role_id),
  FOREIGN KEY (user_id) REFERENCES user(id),
  FOREIGN KEY (role_id) REFERENCES role(id)
);
```

**扩展：Role 与 Permission 多对多**

- `permission` 表：`id, name, resource, action`
- `role_permission` 表：`role_id, permission_id`

**后端实现（思路）**

- 读取某用户权限：
  - 先查询 `user_role` 得到 role 列表，再通过 `role_permission` 聚合得到权限集合，或者使用 JOIN 一次性取出。
- 授权检查：
  - 将权限缓存到 JWT、Redis 或内存（如 Spring Security 的 `UserDetails` 中带权限）以减少 DB 查询。
- 管理接口：
  - 提供用户-角色分配、角色-权限分配、角色 CRUD 的接口；操作 `user_role` 和 `role_permission` 表。
