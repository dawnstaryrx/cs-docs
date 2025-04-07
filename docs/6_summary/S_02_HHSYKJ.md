---
sidebar_position: 2
---

# 恒华数元科技

## 1. #和$的区别

在 MyBatis 中，`#` 和 `$` 都可以用于参数占位符，但它们的行为不同，涉及到 SQL 预编译和安全性问题：

### 1. `#`（推荐使用）

- **使用方式**：

  ```
  SELECT * FROM user WHERE name = #{name}
  ```

- **解析方式**：

  - `#{}` 代表**占位符**，在执行 SQL 语句时，MyBatis 会将 `#{}` 内的值作为**参数绑定**，并使用 **JDBC 预编译**（PreparedStatement）处理参数。
  - MyBatis 会将 `#{name}` 替换为 **?**，然后在执行 SQL 时，使用 **参数填充** 方式进行传值。

- **优势**：

  1. **防止 SQL 注入**：由于是预编译模式，参数不会直接拼接到 SQL 语句中，可以防止 SQL 注入攻击。
  2. **提高查询性能**：数据库会对 SQL 进行预编译，提高执行效率。

------

### 2. `$`（不推荐直接用于用户输入）

- **使用方式**：

  ```
  SELECT * FROM user ORDER BY ${column}
  ```

- **解析方式**：

  - `${}` 代表 **字符串拼接**，MyBatis 会直接将 `${}` 内的值拼接到 SQL 语句中，而不会使用预编译。

  - 例如：

    ```
    SELECT * FROM user ORDER BY name
    ```

  - **不会被预编译**，直接在 SQL 语句中拼接字符串，可能会导致 SQL 注入风险。

- **适用场景**：

  1. 适用于**动态字段、表名、列名**（这些内容无法使用 `#{} `）。
  2. **不要用于用户输入参数**，否则容易被 SQL 注入攻击。

### 3. **推荐使用**

- **大多数情况下，使用 `#{} ` 来传递参数**，防止 SQL 注入。
- **只有在需要动态拼接表名、列名时，才使用 `${}`**，但要做好安全性检查，避免用户输入恶意 SQL 语句。

## 2. 索引失效的原因

### 1. **索引列使用了函数**

- **问题**：在 `WHERE` 条件中对索引列使用了**函数**，会导致索引失效，因为 MySQL 需要对每一行的数据进行计算，无法直接使用索引。

- **示例（索引失效）**：

  ```
  SELECT * FROM users WHERE LEFT(name, 3) = 'Tom';
  ```

  - `LEFT(name, 3)` 会对 `name` 列进行处理，导致索引失效。

- **优化方案（索引生效）**：

  - 避免在索引列上使用函数，改为：

    ```
    SELECT * FROM users WHERE name LIKE 'Tom%';
    ```

  - `LIKE 'Tom%'` 允许 MySQL **利用 B+ 树索引** 进行**范围查询**。

### 2. **隐式类型转换**

- **问题**：索引列和查询值类型不匹配，导致 MySQL 自动转换类型，使索引失效。

- **示例（索引失效）**：

  ```
  SELECT * FROM users WHERE phone = 13812345678;
  ```

  - 假设 `phone` 列是 `VARCHAR` 类型，而 `13812345678` 是 `INT` 类型，MySQL 会自动转换 `phone` 为数值，导致索引失效。

- **优化方案（索引生效）**：

  ```
  SELECT * FROM users WHERE phone = '13812345678';
  ```

  - **确保查询值的类型与索引列的类型一致**。

### 3. **使用 `!=`、`<>`、`NOT IN` 或 `NOT LIKE`**

- **问题**：索引通常用于快速定位匹配的数据，而 `!=`、`<>` 或 `NOT IN` 可能会导致全表扫描。

- **示例（索引失效）**：

  ```
  SELECT * FROM users WHERE age != 18;
  ```

  - `!=` 不能利用 B+ 树索引进行范围查找，导致**全表扫描**。

- **优化方案（索引生效）**：

  - **改用范围查询**：

    ```
    SELECT * FROM users WHERE age < 18 OR age > 18;
    ```

  - 如果可以，考虑使用 **`BETWEEN` 或 `IN`**。

### 4. **`OR` 连接条件**

- **问题**：`OR` 可能导致索引失效，特别是当 `OR` 两侧只有一个字段有索引时。

- **示例（索引失效）**：

  ```
  SELECT * FROM users WHERE name = 'Tom' OR age = 25;
  ```

  - 如果 `name` 有索引但 `age` 没有，MySQL 可能会放弃索引，进行全表扫描。

- **优化方案（索引生效）**：

  - **改用 `UNION ALL`**（前提是 `name` 和 `age` 都有索引）：

    ```
    SELECT * FROM users WHERE name = 'Tom'
    UNION ALL
    SELECT * FROM users WHERE age = 25;
    ```

  - **确保 `OR` 两侧的列都建立了索引**。

### 5. **`LIKE` 模糊查询（前置 `%`）**

- **问题**：`LIKE` 以 `%` 开头时，索引失效，因为无法从 B+ 树索引的**左侧**进行匹配。

- **示例（索引失效）**：

  ```
  SELECT * FROM users WHERE name LIKE '%Tom%';
  ```

  - `%` 开头会导致 MySQL **全表扫描**。

- **优化方案（索引生效）**：

  - 改为：

    ```
    SELECT * FROM users WHERE name LIKE 'Tom%';
    ```

  - **索引可以用于 `LIKE 'Tom%'` 这样的前缀匹配查询**。

### 6. **范围查询 (`>`, `<`, `BETWEEN`) 影响索引排序**

- **问题**：如果 `WHERE` 子句中包含范围查询，索引列后面的字段的索引可能不会被使用。

- **示例（索引失效）**：

  ```
  SELECT * FROM users WHERE age > 20 AND city = 'Beijing';
  ```

  - 假设 `(age, city)` 是联合索引，**由于 `age > 20` 是范围查询，`city` 列的索引可能无法使用**。

- **优化方案（索引生效）**：

  - **调整索引顺序**，将 `city` 放在前面：

    ```
    CREATE INDEX idx_users ON users(city, age);
    ```

  - **或拆分查询**：

    ```
    SELECT * FROM users WHERE age > 20 
    INTERSECT
    SELECT * FROM users WHERE city = 'Beijing';
    ```

### 7. **索引字段未在 `WHERE` 或 `JOIN` 条件中**

- **问题**：索引字段如果未在 `WHERE` 或 `JOIN` 条件中使用，查询时不会用到索引。

- **示例（索引失效）**：

  ```
  SELECT name FROM users;
  ```

  - 假设 `users` 表的 `id` 有索引，但查询不涉及 `id`，所以索引不会生效。

- **优化方案（索引生效）**：

  - 添加 `WHERE` 条件，使索引列参与查询：

    ```
    SELECT name FROM users WHERE id > 1000;
    ```

### 8. **数据分布导致索引不生效**

- **问题**：如果索引列的值**重复度很高**（例如 `gender` 只有 `Male` 和 `Female`），MySQL 可能会放弃索引，进行**全表扫描**。

- **示例（索引失效）**：

  ```
  SELECT * FROM user WHERE gender = 'Male';
  ```

  - `gender` 只有两个值，**索引区分度太低**，MySQL 可能选择全表扫描。

- **优化方案（索引生效）**：

  - **避免在低区分度字段上建立索引**，可以用索引辅助查询：

    ```
    SELECT * FROM users WHERE gender = 'Male' AND id > 1000;
    ```

  - `id` 是主键索引，结合 `gender` 可以提高查询效率。

### 9. **表太小，优化器自动放弃索引**

- **问题**：如果表数据量很少（比如几十行），MySQL 可能会**直接进行全表扫描**，因为它认为扫描整个表比使用索引更快。

- **示例（索引失效）**：

  ```
  SELECT * FROM small_table WHERE name = 'Tom';
  ```

  - 由于 `small_table` 只有几十行，索引的作用不大，优化器可能直接选择全表扫描。

- **优化方案（索引生效）**：

  - **如果表数据量少，索引意义不大，可以考虑去掉索引**。
  - **当数据量增大时，索引才会生效**。

## 3. SQL分页查询

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

## 4. 联表查询

联表查询（JOIN Query）是数据库中用于**关联多张表的数据**并进行查询的一种方式。它通常用于**表之间有关系**（如主外键关系）的情况，以便获取完整的数据。

### **（1）INNER JOIN（内连接）**

**作用**：返回两张表**匹配的记录**（即**只有匹配成功的数据才会返回**）。

**SQL 语法**

```sql
SELECT a.*, b.*
FROM tableA a
INNER JOIN tableB b ON a.id = b.a_id;
```

**示例** 假设有两张表：

- `students`（学生表）
  - `id`（学生ID）
  - `name`（学生姓名）
- `scores`（成绩表）
  - `id`（成绩ID）
  - `student_id`（学生ID，外键）
  - `score`（分数）

```sql
SELECT students.name, scores.score
FROM students
INNER JOIN scores ON students.id = scores.student_id;
```

**结果**

| name | score |
| ---- | ----- |
| 张三 | 90    |
| 李四 | 85    |

**特点**：

- **只有在 `students.id = scores.student_id` 匹配时，才会返回数据**
- **如果 `students` 中的某个学生没有成绩，则不会出现在结果中**

------

### **（2）LEFT JOIN（左连接）**

**作用**：返回**左表的所有数据**，右表**如果匹配不上则返回 NULL**。

**SQL 语法**

```sql
SELECT a.*, b.*
FROM tableA a
LEFT JOIN tableB b ON a.id = b.a_id;
```

**示例**

```sql
SELECT students.name, scores.score
FROM students
LEFT JOIN scores ON students.id = scores.student_id;
```

**结果**

| name | score |
| ---- | ----- |
| 张三 | 90    |
| 李四 | 85    |
| 王五 | NULL  |

**特点**：

- **左表（students）全部返回**，右表（scores）如果找不到匹配项，则返回 `NULL`。
- 适用于**查询某张表的所有数据，即使另一张表没有对应的记录**。

------

### **（3）RIGHT JOIN（右连接）**

**作用**：返回**右表的所有数据**，左表**如果匹配不上则返回 NULL**。

**SQL 语法**

```sql
SELECT a.*, b.*
FROM tableA a
RIGHT JOIN tableB b ON a.id = b.a_id;
```

**示例**

```sql
SELECT students.name, scores.score
FROM students
RIGHT JOIN scores ON students.id = scores.student_id;
```

**特点**：

- **右表（scores）全部返回**，左表（students）如果找不到匹配项，则返回 `NULL`。
- 很少使用，通常用 **LEFT JOIN 反转表顺序** 代替。

------

### **（4）FULL JOIN（全外连接）**

**作用**：返回**左右表所有数据**，如果匹配不上则返回 `NULL`。

**SQL 语法**

```sql
SELECT a.*, b.*
FROM tableA a
FULL JOIN tableB b ON a.id = b.a_id;
```

**示例**

```sql
SELECT students.name, scores.score
FROM students
FULL JOIN scores ON students.id = scores.student_id;
```

**特点**：

- 需要数据库支持（MySQL **不支持 FULL JOIN**，可用 `UNION` 代替）。

```sql
SELECT students.name, scores.score
FROM students
LEFT JOIN scores ON students.id = scores.student_id
UNION
SELECT students.name, scores.score
FROM students
RIGHT JOIN scores ON students.id = scores.student_id;
```

------

### **（5）CROSS JOIN（笛卡尔积）**

**作用**：不加 `ON` 条件，返回**两张表的所有组合**（乘法表效果）。

**SQL 语法**

```sql
SELECT a.*, b.*
FROM tableA a
CROSS JOIN tableB b;
```

**示例**

```sql
SELECT students.name, scores.score
FROM students
CROSS JOIN scores;
```

**特点**：

- **没有关联条件**，会生成 **m × n** 行的数据，通常用于测试或数据组合。
