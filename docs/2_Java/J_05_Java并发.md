---
sidebar_position: 5
---

# Java并发

## 一、线程

### 1. 什么是进程和线程？

#### 进程

当一个程序被运行，从磁盘加载这个程序的代码到内存，这时就开启了一个进程。

进程是程序的一次执行过程，是系统运行程序的基本单位，因此进程是动态的。系统运行一个程序即是一个进程从创建，运行到消亡的过程。

#### 线程

线程与进程相似，但线程是一个比进程更小的执行单位。一个线程就是一个指令流，将指令流中的一条条指令以一定的顺序交给CPU执行。

一个进程在其执行的过程中可以产生多个线程。

> 与进程不同的是同类的多个线程共享进程的**堆**和**方法区**资源，但每个线程有自己的**程序计数器**、**虚拟机栈**和**本地方法栈**，所以系统在产生一个线程，或是在各个线程之间做切换工作时，负担要比进程小得多，也正因为如此，线程也被称为轻量级进程。

#### 区别

- 进程是正在运行程序的实例，进程中包含了线程，每个线程执行不同的任务；
- 不同的进程使用不同的内存空间，在当前进程下的所有线程可以共享内存空间；
- 线程更轻量，线程的上下文切换（从一个线程切换到另一个线程）成本比进程低。

### 2. 并发和并行的区别？

- 单核CPU
  - 线程实际还是串行执行的；
  - 操作系统的任务调度器将CPU的时间片分给不同的线程使用，由于CPU线程间的切换非常快，人类感觉是同时运行的；
  - 宏观上并行，微观上串行。
  - 将线程轮流使用CPU的做法称为并发。
- 多核CPU
  - 线程真正做到并行，同一时间做多件事。

### 3. 如何创建线程？

一般来说，创建线程有很多种方式，例如

- 继承`Thread`类
- 实现`Runnable`接口
- 实现`Callable`接口
- 使用线程池

实现 Runnable 和 Callable 接口的类只能当做一个可以在线程中运行的任务，不是真正意义上的线程，因此最后还需要通过`new Thread().start()`来调用。可以理解为任务是通过线程驱动从而执行的。

#### 实现 Runnable 接口

需要实现接口中的 run() 方法。

```java
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        // ...
    }
}
```

使用 Runnable 实例再创建一个 Thread 实例，然后调用 Thread 实例的 start() 方法来启动线程。

```java
public static void main(String[] args) {
    MyRunnable instance = new MyRunnable();
    Thread thread = new Thread(instance);
    thread.start();
}
```

#### 实现 Callable 接口

与 Runnable 相比，Callable 可以有返回值，返回值通过 FutureTask 进行封装。

```java
public class MyCallable implements Callable<Integer> {
    public Integer call() {
        return 123;
    }
}
public static void main(String[] args) throws ExecutionException, InterruptedException {
    MyCallable mc = new MyCallable();
    FutureTask<Integer> ft = new FutureTask<>(mc);
    Thread thread = new Thread(ft);
    thread.start();
    System.out.println(ft.get());
}
```

#### 继承 Thread 类

同样也是需要实现 run() 方法，因为 Thread 类也实现了 Runable 接口。

当调用 start() 方法启动一个线程时，虚拟机会将该线程放入就绪队列中等待被调度，当一个线程被调度时会执行该线程的 run() 方法。

```java
public class MyThread extends Thread {
    public void run() {
        // ...
    }
}
public static void main(String[] args) {
    MyThread mt = new MyThread();
    mt.start();
}
```

#### 实现接口 VS 继承 Thread

实现接口会更好一些，因为：

- Java 不支持多重继承，因此继承了 Thread 类就无法继承其它类，但是可以实现多个接口；
- 类可能只要求可执行就行，继承整个 Thread 类开销过大。

#### runable和callable有什么区别

- Runable接口run方法没有返回值
- Callable接口call方法有返回值，是个泛型，和Future、FutureTask配合可以用来获取异步执行的结果
- Callable接口的call方法允许抛异常；而Runable接口的run方法的异常只能在内部消化，不能继续上抛

####  在启动线程的时候，可以使用Run方法吗？run()和start()的区别？

run只是一个普通的方法调用，并不会启动新线程。

- start()：用来启动线程，通过该线程调用run方法执行run方法中所定义的逻辑代码。start方法只能被调用一次。
- run()：封装了要被线程执行的代码，可以被调用多次。

### 4. 线程的生命周期和状态？

Java 线程在运行的生命周期中的指定时刻只可能处于下面 6 种不同状态的其中一个状态：

- NEW: 新建状态，线程被创建出来但没有被调用 `start()` 。
- RUNNABLE: 可运行状态，线程被调用了 `start()`等待运行的状态。
- BLOCKED：阻塞状态，需要等待锁释放。
- WAITING：等待状态，表示该线程需要等待其他线程做出一些特定动作（通知或中断）。
- TIMED_WAITING：超时等待状态，可以在指定的时间后自行返回而不是像 WAITING 那样一直等待。
- TERMINATED：终止状态，表示该线程已经运行完毕。

线程在生命周期中并不是固定处于某一个状态而是随着代码的执行在不同状态之间切换。

### 5. 线程按顺序执行

#### 新建3个线程T1,T2,T3，如何保证他们按顺序执行？

可以使用线程的Join方法解决

t.join()阻塞调用此方法的线程进入timed_waiting，直到线程t执行完成后，此线程再继续执行

#### notify()和notifyAll()有什么区别？

- notifyAll：唤醒所有wait的线程
- notify：只随机唤醒一个wait线程



### 6. 什么是线程的上下文切换？

线程的上下文切换（**Context Switch**）是指 **CPU 从一个线程切换到另一个线程的过程**，这个过程中，系统会保存当前线程的运行状态，并恢复另一个线程的状态，从而实现多线程的并发执行。

#### 什么是上下文（Context）？

在操作系统中，一个线程的“上下文”包括：

- **程序计数器（PC）**：记录当前执行到哪一行指令。
- **寄存器内容**：各类通用寄存器、栈指针（SP）、基址指针（BP）等。
- **堆栈信息**：线程调用栈、局部变量、返回地址等。
- **线程状态**：比如运行中、等待中、阻塞中等。

**简而言之**：上下文 = 线程执行的全部信息。

#### 上下文切换的过程

当 CPU 决定把执行权从当前线程切换给另一个线程时，操作系统会做以下事情：

1. **保存当前线程的上下文**（寄存器、程序计数器等）。
2. **将该线程状态设置为就绪或等待**。
3. **从要切换到的线程中恢复其上下文信息**。
4. **设置 CPU 寄存器等为新线程的数据，开始运行该线程**。

这个过程称为“上下文切换”。

#### 为什么会发生线程上下文切换？

主要有以下几种情况：

| 场景                   | 举例说明                                                 |
| ---------------------- | -------------------------------------------------------- |
| **时间片耗尽**         | 一个线程运行时间到，操作系统将其挂起，切换到另一个线程。 |
| **线程阻塞或等待资源** | 等待 I/O、锁、网络、sleep、wait 等操作时。               |
| **高优先级线程抢占**   | 高优先级线程进入就绪态，系统切换执行它。                 |
| **系统调用或中断响应** | 比如 I/O 中断发生，系统中断当前线程处理外设任务。        |

#### 线程上下文切换的代价

虽然上下文切换让多线程并发成为可能，但它并非没有代价：

- **CPU 保存/恢复状态需要时间**。
- **涉及用户态 ↔ 内核态切换**（比较慢）。
- **频繁切换会导致缓存失效**（CPU cache miss）。
- **增加系统开销，降低整体性能**。

> 因此，在高并发编程中，**减少不必要的线程切换** 是性能优化的重要方向。

#### 实际例子说明

假设一个 Java Web 服务处理大量请求时：

- 线程池中的线程 A 正在处理请求1；
- 线程 B 等待数据库查询结果，进入阻塞；
- 操作系统调度器发现线程 A 时间片到了或线程 C 优先级高；
- 保存线程 A 的状态 → 切换到线程 C；
- 线程上下文切换发生。

### 7. Thread#sleep() 方法和 Object#wait() 方法对比

**共同点**：两者都可以暂停线程的执行。

**区别**：

- 锁特性不同：
  - wait方法的调用必须先获取wait对象的锁，而sleep则无此限制
  - wait方法执行后会释放，允许其他线程获取该对象锁
  - `sleep()` 方法如果在synchronized代码块执行，并不会释放对象锁
- 醒来时机不同：
  - `wait()` 方法被调用后，线程不会自动苏醒，需要别的线程调用同一个对象上的 `notify()`或者 `notifyAll()` 方法。
  - `sleep()`方法执行完成后，线程会自动苏醒，或者也可以使用 `wait(long timeout)` 超时后线程会自动苏醒。
- 方法归属不同：
  - `sleep()` 是 `Thread` 类的静态本地方法
  - `wait()` 则是 `Object` 类的本地方法


### 8. 为什么 wait() 方法不定义在 Thread 中？

`wait()` 是让获得对象锁的线程实现等待，会自动释放当前线程占有的对象锁。每个对象（`Object`）都拥有对象锁，既然要释放当前线程占有的对象锁并让其进入 WAITING 状态，自然是要操作对应的对象（`Object`）而非当前的线程（`Thread`）。

类似的问题：**为什么 `sleep()` 方法定义在 `Thread` 中？**

因为 `sleep()` 是让当前线程暂停执行，不涉及到对象类，也不需要获得对象锁。

### 9. 可以直接调用 Thread 类的 run 方法吗？

new 一个 `Thread`，线程进入了新建状态。调用 `start()`方法，会启动一个线程并使线程进入了就绪状态，当分配到时间片后就可以开始运行了。 `start()` 会执行线程的相应准备工作，然后自动执行 `run()` 方法的内容，这是真正的多线程工作。 但是，直接执行 `run()` 方法，会把 `run()` 方法当成一个 main 线程下的普通方法去执行，并不会在某个线程中执行它，所以这并不是多线程工作。

**总结：调用 `start()` 方法方可启动线程并使线程进入就绪状态，直接执行 `run()` 方法的话不会以多线程的方式执行。**

### 10. 如何停止一个正在运行的线程？

有三种方式可以停止线程

- 使用退出标志，使线程正常退出，也就是当run方法完成后线程终止
- 使用stop方法强行终止（不推荐，已作废）
- 使用interrupt方法中断线程
  - 打断阻塞的线程（sleep，wait，join），线程会抛出InterruptedException异常
  - 打断正常的线程，可以根据打断状态来标记是否退出线程

### 11. 基础线程机制

#### Executor

Executor 管理多个异步任务的执行，而无需程序员显式地管理线程的生命周期。这里的异步是指多个任务的执行互不干扰，不需要进行同步操作。

主要有三种 Executor：

- CachedThreadPool：一个任务创建一个线程；
- FixedThreadPool：所有任务只能使用固定大小的线程；
- SingleThreadExecutor：相当于大小为 1 的 FixedThreadPool。

```java
public static void main(String[] args) {
    ExecutorService executorService = Executors.newCachedThreadPool();
    for (int i = 0; i < 5; i++) {
        executorService.execute(new MyRunnable());
    }
    executorService.shutdown();
}
```

#### Daemon

守护线程是程序运行时在后台提供服务的线程，不属于程序中不可或缺的部分。

当所有非守护线程结束时，程序也就终止，同时会杀死所有守护线程。

main() 属于非守护线程。

在线程启动之前使用 setDaemon() 方法可以将一个线程设置为守护线程。

```java
public static void main(String[] args) {
    Thread thread = new Thread(new MyRunnable());
    thread.setDaemon(true);
}
```

#### sleep()

Thread.sleep(millisec) 方法会休眠当前正在执行的线程，millisec 单位为毫秒。

sleep() 可能会抛出 InterruptedException，因为异常不能跨线程传播回 main() 中，因此必须在本地进行处理。线程中抛出的其它异常也同样需要在本地进行处理。

```java
public void run() {
    try {
        Thread.sleep(3000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```

#### yield()

对静态方法 Thread.yield() 的调用声明了当前线程已经完成了生命周期中最重要的部分，可以切换给其它线程来执行。该方法只是对线程调度器的一个建议，而且也只是建议具有相同优先级的其它线程可以运行。

```java
public void run() {
    Thread.yield();
}
```

## 二、线程安全

### 1. synchronized关键字的底层原理

synchronized**对象锁**采用互斥的方式让同一时刻至多只有一个线程能持有**对象锁**，其他线程再想获取这个**对象锁**时就会阻塞。

synchronized底层是Monitor实现的。Monitor被翻译为监视器，由JVM提供，C++实现。

Monitor的结构包括WaitSet，EntryList，Owner。

- Owner：存储当前获取锁的线程，只有一个线程可以获取。
- EntryList：关联没有抢到锁的线程，处于Blocked状态的线程。
- WaitSet：关联调用了wait方法的线程，处于Waiting状态的线程。

### 2. 你谈谈JMM

- JMM是Java内存模型，定义了共享内存中多线程程序读写操作的行为规范，通过这些规则来规范对内存的读写操作从而保证指令的正确性。
- JMM把内存分为两块，一块是私有线程的工作区域（工作内存），一块是所有线程的共享区域（主内存）
- 线程跟线程之间相互隔离，线程跟线程交互需要通过主内存

## 3. 什么是AQS



## 4. ReentrantLock了解吗



## 5. synchronized和Lock有什么区别



## 6. 死锁产生的条件是什么



## 7. 如何进行死锁诊断



## 8. 请谈谈你对volatile的理解



## 9. 聊一下ConcurrentHashMap



## 10. 导致并发程序出现问题的根本原因是什么



## 三、线程池



## 四、使用场景

