# java面试题

### 线程相关
#### 1. 什么是进程
进程是系统中正在运行的一个程序，程序一旦运行就是进程
#### 2. 什么是线程
是操作系统能够进行运算调度的最小单位。它被包含在进程之中，是进程中的实际运作单位。一条线程指的是进程中一个单一顺序的控制流，一个进程中可以并发多个线程，每条线程并行执行不同的任务
#### 3. 线程实现的方式
  - 继承Thread类
  - 实现runable接口
  - 实现callable接口
#### 4. Thread 类中的start() 和 run() 方法有什么区别?
 - start(): start方法来启动线程，真正实现了多线程运行，这时无需等待run方法体中的代码执行完毕而直接继续执行后续的代码。通过调用Thread类的 start()方法来启动一个线程，这时此线程处于就绪（可运行）状态，并没有运行，一旦得到cpu时间片，就开始执行run()方法，这里的run()方法 称为线程体，它包含了要执行的这个线程的内容，Run方法运行结束，此线程随即终止。
 - run(): run()方法只是类的一个普通方法而已，如果直接调用Run方法，程序中依然只有主线程这一个线程，其程序执行路径还是只有一条，还是要顺序执行，还是要等待run方法体执行完毕后才可继续执行下面的代码，这样就没有达到写线程的目的。
 - 调用start方法方可启动线程，而run方法只是thread类中的一个普通方法调用，还是在主线程里执行。
#### 5. 线程的五种状态
 - 新建状态(NEW) 新建一个了线程
 - 可运行状态(RUNNABLE) 线程对象创建后，其他线程调用此对象的start()方法，该状态的线程位于可运行线程池中，等待被线程调度选中，获取cpu 的使用权 。
 - 运行(RUNNING)：可运行状态(runnable)的线程获得了cpu 时间片（timeslice）执行程序代码
 - 阻塞(BLOCKED)：阻塞状态是指线程因为某种原因放弃了cpu 使用权，也即让出了cpu timeslice，暂时停止运行。直到线程进入可运行(runnable)状态，才有机会再次获得cpu timeslice 转到运行(running)状态。阻塞的情况分三种：
   + 等待阻塞：运行(running)的线程执行o.wait()方法，JVM会把该线程放入等待队列(waitting queue)中。
   + 同步阻塞：运行(running)的线程在获取对象的同步锁时，若该同步锁被别的线程占用，则JVM会把该线程放入锁池(lock pool)中。
   + 其他阻塞：运行(running)的线程执行Thread.sleep(long ms)或t.join()方法，或者发出了I/O请求时，JVM会把该线程置为阻塞状态。当sleep()状态超时、join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入可运行(runnable)状态。
  - 死亡(DEAD)：线程run()、main() 方法执行结束，或者因异常退出了run()方法，则该线程结束生命周期。死亡的线程不可再次复生
####  6. 如何知道代码段被哪个线程调用
```
System.out.println(Thread.currentThread().getName());
```
#### 7. sleep()方法
方法sleep()的作用是在指定的毫秒数内让当前的“正在执行的线程”休眠（暂停执行
#### 8. 停止线程
1. run方法执行完成，自然终止。
2. stop()方法，suspend()以及resume()都是过期作废方法，使用它们结果不可预期。

大多数停止一个线程的操作使用Thread.interrupt()等于说给线程打一个停止的标记, 此方法不回去终止一个正
在运行的线程，需要加入一个判断才能可以完成线程的停止
#### 9. interrupted 和 isInterrupted

interrupted : 判断当前线程是否已经中断,会清除状态。

isInterrupted ：判断线程是否已经中断，不会清除状态。

#### 10.yield
放弃当前cpu资源，将它让给其他的任务占用cpu执行时间。但放弃的时间不确定，有可能刚刚放弃，马上又获得cpu时间片

#### 11.设置线程上下文类加载器
1.  获取线程上下文类加载器
```
System.out.println(Thread.currentThread().getName());
```
2. ​ 设置线程类加载器（可以打破Java类加载器的父类委托机制）
```
public  void  setContextClassLoader(ClassLoader cl)
```

#### 12. join
 join是指把指定的线程加入到当前线程，比如join某个线程a,会让当前线程b进入等待,直到a的生命周期结束，此期间b线程是处于blocked状态。

#### 13. 什么是synchronized?
synchronized关键字可以时间一个简单的策略来防止线程干扰和内存一致性错误，如果一个对象是对多个线程可见的，那么对该对想的所有读写都将通过同步的方式来进行。

#### 14. synchronized包括哪两个jvm重要的指令？
 monitor enter 和 monitor exit
 
#### 15. synchronized关键字用法?
 可以用于对代码块或方法的修饰
 
#### 16. synchronized锁的是什么?
普通同步方法 —————> 锁的是当前实力对象。

静态同步方法—————> 锁的是当前类的Class对象。

同步方法块 —————> 锁的是synchonized括号里配置的对象。

#### 17. volatile关键字
volatile 是轻量级的synchronized,它在多处理器开发中保证了共享变量的“可见性“。

Java允许线程访问共享变量，为了保证共享变量能准确和一致的更新，线程应该确保排它锁单独获得这个变量。如果一个字段被声明为volatile,Java线程内存模型所有线程看到这个变量的值是一致的。

#### 18. 等待/通知机制
一个线程修改了一个对象的值，而另一个线程感知到了变化，然后进行相应的操作。
 等待
```
  synchronized(obj) { 
       while(条件不满足) { 
             obj.wait();
           } 
         执行对应逻辑 
  }
```

通知
```
  synchronized(obj){
        改变条件
            obj.notifyAll();

  }
```

#### 19. notify
notify()也是Object类的通用方法，也要在同步方法或同步代码块内调用，该方法用来通知哪些可能灯光对象的对象锁的其他线程，如果有多个线程等待，则随机挑选出其中一个呈wait状态的线程，对其发出 通知 notify，并让它等待获取该对象的对象锁。

#### 20. notify/notifyAll
notify等于说将等待队列中的一个线程移动到同步队列中，而notifyAll是将等待队列中的所有线程全部移动到同步队列中。

#### 21.ThreadLocal

主要解决每一个线程想绑定自己的值，存放线程的私有数据。
  - ThreadLocal使用
 获取当前的线程的值通过get(),设置set(T) 方式来设置值。
```
  public class XKThreadLocal {
  
      public static ThreadLocal threadLocal = new ThreadLocal();
  
      public static void main(String[] args) {
          if (threadLocal.get() == null) {
              System.out.println("未设置过值");
              threadLocal.set("线程学习");
          }
          System.out.println(threadLocal.get());
      }
  
  }
```
  print：
```
  未设置过值
  线程学习
```

 -  解决get()返回null问题
通过继承重写initialValue()方法即可。
```
  public class ThreadLocalExt extends ThreadLocal{
  
      static ThreadLocalExt threadLocalExt = new ThreadLocalExt();
  
      @Override
      protected Object initialValue() {
          return "线程学习";
      }
  
      public static void main(String[] args) {
          System.out.println(threadLocalExt.get());
      }
  }
```
print:
```
  线程学习
```

 
#### 22.lock接口
锁可以防止多个线程同时共享资源。Java5前程序是靠synchronized实现锁功能。Java5之后，并发包新增Lock接口来实现锁功能。

#### 23.lock接口提供synchronized不具备的主要特性

特性|描述
---|---
  尝试非阻塞地获取锁 |   当前线程尝试获取锁，如果这一时刻锁没有被其他线程获取到，则成功获取并持有该锁
 能被中断的获取锁|   与synchronized不同，获取到锁的线程能够响应中断，当获取到锁的线程被中断时，中断异常将会抛出，同时锁会被释放
 超时获取锁|   在指定时间内获取锁，如果截止时间仍没有获取到锁，则返回
   
#### 24.重入锁RenntrantLock 

1. 支持重进入的锁，它表示该锁能够支持一个线程对资源的重复加锁。除此之外，该锁的还支持获取锁时的公平和非公平性选择。

2. 重进入是指任意线程在获取到锁之后能够再次获锁而不被锁阻塞。
该特性主要解决以下两个问题：
 - 锁需要去识别获取锁的线程是否为当前占据锁的线程，如果是则再次成功获取。
 - 所得最终释放。线程重复n次是获取了锁，随后在第n次释放该锁后，其他线程能够获取到该锁。

3. RenntrantLock 默认为非公平锁
4. 公平性与否针对获取锁来说的，如果一个锁是公平的，那么锁的获取顺序就应该符合请求的绝对时间顺序，也就是FIFO。

#### 25. 读写锁
读写锁允许同一时刻多个线程访问，但是写线程和其他写线程均被阻塞。读写锁维护一个读锁一个写锁，读写分离，并发性能得到提高
java提供实现读写锁的实现类是ReentrantReadWriteLock.

#### 26.Java并发容器
1. concurrenthashmap, copyonwritearraylist, CopyOnWriteArraySet, ConcurrentLinkedQueue
2. ConcurrentSkipListMap、ConcurrentSkipListSet、ArrayBlockingQueue、
3. LinkedBlockingQueue、LinkedBlockingDeque、PriorityBlockingQueue、SynchronousQueue、LinkedTransferQueue、DelayQueue

#### 27. 什么是阻塞队列
- 阻塞队列是一个支持两个附加操作的队列,这两个附加操作支持阻塞的插入和移除方法
 1. 支持阻塞的插入方法：当队列满时，队列会阻塞插入元素的线程，直到队列不满。
 2. 支持阻塞的一处方法：当队列为空时，获取元素的线程会等待队列变为非空
- 阻塞队列的常用应用场景 ：
常用于生产者和消费者模式中，生产者是往队列中添加元素的线程，消费者是从队列中提取元素的线程。阻塞队列正好是生产者存放，消费者来获取的容器
- java里的阻塞队列
ArrayBlockingQueue： 数组结构组成的 |有界阻塞队列 
LinkedBlockingQueue： 链表结构组成的|有界阻塞队列 
PriorityBlockingQueue: 支持优先级排序|无界阻塞队列 
DelayQueue： 优先级队列实现|无界阻塞队列 
SynchronousQueue： 不存储元素| 阻塞队列 
LinkedTransferQueue： 链表结构组成|无界阻塞队列 
LinkedBlockingDeque： 链表结构组成|双向阻塞队列

#### 28. 为什么使用线程池
1. 降低系统消耗: 重复利用已经创建的线程降低线程创建和销毁造成的资源损耗
2. 提高响应速度：当任务到达时，任务不需要等待线程的创建就可以立即执行
3. 提供线程的可管理性：可以通过设置合理分配、调优、监控

#### 29. 线程池的工作流程
1. 判断核心线程池的线程是否都在执行任务，否--->创建一个新的线程来执行任务。是--->走下个流程
2. 判断工作队列是否已满: 否--->新任务储存到这个工作队列中， 是---> 走下个流程
3. 判断线程池里的线程是否都在工作状态: 否--->创建一个新的线程来执行任务。是--->走下个流程
4. 按照设置的策略来处理无法执行的任务

#### 30. 创建线程池参数有哪些

```
public ThreadPoolExecutor( int corePoolSize, 
                           int maximumPoolSize, 
                           long keepAliveTime, TimeUnit unit,
                           BlockingQueue<Runnable> workQueue, 
                           ThreadFactory threadFactory, 
                           RejectedExecutionHandler handler)

```

1. corePoolSize:核心线程池大小，当提交一个任务时，线程池会创建一个线程来执行任务，即使其他空闲的核心线程能够执行新任务也会创建，等待需要执行的任务数大于线程核心大小就不会继续创建。

2. maximumPoolSize:线程池最大数，允许创建的最大线程数，如果队列满了，并且已经创建的线程数小于最大线程数，则会创建新的线程执行任务。如果是无界队列，这个参数基本没用。

3. keepAliveTime: 线程保持活动时间，线程池工作线程空闲后，保持存活的时间，所以如果任务很多，并且每个任务执行时间较短，可以调大时间，提高线程利用率。

4. unit: 线程保持活动时间单位，天（DAYS)、小时(HOURS)、分钟(MINUTES、毫秒MILLISECONDS)、微秒(MICROSECONDS)、纳秒(NANOSECONDS)

5. workQueue: 任务队列，保存等待执行的任务的阻塞队列。
一般来说可以选择如下阻塞队列：
ArrayBlockingQueue:基于数组的有界阻塞队列。
LinkedBlockingQueue:基于链表的阻塞队列。
SynchronizedQueue:一个不存储元素的阻塞队列。
PriorityBlockingQueue:一个具有优先级的阻塞队列。

6. threadFactory：设置创建线程的工厂，可以通过线程工厂给每个创建出来的线程设置更有意义的名字。

7. handler: 饱和策略也叫拒绝策略。当队列和线程池都满了，即达到饱和状态。所以需要采取策略来处理新的任务。默认策略是AbortPolicy。
 AbortPolicy:直接抛出异常。
CallerRunsPolicy: 调用者所在的线程来运行任务。
DiscardOldestPolicy:丢弃队列里最近的一个任务，并执行当前任务。
DiscardPolicy:不处理，直接丢掉。
当然可以根据自己的应用场景，实现RejectedExecutionHandler接口自定义策略。


