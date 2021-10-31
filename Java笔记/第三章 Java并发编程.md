# 第三章 Java并发编程

## 3.1 Java线程的创建方式

4种创建方式：继承Thread类、实现Runnable接口、通过ExecutorService和Callable\<Class>实现有返回值的线程、基于线程池创建线程。

### 3.1.1 继承Thread类

通过创建一个类继承Thread类，并实现run()方法。

在实例化该线程对象时，会调用start()方法启动线程。start方法是一个native方法，通过操作系统上启动一个新线程，并最终执行run方法来启动一个线程。run方法内的代码是线程类的具体实现逻辑。

```java
public class NewThread extends Thread {
    public void run() {
        System.out.println("create a new thread by extends Thread");
    }
}
NewThread newThread = new NewThread();
newThread.start();
```

**run()与start()方法的不同**

//TODO

### 3.1.2 实现Runnable接口

如果子类已经继承了一个类，就不能再继承Thread类了，此时可以通过实现Runnable接口创建线程。通过Runnable接口创建ChildrenClassThread线程，实例化名称为childrenThread的线程实例，创建Thread类的实例并传入childrenThread线程实例，调用线程的start方法启动线程。

```java
public class ChildrenClassThread extends SuperClass implements Runnable {
    public void run() {
        System.out.println("create a new thread by implements Punnable");
    }
    ChildrenClassThread childrenThread = new ChildrenClassThread();
    Thread thread = new Tread(childrenClassThread);
    thread.start();
}
```

在传入一个实现了Runnable的线程实例target给Thread后，Thread的run方法在执行时就会调用target.run()方法并执行该线程具体的实现逻辑。源码：

```java
@Override
public void run() {
    if (target != null) {
        target.run();
    }
}
```

### 3.1.3 通过ExecutorService和Callable\<Class>实现有返回值的线程

当需要在主线程中开启多个子线程并发执行一个任务，然后收集各个线程执行返回的结果并将最终结构汇总起来，可以用Callable接口。

创建一个Callable接口，在call()方法中实现具体的运算逻辑并返回计算结果。具体的调用过程：

- 创建一个线程池、一个用于接收返回结果的Future List及Callable线程实例；
- 使用线程池提交任务并将线程执行之后的结果保存在Future中；
- 在线程执行结束后遍历Future List中的Future对象，在该对象上调用get方法就可以获取Callable线程任务返回的数据汇总结果。

```java
public static class MyCallable implements Callable<String> {
    private String name;
    public MyCallable(String name) {
        this.name = name;
    }

    @Override
    public String call() throws Exception {
        return name;
    }
}
// 创建一个固定大小为5的线程池
ExecutorService pool = Executors.newFixedThreadPool(5);
// 创建多个有返回值的任务列表list
List<Future> list = new ArrayList<>();
for (int i = 0; i < 5; i++) {
    // 创建一个有返回值的线程实例
    Callable c = new MyCallable(i + " ");
    // 提交线程，获取Future对象并将其保存到Future List中
    Future future = pool.submit(c);
    System.out.println("submit a callable thread:" + i);
    list.add(future);
}
// 关闭线程池，等待线程执行结束
pool.shutdown();
// 遍历所有线程的运行结果
for (Future f : list) {
    System.out.println("get the result from callable thread:" + f.get().toString());
}
```



### 3.1.4 基于线程池

使用缓存策略并使用线程池来创建线程，可以减少每次创建线程及运行结束后销毁造成的系统资源消耗。

```java
ExecutorService threadPool = Executors.newFixedThreadPool(10);
for (int i = 0; i < 10; i++) {
    // 提交多个线程任务并执行
    threadPool.execute(new Runnable() {
        @Override
        public void run() {
            System.out.println(Thread.currentThread().getName() + " is running");
        }
    });
}
```

***在阿里开发手册中不允许使用Executors创建线程池，而应使用自己定义的ThreadPoolExecutor***

```java
private static ExecutorService executor = new ThreadPoolExecutor(10, 10, 60L, TimeUnit.SECONDS, new ArrayBlockingQueue(10));
```

这种情况下，一旦提交的线程数超过当前可用线程数时，就会抛出`java.util.concurrent.RejectedExecutionException`，这是因为当前线程池使用的队列是有边界队列，队列已经满了便无法继续处理新的请求。但是异常（Exception）总比发生错误（Error）要好。

除了自定义ThreadPoolExecutor外，还可以使用开源类库，如apache和guava等。

阿里工程师推荐使用guava提供的ThreadFactoryBuilder来创建线程池。

```java
public class ExecutorsDemo {
    
    private static ThreadFactory namedThreadFactory = new ThreadFactoryBuilder().setNameFormat("demo-pool-%d").build();
    
    private static ExecutorService pool = new ThreadPoolExecutor(5, 200, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>(1024), namedThreadFactory, new ThreadPoolExecutor.AbortPolicy());
    
    public static void main(String[] args) {
        
        for (int i = 0; i < Integer.MAX_VALUE; i++) {
            pool.execute(new SubThread());
        }
    }
}
```

通过上述方式创建线程时，不仅可用避免OOM的问题，还可以自定义线程名称，更加方便出错的时候溯源。

## 3.2 线程池工作原理

​	JVM先根据用户的参数创建一定数量的可运行的线程任务，并将其放入队列中，在线程创建后启动这些任务，如果正在运行的线程数量超过最大线程数量（用户设置的线程池大小），则超出数量的线程排队等候，在有任务执行完毕后，线程池调度器会发现有可用的线程，进而再次从队列中取出任务并执行。

​	线程池的主要作用是**线程复用、线程资源管理、控制操作系统的最大并发数**，以保证系统高效（通过线程资源复用实现）且安全（通过控制最大线程并发数实现）地运行。

### 3.2.1 线程池复用

​	在Java中，每个Thread方法都有一个start()方法。在调用start()方法启动线程时，Java虚拟机会调用该类的run()方法。

​	在Thread类的run()方法中实际上调用了Runnable对象的run()方法，因此可以继承Thread类，在start方法中不断循环调用传递进来的Runnable对象，程序就会不断执行run()方法中的代码。

​	可以将在循环中不断获取的Runnable对象存储在Queue中，当前线程在获取下一个Runnable对象之前可以是阻塞的，这样既可以能有效控制正在执行的线程个数，也能保证系统中正在等待执行的其他线程有序执行。

### 3.2.2 线程池的核心组件和核心类

- 线程池管理器：用于创建并管理线程池。
- 工作线程：线程池中执行具体任务的线程。
- 任务接口：用于定义工作线程的调度和执行策略，只有线程实现了接口，线程中的任务才能够被线程池调度。
- 任务队列：存放待处理的任务，新的任务将会不断地被加入队列中，执行完成的任务将被从队列中移除。

Java中的线程池通过Executor框架实现的，在该框架中用到了Executor、Executors、ExecutorService、ThreadPoolExecutor、Callable、Future、FutureTask几个核心类

ThreadPoolExecutor具体参数

| 序号 | 参数            | 说明                                                         |
| ---- | --------------- | ------------------------------------------------------------ |
| 1    | corePoolSize    | 线程池中核心线程池数量                                       |
| 2    | maximumPoolSize | 线程池中最大线程的数量                                       |
| 3    | keepAliveTime   | 当前线程数量超过corePoolSize时，空闲线程的存活时间           |
| 4    | unit            | keepAliveTime的时间单位                                      |
| 5    | workQueue       | 任务队列，被提交但尚未执行的任务存放的地方                   |
| 6    | threadFactory   | 线程工厂，用于创建线程，可使用默认的线程工厂或自定义线程工厂 |
| 7    | handler         | 由于任务过多或其他原因导致线程池无法处理时的任务拒绝策略     |

### 3.2.4 线程池的拒绝策略

1. AbortPolicy
2. CallerRunsPolicy
3. DiscardPolicy
4. DiscardOldestPolicy
5. 自定义拒绝策略

## 3.3 5种常用的线程池

1. newCachedThreadPool：可缓存的线程池
2. newFixedThreadPool：固定大小的线程池
3. newScheduledThreadPool：可做任务调度的线程池
4. newSingleThreadPool：单个线程的线程池
5. newWorkStealingPool：足够大小的线程池，JDK1.8新增

## 3.4 线程的生命周期

### 3.4.1 新建状态：New

### 3.4.2 就绪状态：Runnable

### 3.4.3 运行状态：Running

### 3.4.4 阻塞状态：Blocked

### 3.4.5 线程死亡：Dead

## 3.5 线程的基本方法

### 3.5.1 等待方法：wait()

调用wait方法的线程会进入WAITING状态，只有等到其他线程的通知或被中断后才会返回。

调用wait会释放对象的锁，wait方法一般被用于同步方法或同步代码块中。

### 3.5.2 线程睡眠：sleep()

调用sleep方法会导致当前线程休眠。

sleep方法不会释放当前占有的锁，会导致线程进入TIMED-WAITING状态。

### 3.5.3 线程让步：yield()

调用yield方法会使当前线程让出（释放）CPU执行时间片，与其他线程一起重新竞争CPU时间片。

在一般情况下，优先级高的线程更有可能竞争到CPU时间片，但存在有的操作系统对线程的优先级并不敏感。

### 3.5.4 线程加入：join()

join方法用于等待其他线程终止，如果在当前线程中调用一个线程的join方法，则当前线程会转为阻塞态，等到另一个线程结束，当前线程才由阻塞态转为就绪状态，等待CPU的使用权。

在多种情况下，主线程生成并启动了子线程，需要等到子线程返回结果并收集和处理再退出，这时就要用到join方法。

```java
System.out.println("子线程运行开始！");
ChildThread childThread = new ChildThread();
childThread.join(); //主线程（当前线程）阻塞等待子线程childThread执行结束
System.out.println("子线程join()结束，开始运行主线程");
```



### 3.5.5 线程中断：interrupt()

interrupt方法用于向线程发送一个终止通知信号，会影响该线程内部的一个中断标识位，线程本身并不会因为调用interrupt方法而改变状态（阻塞、终止等）。状态的具体变化需要等待接收到中断标识的程序的最终处理结果来判定。

对interrupt方法的理解需要4个核心点：

- 调用interrupt方法并不会中断一个正在运行的线程，也就是说处于Running状态的线程不会因调用interrupt方法而终止，仅仅改变了内部的状态位而已。具体JDK源码：

  ``` java
  public static boolean interrupted() {
  	return currentThread().isInterrupted(true);
  }
  public boolean isInterrupted() {
      return isInterrupted(false);
  }
  ```

- 若因调用sleep方法而使线程处于TIMED-WAITING状态，则这时调用interrupt方法会抛出InterruptedException，使线程提前结束TIMED-WAITING状态。

- 许多声明抛出InterruptedException的方法如`Thread.sleep(long mills)`，在抛出异常前都会清除中断标识位，所以在抛出异常后调用isInterrupted方法会返回false。

- 中断状态是线程固有的一个标识位，可以通过此标识位安全终止线程。比如，在想终止一个线程时，可以先调用该线程的interrupt方法，然后在线程的run方法中根据该线程的isInterrupted方法的返回状态值安全终止线程。

  ```java
  public class SafeInterruptedThread extends Thread {
      @Override
      public void run() {
          if (!Thread.currentThread().isInterrupted()) {
              try {
                  // 1.这里处理正常的线程业务逻辑
                  sleep(10);
              } catch (InterruptedException e) {
                  Thread.currentThread().interrupt(); //重新设置中断状态
              }
          }
          if (Thread.currentThread().isInterrupted()) {
              // 2.处理线程结束前必要的一些资源释放和清理工作，比如释放锁、存储数据到持久化层、发出异常通知等
              try {
                  sleep(10);
              } catch (InterruptedException e) {
                  e.printStackTrace();
              }
          }
      }
  }
  
  // 3.定义一个可以安全退出的线程
  SafeInterruptedThread thread = new SafeInterruptedThread();
  // 4.安全退出线程
  thread.interrupt();
  ```

  

### 3.5.6 线程唤醒：notify()

Object类中存在notify方法，用于唤醒在此对象监视器上等待的一个线程，如果所有线程都在此对象上等待，则会选择唤醒其中一个线程，唤醒时选择是任意的。

我们通常调用其中一个对象的wait方法在对象的监视器上等待，直到当前线程放弃此对象上的锁定，才能继续执行被唤醒的线程，被唤醒的线程将以常规方式与在该对象上所有同步的其他线程竞争。类似的方法还有notifyAll，用于唤醒在监视器上等待的所有线程。

### 3.5.7 多线程打印

分别定义两个线程打印数字和字母，打印数字线程打印完两个数字切换到打印字母线程打印一个字母，又切换回打印数字线程。

```java
public class ThreadPrint2 {
    public static void main(String[] args) {
        Object object = new Object(); //共享资源，实现线程间的通信
        Thread printNum = new Thread(new PrintNum(object));
        Thread printChar = new Thread(new PrintChar(object));
        printNum.start();
        printChar.start();
    }
}

class PrintNum implements Runnable {
    private Object object;

    public PrintNum(Object object) {
        this.object = object;
    }

    @Override
    public void run() {
        synchronized (object) {
            for (int i = 1; i <= 52; i++) {
                System.out.print(i);
                if (i % 2 == 0) {
                    object.notifyAll(); //唤醒所有线程
                    try {
                        object.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
}

class PrintChar implements Runnable {
    private Object object;

    public PrintChar(Object object) {
        this.object = object;
    }

    public void run() {
        synchronized (object) {
            for (int i = 65; i <= 90; i++) {
                System.out.print((char)i);
                object.notifyAll();
                try {
                    object.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```



### 3.5.8 后台守护线程：setDaemon()

setDaemon方法用于定义一个守护线程，也叫作”服务线程“，该线程是后台线程。后台线程有一个特性，即为用户线程提供公共服务，在没有用户线程可服务时会自动离开。

守护线程是JVM级别的，比如垃圾回收线程就是一个经典的守护线程，在我们的程序中不再有任何线程运行时，程序就不会再产生垃圾，垃圾回收器也就无事可做，所以在回收JVM上仅剩的线程时，垃圾回收线程会自动离开。它始终在低级别的状态下运行，用于实时监控和管理系统中的回收资源。

在JVM中的所有线程都是守护线程时，JVM就可以退出了，如果还有一个或一个以上的非守护线程，JVM就不会退出。

### 3.5.9 start()方法和run()方法的区别

## 3.6 Java中的锁

Java中的锁主要用于保障线程在多并发情况下数据的一致性。在多线程编程中为了保障数据的一致性，我们通常需要在使用对象或者调用方法之前加锁，这时如果有其他线程也需要调用该对象或者调用该方法，则首先需要先获得锁。如果某个线程发现锁正在被其他线程使用，就会进入阻塞队列等待锁的释放，直到其他线程执行完并释放锁，该线程才有机会再次获取锁并执行操作。这样就保障了在同一时刻只有一个线程持有该对象的锁并修改该对象，从而保障数据的安全。



### 3.6.1 乐观锁

乐观锁采用乐观的思想处理数据，在每次读取数据时都认为别人不会修改数据，所以不会加锁，但在更新的时候会判断在此期间别人有没有更新该数据，通常采用在写时先读出当前版本号然后加锁的方法。具体过程为：比较当前版本号与上一次的版本号，如果版本号一致，则更新，如果不一致，则重复进行读、比较、写操作。

Java中的乐观锁大部分是通过CAS（Compare And Swap，比较和交换）操作实现的，CAS是一种原子操作，在对数据操作之前首先会比较当前值与传入的值是否一样，如果一样则更新，否则不执行更新操作，直接返回失败状态。

### 3.6.2 悲观锁

悲观锁采用悲观思想处理数据，在每次读取数据时都认为别人会修改数据，所以每次在读写数据时都会加锁，这样别人想读写这个数据时就会阻塞、等待直到获取锁。

Java中的悲观锁大部分基于AQS（Abstract Queued Synchronized，抽象的队列同步器）架构实现。AQS定义了一套多线程访问共享资源的同步框架，许多同步类的实现都依赖于它，例如常用的Synchronized、ReentrantLock、Semaphore、CountDownLatch等。

该框架下的锁会先尝试以CAS乐观锁去获取锁，如果获取不到，则会转为悲观锁（如ReentrantLock）。

### 3.6.3 自旋锁

### 3.6.4 synchronized

synchronized关键字用于为Java对象、方法、代码块提供线程安全的操作。synchronized属于独占式的悲观锁，同时输入可重入锁。在使用synchronized修饰对象时，同一时刻只能有一个线程对该对象进行访问；在synchronized修饰方法、代码块时，同一时库只能有一个线程执行该方法体或代码块，其他线程只能等待当前线程执行完毕并释放锁资源后才能访问该对象或执行同步代码块。

Java中的每个对象都有个monitor对象，加锁就是在竞争monitor对象。对代码块加锁是通过在前后分别加上monitorenter和moniterexit指令实现的，对方法是否加锁是通过一个标志位来判断的。

#### 1. synchronized的作用范围

- synchronized作用于成员变量和非静态方法时，锁住的是对象的实例，即this对象。
- synchronized作用于静态方法时，锁住的是Class实例，因为静态方法属于Class而不属于对象。
- synchronized作用于一个代码块时，锁住的是所有代码块中配置的对象。

#### 2. synchronized的用法简介



### 3.6.5 ReentranLock

### 3.6.6 Semaphore

### 3.6.7 AtomicInteger

### 3.6.9 可重入锁

可重入锁也叫递归锁，指在同一线程中，在外层函数获取到该锁之后，内层的递归函数仍然可以继续获取该锁。在Java环境下，ReentrantLock和synchronized都是可重入锁。

### 3.6.10 公平锁与非公平锁

- 公平锁（Fair Lock）指在分配锁前检查是否有线程在排队等待获取该锁，优先将该锁分配给排队时间最长的线程。
- 非公平锁（Nonfair Lock）指在分配锁时不考虑线程排队等待的情况，直接尝试获取锁，在获取不到锁时再排队到队尾等待。

因为公平锁需要在多线程的情况下维护一个锁线程等待队列，基于该队列进行锁的分配，因此效率比非公平锁低很多。Java中的synchronized是非公平锁，ReentrantLock默认的lock方法采用的是非公平锁。

## 3.7 线程上下文切换

CPU利用时间片轮询来为每个任务都服务一定时间，然后把当前任务的状态保存下来，继续服务下一个任务。任务的状态保存及再加载的过程叫作线程的上下文切换。

- 进程：指一个运行中的程序的实例。在一个进程的内部可以有多个线程同时运行，并与创建它的进程共享同一地址空间（一段内存区域）和其他资源。
- 上下文：指进程切换时CPU寄存器和程序计数器所保存的当前线程的信息。
- 寄存器：指CPU内部容量较小但速度很快的内存区域（与之对应的是CPU外部相对较慢的RAM主内存）。寄存器通过对常用值（通常是运算的中间值）的快速访问来加快计算器程序运行的速度。
- 程序计数器：是一个专用的寄存器，用于表明指令序列中CPU正在执行的位置，存储的值为正在执行的指令的位置或者下一个被执行的指令的位置，这依赖于特定的系统。

### 3.7.1 上下文切换

上下文切换是指内核在CPU上对进程或线程进行切换。上下文切换时的过程信息被保存在进程控制块（PCB-Process Control Block）中。PCB又被叫作切换帧（SwitchFrame）。上下文切换的信息会被一直保存在CPU的内存中，直到被再次使用。上下文切换流程：

1. 挂起一个线程，将这个线程在CPU中的状态（上下文信息）存储在内存的PCB中。
2. 在PCB中检索下一个线程的上下文并将其子啊CPU的寄存器中恢复。
3. 跳转到程序计数器所指向的位置（即跳转到线程被中断时的代码行）并恢复该线程。

### 3.7.2 引起线程上下文切换的原因

- 当前正在执行的任务完成，系统的CPU正常调度下一个任务。
- 当前正在执行的任务遇到I/O等阻塞操作，调度器挂起此任务，继续调度下一个任务。
- 多个任务并发抢占锁资源，当前任务没有抢到锁资源，被调度器挂起，继续调度下一个任务。
- 用户的代码挂起当前任务，比如线程执行`sleep`方法，让出CPU。
- 硬件中断。

## 3.9 Java并发关键字

### 3.9.1 CountDownLatch

### 3.9.2 CyclicBarrier

### 3.9.3 Semaphore

### 3.9.4 volatile

## 3.11 ConcurrentHashMap并发

## 3.12 Java中的线程调度

## 3.14 CAS

## 3.15 AQS
