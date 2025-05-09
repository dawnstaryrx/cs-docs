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



## 4. Vue和React的区别

语法与设计理念

| 对比点     | Vue                                            | React                          |
| ---------- | ---------------------------------------------- | ------------------------------ |
| 组件写法   | HTML、CSS、JS 三者分离 (`.vue` 单文件组件)     | 使用 JSX（JS + HTML 混写）     |
| 数据绑定   | 双向绑定（`v-model`）                          | 单向数据流                     |
| 模板语法   | 模板指令（如：`v-if`, `v-for`）                | JSX 语法，表达式放在 `{}` 中   |
| 响应式系统 | Vue2 用 `Object.defineProperty`，Vue3 用 Proxy | React 通过状态更新触发重新渲染 |



## 5. 项目中多线程怎么使用的



## 6. 什么是无头浏览器，项目中怎么用的

