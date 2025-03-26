---
sidebar_position: 6
slug: /
---

# 1. 中科闻歌

## 1. 面向对象的三大特征，多态

- 封装是指把一个对象的属性隐藏在对象内部，不允许外部对象直接访问对象的内部信息。但是可以提供一些可以被外界访问的方法来操作属性。
- 继承是指**子类继承父类的属性和方法**，从而复用已有代码，并扩展新功能。
- 多态的核心思想是 **“同一个接口，多个实现方式”**，即 **同一个方法在不同的对象上具有不同的行为**。多态指的是 **父类引用指向子类对象**，并且调用的是子类重写的方法，而不是父类的方法。

**多态的三大条件：**

1. **继承（Inheritance）**：必须有父类和子类关系。
2. **方法重写（Override）**：子类必须重写父类的方法。
3. **父类引用指向子类对象**：`Parent obj = new Child();`。

Java 中的多态主要有三种实现方式：

1. **方法重写（Override，多态的基础）**
2. **接口实现（Interface，多态的拓展）**
3. **对象的向上转型（Upcasting，多态的应用）**

### **1. 方法重写（Override）**

方法重写是 **子类对父类方法的重新实现**，使得调用同一个方法时，执行的是子类的实现，而不是父类的实现。

```java
// 父类
class Animal {
    void makeSound() {
        System.out.println("动物发出声音");
    }
}

// 子类
class Dog extends Animal {
    @Override
    void makeSound() {
        System.out.println("狗叫：汪汪汪");
    }
}

class Cat extends Animal {
    @Override
    void makeSound() {
        System.out.println("猫叫：喵喵喵");
    }
}

// 测试多态
public class PolymorphismDemo {
    public static void main(String[] args) {
        Animal myAnimal = new Dog(); // 父类引用指向子类对象
        myAnimal.makeSound(); // 输出：狗叫：汪汪汪

        myAnimal = new Cat();
        myAnimal.makeSound(); // 输出：猫叫：喵喵喵
    }
}
```

**结论：**

- `Animal myAnimal = new Dog();` **父类引用指向子类对象**，调用的是 `Dog` 的 `makeSound()` 方法，而不是 `Animal` 的 `makeSound()` 方法。
- 这种机制让代码更加灵活，不需要修改 `main` 方法，就能适配新的 `Animal` 子类。

------

### **2. 接口实现（Interface）**

接口的多态体现在：**不同的类可以实现同一个接口，并提供不同的实现**。

```java
// 定义接口
interface Shape {
    void draw();
}

// 实现类
class Circle implements Shape {
    @Override
    public void draw() {
        System.out.println("画一个圆形");
    }
}

class Rectangle implements Shape {
    @Override
    public void draw() {
        System.out.println("画一个矩形");
    }
}

// 测试接口的多态
public class InterfacePolymorphism {
    public static void main(String[] args) {
        Shape shape = new Circle();
        shape.draw(); // 输出：画一个圆形

        shape = new Rectangle();
        shape.draw(); // 输出：画一个矩形
    }
}
```

**结论：**

- `Shape` 接口定义了 `draw()` 方法，`Circle` 和 `Rectangle` 实现了该接口。
- 通过 `Shape shape = new Circle();` 和 `shape = new Rectangle();` **实现多态**，具体调用哪个 `draw()` 取决于对象的实际类型。

------

### **3. 对象的向上转型（Upcasting）**

向上转型（Upcasting）是指：**子类对象赋值给父类引用**。

```
Animal animal = new Dog();  // 向上转型，子类转为父类
```

```java
class Parent {
    void show() {
        System.out.println("父类 show 方法");
    }
}

class Child extends Parent {
    @Override
    void show() {
        System.out.println("子类 show 方法");
    }
}

public class UpcastingDemo {
    public static void main(String[] args) {
        Parent obj = new Child(); // 向上转型
        obj.show(); // 输出：子类 show 方法
    }
}
```

**结论：**

- `Parent obj = new Child();` **父类引用指向子类对象**，但调用的是子类的 `show()` 方法。
- **父类引用只能访问父类的方法**，不能访问子类特有的方法。

## 2. 字符串拼接的几种方式

### **1. 使用 `+` 进行字符串拼接**

Java 允许使用 `+` 运算符来拼接字符串，这是最简单直观的方式。

```
public class StringConcatDemo {
    public static void main(String[] args) {
        String str1 = "Hello";
        String str2 = "World";
        String result = str1 + " " + str2 + "!";
        System.out.println(result); // 输出：Hello World!
    }
}
```

✅ 语法简洁，代码易读。
❌ **性能低**，因为 `String` 是 **不可变对象**，每次拼接都会创建新的 `String` 对象，浪费内存并增加 GC 压力。

### **2. 使用 `String.concat()`**

`String` 类提供了 `concat()` 方法用于拼接字符串，比 `+` 性能稍好。

```
public class StringConcatDemo {
    public static void main(String[] args) {
        String str1 = "Hello";
        String str2 = "World";
        String result = str1.concat(" ").concat(str2).concat("!");
        System.out.println(result); // 输出：Hello World!
    }
}
```

✅ **比 `+` 性能稍好**，因为 `concat()` 方法直接创建新字符串，而不会涉及自动装箱等操作。
❌ **仍然会创建新字符串**，与 `+` 相比提升不大。

### **3. 使用 `StringBuilder`（推荐）**

`StringBuilder` 是 Java 提供的 **可变字符串类**，其 `append()` 方法不会创建新对象，**性能远优于 `+` 和 `concat()`**。

```
public class StringBuilderDemo {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder();
        sb.append("Hello").append(" ").append("World").append("!");
        System.out.println(sb.toString()); // 输出：Hello World!
    }
}
```

✅ **性能高**，`append()` 方法不会创建多个 `String` 对象，而是对同一个 `StringBuilder` 进行修改。
✅ **适合频繁拼接**（如循环中拼接）。
❌ **不是线程安全的**（如需要线程安全，使用 `StringBuffer`）。

### **4. 使用 `StringBuffer`（线程安全版）**

`StringBuffer` 与 `StringBuilder` 类似，但它是**线程安全的**，适用于多线程环境。

```
public class StringBufferDemo {
    public static void main(String[] args) {
        StringBuffer sb = new StringBuffer();
        sb.append("Hello").append(" ").append("World").append("!");
        System.out.println(sb.toString()); // 输出：Hello World!
    }
}
```

✅ **线程安全**，适用于多线程环境。
❌ **性能略低于 `StringBuilder`**，因为 `StringBuffer` 使用了 `synchronized` 进行同步。

### **5. 使用 `String.join()`（适用于多个字符串）**

Java 8 提供了 `String.join()` 方法，适用于多个字符串之间使用相同的分隔符拼接。

```
public class StringJoinDemo {
    public static void main(String[] args) {
        String result = String.join(", ", "Apple", "Banana", "Cherry");
        System.out.println(result); // 输出：Apple, Banana, Cherry
    }
}
```

✅ **适用于拼接多个字符串，尤其是列表数据**。
✅ **可读性好**，可以灵活指定分隔符。
❌ **底层仍然使用 `StringBuilder`**，性能与 `StringBuilder` 接近。

### **6. 使用 `String.format()`（格式化字符串）**

`String.format()` 提供了类似 C 语言 `printf` 的格式化拼接方式。

```
public class StringFormatDemo {
    public static void main(String[] args) {
        String name = "Alice";
        int age = 25;
        String result = String.format("Name: %s, Age: %d", name, age);
        System.out.println(result); // 输出：Name: Alice, Age: 25
    }
}
```

✅ **适用于格式化输出**，可读性好。
❌ **性能比 `StringBuilder` 低**，因为内部仍然使用 `StringBuilder`，但解析格式字符串额外消耗时间。

### **7. 使用 `Collectors.joining()`（适用于集合）**

Java 8 的 `Stream API` 提供了 `Collectors.joining()`，适用于 `List<String>` 之类的集合数据拼接。

```
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class CollectorsJoiningDemo {
    public static void main(String[] args) {
        List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry");
        String result = fruits.stream().collect(Collectors.joining(", "));
        System.out.println(result); // 输出：Apple, Banana, Cherry
    }
}
```

✅ **适用于列表拼接**，代码简洁。
✅ **性能高**，底层使用 `StringBuilder`。
❌ **比 `StringBuilder` 略慢**，因为流（Stream）有额外的开销。

### **性能对比**

| 拼接方式               | 是否创建新对象 | 线程安全 | 适用场景             | 性能     |
| ---------------------- | -------------- | -------- | -------------------- | -------- |
| `+`                    | 是             | 否       | 简单拼接             | **低**   |
| `concat()`             | 是             | 否       | 简单拼接             | **低**   |
| `StringBuilder`        | 否             | 否       | **高频拼接（推荐）** | **高**   |
| `StringBuffer`         | 否             | 是       | **多线程环境**       | **中等** |
| `String.join()`        | 否             | 否       | **批量字符串拼接**   | **高**   |
| `String.format()`      | 是             | 否       | **格式化字符串**     | **低**   |
| `Collectors.joining()` | 否             | 否       | **集合数据拼接**     | **高**   |

### **最佳实践**

1. **少量字符串拼接**（少量操作）：可以使用 `+` 或 `concat()`。
2. **大量字符串拼接**（高频操作，如循环中拼接）：推荐 **`StringBuilder`**，避免创建大量 `String` 对象。
3. **多线程环境拼接**：使用 **`StringBuffer`**（保证线程安全）。
4. **多个字符串拼接**：使用 **`String.join()`**（简单高效）。
5. **集合字符串拼接**：使用 **`Collectors.joining()`**。

如果拼接字符串的操作较多，建议 **优先使用 `StringBuilder`**，因为它的 **性能最优**。

### **总结**

- `+` 和 `concat()` 适用于少量拼接，但性能差。
- `StringBuilder` 是 **拼接字符串的最佳选择**，性能最高，适用于高频拼接。
- `StringBuffer` 适用于多线程场景。
- `String.join()` 和 `Collectors.joining()` 适用于多个字符串或集合拼接。
- `String.format()` 适用于格式化字符串，但性能不如 `StringBuilder`。

## 3. Java集合类有哪些？

Java 集合类主要位于 `java.util` 包下，分为 **Collection** 和 **Map** 两大体系。具体分类如下：

### Collection 体系

#### 1. **List（有序、可重复）**

> **特点**：元素按插入顺序排列，允许重复元素，有索引。

| 实现类       | 线程安全 | 底层数据结构 | 主要特点                                   |
| ------------ | -------- | ------------ | ------------------------------------------ |
| `ArrayList`  | 否       | 动态数组     | 查询快，增删慢，默认容量 10，扩容为 1.5 倍 |
| `LinkedList` | 否       | 双向链表     | 增删快，查询慢，可实现队列和双端队列       |
| `Vector`     | 是       | 动态数组     | 线程安全，效率低，扩容为 2 倍              |
| `Stack`      | 是       | 动态数组     | 继承自 `Vector`，先进后出（LIFO）          |

#### 2. **Set（无序、不可重复）**

> **特点**：不允许重复元素，没有索引。

| 实现类          | 线程安全 | 底层数据结构                            | 主要特点                                          |
| --------------- | -------- | --------------------------------------- | ------------------------------------------------- |
| `HashSet`       | 否       | HashMap（键存元素，值固定为 `PRESENT`） | 无序，允许 `null`，基于哈希表，查找快             |
| `LinkedHashSet` | 否       | LinkedHashMap                           | 维护插入顺序                                      |
| `TreeSet`       | 否       | 红黑树（TreeMap）                       | 自动排序（自然排序或自定义比较器），不允许 `null` |

#### 3. **Queue（队列，FIFO）**

> **特点**：先进先出（FIFO），但 `Deque` 也支持双端操作。

| 实现类                  | 线程安全 | 底层数据结构 | 主要特点                                     |
| ----------------------- | -------- | ------------ | -------------------------------------------- |
| `PriorityQueue`         | 否       | 二叉堆       | 按自然排序或自定义比较器排序                 |
| `LinkedList`            | 否       | 双向链表     | 既可做队列（FIFO），也可做栈（LIFO）         |
| `ArrayDeque`            | 否       | 动态数组     | 线程不安全，性能优于 `Stack` 和 `LinkedList` |
| `ConcurrentLinkedQueue` | 是       | 链表         | 线程安全的无界队列                           |

#### 4. **Deque（双端队列）**

> **特点**：可从两端插入或删除。

| 实现类       | 线程安全 | 主要特点                       |
| ------------ | -------- | ------------------------------ |
| `ArrayDeque` | 否       | 基于动态数组，支持队列和栈操作 |
| `LinkedList` | 否       | 基于双向链表，支持队列和栈操作 |

### **Map 体系**

> **特点**：存储键值对 (`key-value`)，`key` 不能重复。

| 实现类              | 线程安全 | 底层数据结构      | 主要特点                                                   |
| ------------------- | -------- | ----------------- | ---------------------------------------------------------- |
| `HashMap`           | 否       | 哈希表            | 允许 `null` 键，`key` 无序，默认容量 16，负载因子 0.75     |
| `LinkedHashMap`     | 否       | 哈希表 + 双向链表 | 维护插入顺序                                               |
| `TreeMap`           | 否       | 红黑树            | `key` 自动排序（自然排序或自定义比较器），不允许 `null` 键 |
| `Hashtable`         | 是       | 哈希表            | 线程安全，不允许 `null` 键和值                             |
| `ConcurrentHashMap` | 是       | 分段锁            | 高并发环境下效率高                                         |

### 总结

#### **1. List 适用于：**

- 需要 **有序** 且 **允许重复** 的数据，如：用户列表、历史记录等。
- 需要 **频繁访问** 某个索引位置的元素时，推荐 `ArrayList`。
- 需要 **频繁插入和删除** 时，推荐 `LinkedList`。

#### **2. Set 适用于：**

- 需要 **去重** 时，推荐 `HashSet`。
- 需要 **排序** 时，推荐 `TreeSet`。
- 需要 **维持插入顺序** 时，推荐 `LinkedHashSet`。

#### **3. Queue 适用于：**

- 先进先出（FIFO）操作，如任务队列、消息队列。
- **普通队列** 用 `LinkedList` 或 `ArrayDeque`。
- **优先级队列** 用 `PriorityQueue`。

#### **4. Map 适用于：**

- 需要 **键值映射**，推荐 `HashMap`（高效但无序）。
- 需要 **保证插入顺序**，推荐 `LinkedHashMap`。
- 需要 **排序**，推荐 `TreeMap`（基于 `key` 排序）。
- 需要 **线程安全**，推荐 `ConcurrentHashMap`。

## 4. ArrayList和LinkedList的区别？

`ArrayList` 和 `LinkedList` 都是 Java 中 `List` 接口的实现类，主要区别如下：

### **1. 底层数据结构**

- **`ArrayList`**：基于 **动态数组** 实现，默认容量为 `10`，当容量不足时，会扩容 1.5 倍（`newCapacity = oldCapacity + (oldCapacity >> 1)`）。
- **`LinkedList`**：基于 **双向链表** 实现，每个节点包含 `prev`（前指针）、`next`（后指针）、`data`（数据）。

### **2. 插入和删除**

- **`ArrayList`**：
  - 插入（`add(index, element)`）和删除（`remove(index)`）时，**需要移动大量元素**，导致 **性能较低**。
  - `add(E e)`（尾部追加）性能较好，因为只需添加到数组末尾，可能触发扩容。
  - 复杂度：
    - **尾部插入**：`O(1)`（均摊）
    - **中间插入/删除**：`O(n)`
- **`LinkedList`**：
  - 插入（`add(index, element)`）和删除（`remove(index)`）时，只需调整节点的 `prev` 和 `next` 指针，**不需要移动元素**，**性能较高**。
  - 但需要 **遍历链表** 以找到目标位置（最坏 `O(n)`）。
  - 复杂度：
    - **任意位置插入/删除**：`O(1)`（已找到目标节点）
    - **查找元素**：`O(n)`

📌 **结论**：
 ✅ **频繁插入/删除，推荐 `LinkedList`**（不需要移动大量数据）。
 ✅ **插入/删除主要在尾部，推荐 `ArrayList`**（`ArrayList` 追加数据更快）。

### **3. 查询性能**

- **`ArrayList`**：
  - **索引查询（`get(index)`）速度快**，底层是 **数组**，可以直接通过索引访问元素，时间复杂度 **O(1)**。
  - **遍历速度快**，因为数组数据存储是 **连续的**，CPU **缓存命中率高**。
- **`LinkedList`**：
  - **索引查询（`get(index)`）速度慢**，需要从头（或尾）开始遍历，时间复杂度 **O(n)**。
  - **遍历速度较慢**，因为链表节点存储在**不同的内存位置**，CPU **缓存命中率低**。

📌 **结论**：
 ✅ **需要频繁查询，推荐 `ArrayList`**。
 ❌ **查询性能要求高的场景，不推荐 `LinkedList`**。

### **4. 内存占用**

- **`ArrayList`**：
  - 只存储 **数据本身**，占用 **较少内存**。
  - 可能存在 **预留空间**，导致部分内存浪费。
- **`LinkedList`**：
  - 每个节点除了存 `data` 之外，还存 `prev` 和 `next` 指针，因此 **内存开销大**。
  - **额外的对象头**（链表的节点是对象）。

📌 **结论**：
 ✅ **内存紧张的情况下，`ArrayList` 更优**。
 ❌ **`LinkedList` 由于额外的指针，内存开销较大**。

### **5. 扩容机制**

- **`ArrayList`**：
  - 默认容量 **10**，当容量不足时，**扩容 1.5 倍**（`newCapacity = oldCapacity + (oldCapacity >> 1)`）。
  - **扩容需要拷贝原数组**，性能较低。
- **`LinkedList`**：
  - **不需要扩容**，因为链表按需分配，每次新增元素只需创建新节点并调整指针。

📌 **结论**：
 ✅ **`ArrayList` 扩容有性能损耗，而 `LinkedList` 无需扩容**。

### **6. 线程安全性**

- `ArrayList`和 `LinkedList` 都是非线程安全的，多线程环境需要手动加锁：

  - 可以使用 `Collections.synchronizedList()`进行同步：

   ```java
  List<String> list = Collections.synchronizedList(new ArrayList<>());
   ```

  - 也可以使用 `CopyOnWriteArrayList`，适用于读多写少的场景。

📌 **结论**：
 ✅ **多线程环境建议使用 `CopyOnWriteArrayList` 代替 `ArrayList`**。
 ✅ **如果必须用 `LinkedList`，需要手动同步**。

### **7. 适用场景总结**

| 操作类型                  | `ArrayList`                     | `LinkedList`                 |
| ------------------------- | ------------------------------- | ---------------------------- |
| **查找（get）**           | ✅ **快 `O(1)`**（数组索引访问） | ❌ **慢 `O(n)`**（需要遍历）  |
| **插入（add(index, E)）** | ❌ **慢 `O(n)`**（需要移动元素） | ✅ **快 `O(1)`**（指针调整）  |
| **删除（remove(index)）** | ❌ **慢 `O(n)`**（需要移动元素） | ✅ **快 `O(1)`**（指针调整）  |
| **尾部追加（add(E)）**    | ✅ **快 `O(1)`**（均摊）         | ✅ **快 `O(1)`**              |
| **遍历**                  | ✅ **快（CPU 缓存友好）**        | ❌ **慢（内存不连续）**       |
| **内存占用**              | ✅ **较小**（仅存数据）          | ❌ **较大**（需额外存储指针） |
| **扩容**                  | ❌ **需要扩容（拷贝数据）**      | ✅ **无需扩容**               |
| **线程安全**              | ❌ **非线程安全**                | ❌ **非线程安全**             |

### **8. 什么时候用 `ArrayList`？**

✅ **查询操作频繁**（比如 `get(index)`）
✅ **存储的数据量较小**
✅ **插入/删除操作主要发生在尾部**
✅ **内存占用要尽量少**
📌 适用于 **大多数场景**，比如：

- 需要**快速访问**元素的场景（如缓存、排行榜）。
- 数据**量大**，但**不频繁插入/删除**的场景（如 `List<String>`）。

### **9. 什么时候用 `LinkedList`？**

✅ **频繁插入/删除数据**（尤其是**中间位置**）
✅ **插入/删除远比查询更重要**
✅ **不关心额外的内存开销**
📌 适用于：

- **队列（Queue）、双端队列（Deque）**（比如 `LinkedList` 可实现 `Deque`）。
- **数据量小，但插入/删除多**（如 `LRU 缓存`）。

### **10. 结论**

- **如果没有特殊需求，`ArrayList` 是首选**（查询快，内存占用低）。
- **`LinkedList` 适用于插入/删除较多的情况**（但查询性能差）。
- **不要滥用 `LinkedList`**，**大部分情况下 `ArrayList` 更高效**！

------

### 🚀 **推荐方案**

1. **大多数情况**：用 `ArrayList`
2. **频繁增删（非尾部）**：用 `LinkedList`
3. **线程安全**：用 `CopyOnWriteArrayList`
4. **双端队列/队列**：用 `LinkedList`（或者 `ArrayDeque`，比 `LinkedList` 更高效）

## 5. Spring IOC和AOP

### 1. IOC

**IOC（Inversion of Control，控制反转）** 是 Spring 框架的核心思想之一，主要作用是**将对象的创建和管理权交给 Spring 容器，而不是由开发者手动控制**。

在传统的 Java 编程中，我们通常使用 `new` 关键字来创建对象，而 IOC 通过**依赖注入（DI，Dependency Injection）**的方式，把对象的创建交由 Spring 容器来管理，使代码更加解耦。

#### **IOC核心概念：**

**Bean**：Spring 容器管理的对象。

**BeanFactory**：Spring 的底层 IOC 容器，提供 Bean 的基本管理功能（懒加载）。

**ApplicationContext**：高级 IOC 容器，提供更丰富的功能（支持国际化、事件传播等，默认立即初始化所有 Bean）。

**依赖注入（DI）**：Spring 通过配置文件或注解，将所需的 Bean 注入到对象中。

#### 依赖注入（DI）方式

Spring 提供了三种依赖注入方式：

##### **1️⃣ 构造器注入**

通过构造方法注入依赖对象：

```
@Component
public class UserService {
    private final UserRepository userRepository;

    @Autowired
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

##### **2️⃣ Setter 方法注入**

通过 `setXxx` 方法注入：

```
@Component
public class UserService {
    private UserRepository userRepository;

    @Autowired
    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

##### **3️⃣ 直接字段注入（@Autowired）**

```
@Component
public class UserService {
    @Autowired
    private UserRepository userRepository;
}
```

⚠️ **推荐使用构造器注入**，避免 **循环依赖** 和 **无法单元测试** 的问题。

#### IOC 的优点

✅ **解耦**：降低模块之间的依赖，使代码更加可维护。
✅ **可测试**：方便使用 mock 对象进行单元测试。
✅ **易扩展**：Bean 可以通过配置文件或注解动态修改。
✅ **统一管理**：所有 Bean 由容器管理，减少对象管理的复杂度。

### 2. AOP

**AOP（Aspect-Oriented Programming，面向切面编程）** 是 Spring 的另一个核心特性，主要用于**在不修改源代码的情况下，为方法增加额外的功能**（如日志、事务管理、权限控制等）。

AOP 采用 **横切关注点**（Cross-cutting Concerns）思想，把多个类中**重复的代码提取**出来，独立封装成一个**切面（Aspect）**，然后在运行时**动态织入**到目标对象的方法中。

