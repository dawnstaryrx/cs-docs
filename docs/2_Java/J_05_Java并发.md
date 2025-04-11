---
sidebar_position: 5
---

# Java并发

## 一、线程

### 1. 什么是进程和线程？

#### 进程

当一个程序被运行，从磁盘加载这个程序的代码到内存，这时就开启了一个进程。

进程是程序的一次执行过程，是系统运行程序的基本单位，因此进程是动态的。系统运行一个程序即是一个进程从创建，运行到消亡的过程。

在 Java 中，当我们启动 main 函数时其实就是启动了一个 JVM 的进程，而 main 函数所在的线程就是这个进程中的一个线程，也称主线程。

#### 线程

线程与进程相似，但线程是一个比进程更小的执行单位。一个线程就是一个指令流，将指令流中的一条条指令以一定的顺序交给CPU执行。

一个进程在其执行的过程中可以产生多个线程。

与进程不同的是同类的多个线程共享进程的**堆**和**方法区**资源，但每个线程有自己的**程序计数器**、**虚拟机栈**和**本地方法栈**，所以系统在产生一个线程，或是在各个线程之间做切换工作时，负担要比进程小得多，也正因为如此，线程也被称为轻量级进程。

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

### 3. 线程和进程的关系、区别及优缺点？

线程是进程划分成的更小的运行单位。

线程和进程最大的不同在于基本上各进程是独立的，而各线程则不一定，因为同一进程中的线程极有可能会相互影响。

线程执行开销小，但不利于资源的管理和保护；而进程正相反。

### 4. 如何创建线程？

一般来说，创建线程有很多种方式，例如

- 继承`Thread`类
- 实现`Runnable`接口
- 实现`Callable`接口
- 使用线程池
- 使用`CompletableFuture`类等等。

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

- start()：用来启动线程，通过该线程调用run方法执行run方法中所定义的逻辑代码。start方法只能被调用一次。
- run()：封装了要被线程执行的代码，可以被调用多次。

### 5. 线程的生命周期和状态？

Java 线程在运行的生命周期中的指定时刻只可能处于下面 6 种不同状态的其中一个状态：

- NEW: 新建状态，线程被创建出来但没有被调用 `start()` 。
- RUNNABLE: 可运行状态，线程被调用了 `start()`等待运行的状态。
- BLOCKED：阻塞状态，需要等待锁释放。
- WAITING：等待状态，表示该线程需要等待其他线程做出一些特定动作（通知或中断）。
- TIMED_WAITING：超时等待状态，可以在指定的时间后自行返回而不是像 WAITING 那样一直等待。
- TERMINATED：终止状态，表示该线程已经运行完毕。

线程在生命周期中并不是固定处于某一个状态而是随着代码的执行在不同状态之间切换。

### 6. 线程按顺序执行

#### 新建3个线程T1,T2,T3，如何保证他们按顺序执行？

可以使用线程的Join方法解决

t.join()阻塞调用此方法的线程进入timed_waiting，直到线程t执行完成后，此线程再继续执行

#### notify()和notifyAll()有什么区别？

- notifyAll：唤醒所有wait的线程
- notify：只随机唤醒一个wait线程



### 7. 什么是线程的上下文切换？

线程在执行过程中会有自己的运行条件和状态（也称上下文），比如上文所说到过的程序计数器，栈信息等。当出现如下情况的时候，线程会从占用 CPU 状态中退出。

- 主动让出 CPU，比如调用了 `sleep()`, `wait()` 等。
- 时间片用完，因为操作系统要防止一个线程或者进程长时间占用 CPU 导致其他线程或者进程饿死。
- 调用了阻塞类型的系统中断，比如请求 IO，线程被阻塞。
- 被终止或结束运行

这其中前三种都会发生线程切换，线程切换意味着需要保存当前线程的上下文，留待线程下次占用 CPU 的时候恢复现场。并加载下一个将要占用 CPU 的线程上下文。这就是所谓的 **上下文切换**。

上下文切换是现代操作系统的基本功能，因其每次需要保存信息恢复信息，这将会占用 CPU，内存等系统资源进行处理，也就意味着效率会有一定损耗，如果频繁切换就会造成整体效率低下。

### 8. Thread#sleep() 方法和 Object#wait() 方法对比

**共同点**：两者都可以暂停线程的执行。

**区别**：

- 锁特性不同：
  - wait方法的调用必须先获取wait对象的锁，而sleep则无此限制
  - wait方法执行后会释放，允许其他线程获取该对象锁
  - `sleep()` 方法如果在synchronized代码块执行，并不会释放对象锁

- `wait()` 通常被用于线程间交互/通信，`sleep()`通常被用于暂停执行。
- 醒来时机不同：
  - `wait()` 方法被调用后，线程不会自动苏醒，需要别的线程调用同一个对象上的 `notify()`或者 `notifyAll()` 方法。
  - `sleep()`方法执行完成后，线程会自动苏醒，或者也可以使用 `wait(long timeout)` 超时后线程会自动苏醒。

- 方法归属不同：
  - `sleep()` 是 `Thread` 类的静态本地方法
  - `wait()` 则是 `Object` 类的本地方法


### 9. 为什么 wait() 方法不定义在 Thread 中？

`wait()` 是让获得对象锁的线程实现等待，会自动释放当前线程占有的对象锁。每个对象（`Object`）都拥有对象锁，既然要释放当前线程占有的对象锁并让其进入 WAITING 状态，自然是要操作对应的对象（`Object`）而非当前的线程（`Thread`）。

类似的问题：**为什么 `sleep()` 方法定义在 `Thread` 中？**

因为 `sleep()` 是让当前线程暂停执行，不涉及到对象类，也不需要获得对象锁。

### 10. 可以直接调用 Thread 类的 run 方法吗？

这是另一个非常经典的 Java 多线程面试问题，而且在面试中会经常被问到。很简单，但是很多人都会答不上来！

new 一个 `Thread`，线程进入了新建状态。调用 `start()`方法，会启动一个线程并使线程进入了就绪状态，当分配到时间片后就可以开始运行了。 `start()` 会执行线程的相应准备工作，然后自动执行 `run()` 方法的内容，这是真正的多线程工作。 但是，直接执行 `run()` 方法，会把 `run()` 方法当成一个 main 线程下的普通方法去执行，并不会在某个线程中执行它，所以这并不是多线程工作。

**总结：调用 `start()` 方法方可启动线程并使线程进入就绪状态，直接执行 `run()` 方法的话不会以多线程的方式执行。**

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

### 12. 如何停止一个正在运行的线程？

有三种方式可以停止线程

- 使用退出标志，使线程正常退出，也就是当run方法完成后线程终止
- 使用stop方法强行终止（不推荐，已作废）
- 使用interrupt方法中断线程
  - 打断阻塞的线程（sleep，wait，join），线程会抛出InterruptedException异常
  - 打断正常的线程，可以根据打断状态来标记是否退出线程

## 二、多线程





## 三、死锁



