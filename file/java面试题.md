
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

#### 31.线程池提交的方式
excute(),  submit()
excute(): 无返回值，所以无法判断任务是否成功
submit()：用于提交需要有返回值的任务，线程池返回一个future类型对象，通过future对象可以判断任务是否执行成功，并且通过future的get()来获取返回值，get()方法会阻塞当前线程知道任务完成。get(long timeout,TimeUnit unit)可以设置超市时间。

#### 32. 线程池关闭方式
可以通过shutdown(0或shutdownnow()来关闭线程池。他们的工作原理是通过遍历线程池的工作线程，然后逐个调用线程的interrupt来中断线程，所以无法响应终端的任务可能永远停止。
shutdownnow首先将线程池状态设置为stop，然后尝试停止所有正在执行或者暂停的线程，并返回等待执行的列表
shutdown只是将线程池的状态设置为shutdown状态，然后终端所有没有正在执行任务的线程。

只要调用两者之一，isShutdown就会返回true,当所有任务都已关闭，isTerminaed就会返回true。

一般来说调用shutdown方法来关闭线程池，如果任务不一定要执行完，可以直接调用shutdownNow方法。


#### 33. Excutor
1. 从JDK1.5开始，把工作单元和执行机制分开。工作单元包括Runable和callable，而执行机制有Executor框架提供。
2. executor框架的主要成员
 - ThreadPoolExecutor :可以通过工厂类Executors来创建。
 - 可以创建3种类型的ThreadPoolExecutor：SingleThreadExecutor、FixedThreadPool、CachedThreadPool。
- ScheduledThreadPoolExecutor ：可以通过工厂类Executors来创建。

- 可以创建2中类型的ScheduledThreadPoolExecutor：ScheduledThreadPoolExecutor、SingleThreadScheduledExecutor

- Future接口:Future和实现Future接口的FutureTask类来表示异步计算的结果。

- Runnable和Callable:它们的接口实现类都可以被ThreadPoolExecutor或ScheduledThreadPoolExecutor执行。Runnable不能返回结果，Callable可以返回结果。


### java基础

#### 1. JDK 和 JRE 有什么区别
- JDK：Java Development Kit 的简称，java 开发工具包，提供了 java 的开发环境和运行环境。
- JRE：Java Runtime Environment 的简称，java 运行环境，为 java 的运行提供了所需环境。

具体来说 JDK 其实包含了 JRE，同时还包含了编译 java 源码的编译器 javac，还包含了很多 java 程序调试和分析的工具。简单来说：如果你需要运行 java 程序，只需安装 JRE 就可以了，如果你需要编写 java 程序，需要安装 JDK。

#### 2. == 和equals
1. == 对于基本类型来说是值比较，对于引用类型来说是比较的是引用；
2. equals 默认情况下是引用比较，只是很多类重新了 equals 方法，比如 String、Integer 等把它变成了值比较，所以一般情况下 equals 比较的是值是否相等。

#### 3. 两个对象的hashcode值相同，则equals的一定为true 对吗？
不对，，因为在散列表中，hashCode()相等即两个键值对的哈希值相等，然而哈希值相等，并不一定能得出键值对相等。

4. final 在 java 中有什么作用？

*   final 修饰的类叫最终类，该类不能被继承。
*   final 修饰的方法不能被重写。
*   final 修饰的变量叫常量，常量必须初始化，初始化之后值就不能被修改。

5.  java 中的 Math.round(-1.5) 等于多少
等于 -1，因为在数轴上取值时，中间值（0.5）向右取整，所以正 0.5 是往上取整，负 0.5 是直接舍弃。

6. String 属于基础的数据类型吗？
String 不属于基础类型，基础类型有 8 种：byte、boolean、char、short、int、float、long、double，而 String 属于对象。

7. java 中操作字符串都有哪些类？它们之间有什么区别？
- 操作字符串的类有：String、StringBuffer、StringBuilder。
- String 和 StringBuffer、StringBuilder 的区别在于 String 声明的是不可变的对象，每次操作都会生成新的 String 对象，然后将指针指向新的 String 对象，而 StringBuffer、StringBuilder 可以在原有对象的基础上进行操作，所以在经常改变字符串内容的情况下最好不要使用 String。
- StringBuffer 和 StringBuilder 最大的区别在于，StringBuffer 是线程安全的，而 StringBuilder 是非线程安全的，但 StringBuilder 的性能却高于 StringBuffer，所以在单线程环境下推荐使用 StringBuilder，多线程环境下推荐使用 StringBuffer。

8. String str="i"与 String str=new String("i")一样吗？
不一样，因为内存的分配方式不一样。String str="i"的方式，java 虚拟机会将其分配到常量池中；而 String str=new String("i") 则会被分到堆内存中。

9. 如何将字符串反转
使用stringbuilder 或者stringbuffer的reverse()方法

10. string类的常用方法
-   indexOf()：返回指定字符的索引。
-   charAt()：返回指定索引处的字符。
-   replace()：字符串替换。
-   trim()：去除字符串两端空白。
-   split()：分割字符串，返回一个分割后的字符串数组。
-   getBytes()：返回字符串的 byte 类型数组。
-   length()：返回字符串长度。
-   toLowerCase()：将字符串转成小写字母。
-   toUpperCase()：将字符串转成大写字符。
-   substring()：截取字符串。
-   equals()：字符串比较。

11. 抽象类必须要有抽象方法吗？
不需要

12. 普通类和抽象类的区别
- 普通类不能包含抽象方法
- 抽象类不能实例化

13. 抽象类可以用final修饰吗？
不能，抽象类本来就是用来被继承的

14. 接口和抽象类有什么区别
- 抽象类的子类用extend来继承， 接口必须用implements来实现接口
- 抽象类可以有构造函数， 接口不可以
- main 抽象类可以有main方法， 接口不可以
- 类可以实现多个接口，但只能继承一个抽象类 
- 接口的方法默认使用public, 抽象类可以使用任意修饰符

15. java中流分几种
- 按功能分 ： 输入流、 输出流
- 按类型分： 字节流、 字符流
字节流和字符流的区别： 字节流按8位为传输单位输入输出数据，字符流按16位为输出输入单位

16. BIO NIO AIO 的区别
- BIO：Block IO 同步阻塞式 IO，就是我们平常使用的传统 IO，它的特点是模式简单使用方便，并发处理能力低。
- NIO：New IO 同步非阻塞 IO，是传统 IO 的升级，客户端和服务器端通过 Channel（通道）通讯，实现了多路复用。
- AIO：Asynchronous IO 是 NIO 的升级，也叫 NIO2，实现了异步非堵塞 IO ，异步 IO 的操作基于事件和回调机制。

17. file的常用方法
-   Files.exists()：检测文件路径是否存在。
-   Files.createFile()：创建文件。
-   Files.createDirectory()：创建文件夹。
-   Files.delete()：删除一个文件或目录。
-   Files.copy()：复制文件。
-   Files.move()：移动文件。
-   Files.size()：查看文件个数。
-   Files.read()：读取文件。
-   Files.write()：写入文件。

### 集合


18. java 的容器有哪些
![java容器](https://raw.githubusercontent.com/FuLaMeiEr/java-learn-md/master/report/picture/java%E5%AE%B9%E5%99%A8.png)

19. collection和collections有什么区别
- java.util.collection 是一个集合接口。他提供了对集合对象进行基本操作的通用接口方法。Conllection 在Java中类库中有很多的具体的实现。Collection接口的意义就是为各种具体的集合提供统一操作方式，其直接继承接口的有List和set.
- Collections则是集合类的一个工具类。其中提供了一系列的静态方法，用于对集合中元素的排序、搜索以及线程安全的各种啊哦做

20. List、Map、Set的区别
![List、Map、Set的区别](https://github.com/FuLaMeiEr/java-learn-md/blob/master/report/picture/list%E3%80%81set%E3%80%81map%E7%9A%84%E5%8C%BA%E5%88%AB.png)

21. HashMap 和 Hashtable 有什么区别？
- hashMap去掉了HashTable 的contains方法，但是加上了containsValue（）和containsKey（）方法。
- hashTable同步的，而HashMap是非同步的，效率上逼hashTable要高。
- hashMap允许空键值，而hashTable不允许。

22. 如何决定使用 HashMap 还是 TreeMap？
对于在Map中插入、删除和定位元素这类操作，HashMap是最好的选择。然而，假如你需要对一个有序的key集合进行遍历，TreeMap是更好的选择。基于你的collection的大小，也许向HashMap中添加元素会更快，将map换为TreeMap进行有序key的遍历。
23. HashMap 的实现原理
- HashMap概述：HashMap是基于哈希表的Map接口的非同步实现。此实现提供所有可选的映射操作，并允许使用null值和null键。此类不保证映射的顺序，特别是它不保证该顺序恒久不变。 

- HashMap的数据结构：在java编程语言中，最基本的结构就是两种，一个是数组，另外一个是模拟指针（引用），所有的数据结构都可以用这两个基本结构来构造的，HashMap也不例外。HashMap实际上是一个“链表散列”的数据结构，即数组和链表的结合体。

- 当我们往Hashmap中put元素时,首先根据key的hashcode重新计算hash值,根绝hash值得到这个元素在数组中的位置(下标),如果该数组在该位置上已经存放了其他元素,那么在这个位置上的元素将以链表的形式存放,新加入的放在链头,最先加入的放入链尾.如果数组中该位置没有元素,就直接将该元素放到数组的该位置上。

- 需要注意Jdk 1.8中对HashMap的实现做了优化,当链表中的节点数据超过八个之后,该链表会转为红黑树来提高查询效率,从原来的O(n)到O(logn)
24. HashSet的实现原理
- HashSet底层由HashMap实现
- HashSet的值存放于HashMap的key上
- HashMap的value统一为PRESENT

25. ArrayList 和 LinkedList 的区别是什么？
最明显的区别是 ArrrayList底层的数据结构是数组，支持随机访问，而 LinkedList 的底层数据结构是双向循环链表，不支持随机访问。使用下标访问一个元素，ArrayList 的时间复杂度是 O(1)，而 LinkedList 是 O(n)。

26. 如何实现数组和 List 之间的转换
- List转化为数组：调用ArrayList的toArray方法
- 数组转化为List: 调用Arrays的asList方法

27. ArrayList 和 Vector 的区别是什么
- Vector是同步的，而ArrayList不是。然而，如果你寻求在迭代的时候对列表进行改变，你应该使用CopyOnWriteArrayList。 
- ArrayList比Vector快，它因为有同步，不会过载。 
- ArrayList更加通用，因为我们可以使用Collections工具类轻易地获取同步列表和只读列表。

28. Array 和 ArrayList 有何区别

- Array可以容纳基本类型和对象，而ArrayList只能容纳对象
- Array是指定大小的，而ArrayList是固定大小
- Array没有提供ArrayList那么多功能，比如addAll、removeAll和iterator等。

29. 在 Queue 中 poll()和 remove()有什么区别
poll() 和 remove() 都是从队列中取出一个元素，但是 poll() 在获取元素失败的时候会返回空，但是 remove() 失败的时候会抛出异常。

30. 哪些集合类是线程安全的
- vector：就比arraylist多了个同步化机制（线程安全），因为效率较低，现在已经不太建议使用。在web应用中，特别是前台页面，往往效率（页面响应速度）是优先考虑的。
- statck：堆栈类，先进后出。
- hashtable：就比hashmap多了个线程安全。
- enumeration：枚举，相当于迭代器。

31. 迭代器 Iterator 是什么
迭代器是一种设计模式，它是一个对象，它可以遍历并选择序列中的对象，而开发人员不需要了解该序列的底层结构。迭代器通常被称为“轻量级”对象，因为创建它的代价小。

32. Iterator 怎么使用？有什么特点
- Java中的Iterator功能比较简单，并且只能单向移动：
(1) 使用方法iterator()要求容器返回一个Iterator。第一次调用Iterator的next()方法时，它返回序列的第一个元素。注意：iterator()方法是java.lang.Iterable接口,被Collection继承。
(2) 使用next()获得序列中的下一个元素。
(3) 使用hasNext()检查序列中是否还有元素。
(4) 使用remove()将迭代器新返回的元素删除。
- Iterator是Java迭代器最简单的实现，为List设计的ListIterator具有更多的功能，它可以从两个方向遍历List，也可以从List中插入和删除元素。

33. Iterator 和 ListIterator 有什么区别
- Iterator可用来遍历Set和List集合，但是ListIterator只能用来遍历List。 
- Iterator对集合只能是前向遍历，ListIterator既可以前向也可以后向。 
- ListIterator实现了Iterator接口，并包含其他的功能，比如：增加元素，替换元素，获取前一个和后一个元素的索引，等等。

### 反射
34. 什么是反射
反射主要是指程序可以访问、检测和修改它本身状态或行为的一种能力

35. java反射
在java运行环境中，对于任何一个类，都能知道这个了类的属性和方法，对于一个对象，可以调用其方法和属性

36. 什么是java序列化
- 简单说就是为了保存在内存中的各种对象的状态（也就是实例变量，不是方法），并且可以把保存的对象状态再读出来。虽然你可以用你自己的各种各样的方法来保存object states，但是Java给你提供一种应该比你自己好的保存对象状态的机制，那就是序列化。

- 什么情况需要序列化
    1. 当你想把内存中的对象状态保存到一个文件中或者数据库中
    2. 当你想用套接字在网络上传送对象的时候
    3. 当你想通过RMI 传输对象的时候

59. 动态代理是什么？有哪些应用？

- 动态代理：
当想要给实现某个接口类中的方法，加一些额外的处理。比如加日志、事务等。可以给这个类创建一个代理，顾名思义就是创建一个新的类，这个类不仅包括原来类方法的功能，而且在原来的基础上添加了额外处理的新类，这个代理类并不是定义好的而是自动生成的。具有解耦意思、灵活扩展性强

- 动态代理的应用
    1. Spring中的AOP
    2. 加事务
    3. 加权限
    4. 加日志

60. 怎么实现动态代理
首先必须定义一个接口，还要有一个InvcationHandler(将实现接口的类的对象传递给它)处理类。再加一个工具类Proxy(习惯性的称之为代理类，因为调用他的nweInstance()可以产生代理对象，其实就是一个代理对象的工具类)。利用到InvocationHandler,拼接代理类源代码，将其编译成代理类的二进制代码。利用加载器加载，并将其实例化产生代理对象，最后返回。

### 对象拷贝
61. 为什么使用克隆
相对一个对象进行处理，又想保留原来的数据进行接下来的操作，就需要克隆，java语言中的克隆针对的是类的示例

### javaWeb

62. jsp和servlet有什么区别
     1. jsp经编译后就成了Servlet(jsp的本质就是servlet,jvm只能之别java的类，不能识别jsp代码，web容器将jsp代码编译成jvm能识别的java类)
     2. jsp更擅长表现于页面显示，servlet更擅长逻辑控制
     3. Servlet没有内置对象，Jsp中的内置对象都是必须通过HttpServletRequest对象，HttpServletResponse对象以及HttpServlet对象得到。
     4. Jsp是Servlet的一种简化，使用Jsp只需要完成程序员需要输出到户端的内容，Jsp中的Java脚本如何镶嵌到一个类中，由Jsp容器完成。而Servlet则是个完整的Java类，这个类的Service方法用于生成对客户端的响应。

63. jsp有哪些内置对象，有什么作用
- request 封装客户端请求，其中包含get、post的参数
- response 封装服务器对客户端的响应
- pageContext 通过该对象可以获取其他对象
- seesion 封装用户会话对象
- application 封装服务器运行环境对象
- out 输出服务器响应的输出流对象
- config web配置对象
- page jsp页面
- exception 封装页面抛出的异常对象

64. jsp的四种作用域
- jsp的四种作用域包括page、request、session、application。
- page 代表与页面相关的对象和属性
- request 代表与Web客户机发出的一个请求相关的对象和属性。一个请求可能跨越多个页面，涉及多个Web组件；需要在页面显示的临时数据可以置于此作用域。
- session 代表与某个用户与服务器建立的一次会话相关的对象和属性。跟某个用户相关的数据应该放在用户自己的session中。
- application 代表与整个Web应用程序相关的对象和属性，它实质上是跨越整个Web应用程序，包括多个页面、请求和会话的一个全局作用域。

65. seesion与cookie的区别
- 由于HTTP协议是无状态的协议，所以服务端需要记录用户的状态时，就需要用某种机制来识具体的用户，这个机制就是Session.典型的场景比如购物车，当你点击下单按钮时，由于HTTP协议无状态，所以并不知道是哪个用户操作的，所以服务端要为特定的用户创建了特定的Session，用用于标识这个用户，并且跟踪用户，这样才知道购物车里面有几本书。这个Session是保存在服务端的，有一个唯一标识。在服务端保存Session的方法很多，内存、数据库、文件都有。集群的时候也要考虑Session的转移，在大型的网站，一般会有专门的Session服务器集群，用来保存用户会话，这个时候 Session 信息都是放在内存的，使用一些缓存服务比如Memcached之类的来放 Session。

- 思考一下服务端如何识别特定的客户？这个时候Cookie就登场了。每次HTTP请求的时候，客户端都会发送相应的Cookie信息到服务端。实际上大多数的应用都是用 Cookie 来实现Session跟踪的，第一次创建Session的时候，服务端会在HTTP协议中告诉客户端，需要在 Cookie 里面记录一个Session ID，以后每次请求把这个会话ID发送到服务器，我就知道你是谁了。有人问，如果客户端的浏览器禁用了 Cookie 怎么办？一般这种情况下，会使用一种叫做URL重写的技术来进行会话跟踪，即每次HTTP交互，URL后面都会被附加上一个诸如 sid=xxxxx 这样的参数，服务端据此来识别用户。

-  Cookie其实还可以用在一些方便用户的场景下，设想你某次登陆过一个网站，下次登录的时候不想再次输入账号了，怎么办？这个信息可以写到Cookie里面，访问网站的时候，网站页面的脚本可以读取这个信息，就自动帮你把用户名给填了，能够方便一下用户。这也是Cookie名称的由来，给用户的一点甜头。所以，总结一下：Session是在服务端保存的一个数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中；Cookie是客户端保存用户信息的一种机制，用来记录用户的一些信息，也是实现Session的一种方式。

66. session的工作原理
其实session是一个存在服务器上的类似于一个散列表格的文件。里面存有我们需要的信息，在我们需要用的时候可以从里面取出来。类似于一个大号的map吧，里面的键存储的是用户的sessionid，用户向服务器发送请求的时候会带上这个sessionid。这时就可以从中取出对应的值了。

67. 如果客户端关闭cookie，sessIon还能用吗
- Cookie与 Session，一般认为是两个独立的东西，Session采用的是在服务器端保持状态的方案，而Cookie采用的是在客户端保持状态的方案。但为什么禁用Cookie就不能得到Session呢？因为Session是用Session ID来确定当前对话所对应的服务器Session，而Session ID是通过Cookie来传递的，禁用Cookie相当于失去了Session ID，也就得不到Session了。

- 假定用户关闭Cookie的情况下使用Session，其实现途径有以下几种：
  1.  设置php.ini配置文件中的“session.use_trans_sid = 1”，或者编译时打开打开了“--enable-trans-sid”选项，让PHP自动跨页传递Session ID。
  2.  手动通过URL传值、隐藏表单传递Session ID。
  3.  用文件、数据库等形式保存Session ID，在跨页过程中手动调用。

68. springmvc和Struts的区别是什么
- 拦截机制的不同
    1. Struts2是类级别的拦截，每次请求就会创建一个Action，和Spring整合时Struts2的ActionBean注入作用域是原型模式prototype，然后通过setter，getter吧request数据注入到属性。Struts2中，一个Action对应一个request，response上下文，在接收参数时，可以通过属性接收，这说明属性参数是让多个方法共享的。Struts2中Action的一个方法可以对应一个url，而其类属性却被所有方法共享，这也就无法用注解或其他方式标识其所属方法了，只能设计为多例。

    2. SpringMVC是方法级别的拦截，一个方法对应一个Request上下文，所以方法直接基本上是独立的，独享request，response数据。而每个方法同时又何一个url对应，参数的传递是直接注入到方法中的，是方法所独有的。处理结果通过ModeMap返回给框架。在Spring整合时，SpringMVC的Controller Bean默认单例模式Singleton，所以默认对所有的请求，只会创建一个Controller，有应为没有共享的属性，所以是线程安全的，如果要改变默认的作用域，需要添加@Scope注解修改。

    3. Struts2有自己的拦截Interceptor机制，SpringMVC这是用的是独立的Aop方式，这样导致Struts2的配置文件量还是比SpringMVC大。

-  底层框架的不同
 Struts2采用Filter（StrutsPrepareAndExecuteFilter）实现，SpringMVC（DispatcherServlet）则采用Servlet实现。Filter在容器启动之后即初始化；服务停止以后坠毁，晚于Servlet。Servlet在是在调用时初始化，先于Filter调用，服务停止后销毁。

-  性能方面
Struts2是类级别的拦截，每次请求对应实例一个新的Action，需要加载所有的属性值注入，SpringMVC实现了零配置，由于SpringMVC基于方法的拦截，有加载一次单例模式bean注入。所以，SpringMVC开发效率和性能高于Struts2。

- 配置方面
spring MVC和Spring是无缝的。从这个项目的管理和安全上也比Struts2高。

69. 如何避免 sql 注入
    1.  PreparedStatement（简单又有效的方法）
    2.  使用正则表达式过滤传入的参数
    3.  字符串过滤
    4.  JSP中调用该函数检查是否包函非法字符
    5.  JSP页面判断代码

70. 什么是 XSS 攻击，如何避免？
- XSS攻击又称CSS,全称Cross Site Script  （跨站脚本攻击），其原理是攻击者向有XSS漏洞的网站中输入恶意的 HTML 代码，当用户浏览该网站时，这段 HTML 代码会自动执行，从而达到攻击的目的。XSS 攻击类似于 SQL 注入攻击，SQL注入攻击中以SQL语句作为用户输入，从而达到查询/修改/删除数据的目的，而在xss攻击中，通过插入恶意脚本，实现对用户游览器的控制，获取用户的一些信息。 XSS是 Web 程序中常见的漏洞，XSS 属于被动式且用于客户端的攻击方式。

- XSS防范的总体思路是：对输入(和URL参数)进行过滤，对输出进行编码。

71. 什么是CFRS攻击，如何避免
- CSRF（Cross-site request forgery）也被称为 one-click attack或者 session riding，中文全称是叫**跨站请求伪造**。一般来说，攻击者通过伪造用户的浏览器的请求，向访问一个用户自己曾经认证访问过的网站发送出去，使目标网站接收并误以为是用户的真实操作而去执行命令。常用于盗取账号、转账、发送虚假消息等。攻击者利用网站对请求的验证漏洞而实现这样的攻击行为，网站能够确认请求来源于用户的浏览器，却不能验证请求是否源于用户的真实意愿下的操作行为。
- 如何避免
    1. 验证 HTTP Referer 字段
HTTP头中的Referer字段记录了该 HTTP 请求的来源地址。在通常情况下，访问一个安全受限页面的请求来自于同一个网站，而如果黑客要对其实施 CSRF
攻击，他一般只能在他自己的网站构造请求。因此，可以通过验证Referer值来防御CSRF 攻击。
    2. 使用验证码
    关键操作页面加上验证码，后台收到请求后通过判断验证码可以防御CSRF。但这种方法对用户不太友好。
    3. 在请求地址中加入token并验证
        + CSRF 攻击之所以能够成功，是因为黑客可以完全伪造用户的请求，该请求中所有的用户验证信息都是存在于cookie中，因此黑客可以在不知道这些验证信息的情况下直接利用用户自己的cookie 来通过安全验证。要抵御 CSRF，关键在于在请求中放入黑客所不能伪造的信息，并且该信息不存在于 cookie 之中。可以在 HTTP 请求中以参数的形式加入一个随机产生的 token，并在服务器端建立一个拦截器来验证这个 token，如果请求中没有token或者 token 内容不正确，则认为可能是 CSRF 攻击而拒绝该请求。这种方法要比检查 Referer 要安全一些，token 可以在用户登陆后产生并放于session之中，然后在每次请求时把token 从 session 中拿出，与请求中的 token 进行比对，但这种方法的难点在于如何把 token 以参数的形式加入请求。
        + 于 GET 请求，token 将附在请求地址之后，这样 URL 就变成 http://url?csrftoken=tokenvalue。
        + 对于 POST 请求来说，要在 form 的最后加上 <input type="hidden" name="csrftoken" value="tokenvalue"/>，这样就把token以参数的形式加入请求了
    4. 在HTTP 头中自定义属性并验证
这种方法也是使用 token 并进行验证，和上一种方法不同的是，这里并不是把 token 以参数的形式置于 HTTP 请求之中，而是把它放到 HTTP 头中自定义的属性里。通过 XMLHttpRequest 这个类，可以一次性给所有该类请求加上 csrftoken 这个 HTTP 头属性，并把 token 值放入其中。这样解决了上种方法在请求中加入 token 的不便，同时，通过 XMLHttpRequest 请求的地址不会被记录到浏览器的地址栏，也不用担心 token 会透过 Referer 泄露到其他网站中去。

### 异常
72. throw和throws的区别
throws是用来声明一个方法可能抛出的所有异常信息，throw只将异常声明但是不处理，而是将异常往上传，谁调用我就交给谁处理。而throw是具体抛出一个异常类型

73. final、finally、finalize有什么区别
- final可以修饰类、方法、变量。修饰类表示该类不能被继承、修饰方法表示该方法不能被重写、修饰变量表示该变量不能重新赋值
- finally 一般在try-catch代码块中，在处理异常的时候，通常我们一定要执行的代码方法finally代码块中，表示不管是否出现异常,该代码都会被执行。一般用来关闭资源的代码
- finalize是一个方法、属于Object类，该方法一般有垃圾回收机制调用，当我们调用System的gc()方法的时候，由垃圾回收器调用finalize(),回收垃圾

74. 常见异常类有哪些
  - NullPointerException: 空异常    当程序视图访问空对象时，抛出该异常
  - SQLException: Sql异常     提供关于数据库访问错误或其他错误信息的异常。
  - IndexOutOfBoundsException：数组下标越界异常 指示某排序索引（例如对数组、字符串或向量的排序）超出范围时抛出。
  - NumberFormatException :  数字格式异常 当应用程序试图将字符串转换成一种数值类型，但该字符串不能转换为适当格式时，抛出该异常。
  - FileNotFoundException: 文件找不到异常  当试图打开指定路径名表示的文件失败时，抛出此异常。
  - IOException：io异常 当发生某种I/O异常时，抛出此异常。此类是失败或中断的I/O操作生成的异常的通用类。
  - ClassCastException:  类型转换异常 当试图将对象强制转换为不是实例的子类时，抛出该异常。
  - ArrayStoreException:                  试图将错误类型的对象存储到一个对象数组时抛出的异常。
  - IllegalArgumentException 不合法参数异常 抛出的异常表明向方法传递了一个不合法或不正确的参数。
  - ArithmeticException：算术运算异常 当出现异常的运算条件时，抛出此异常。例如，一个整数“除以零”时，抛出此类的一个实例。
  - NegativeArraySizeException 如果应用程序试图创建大小为负的数组，则抛出该异常。
  - NoSuchMethodException：无法找到某一特定方法时，抛出该异常。
  - SecurityException：由安全管理器抛出的异常，指示存在安全侵犯。
  - UnsupportedOperationException：当不支持请求的操作时，抛出该异常。
  - RuntimeExceptionRuntimeException： 是那些可能在Java虚拟机正常运行期间抛出的异常的超类。


### 设计模式

75. 常用的设计模式
- 单利模式  就是一个应用程序中，某个类的实例对象只有一个，你没有办法去new，因为构造器是被private修饰的，一般通过getInstance()的方法来获取它们的实例。
  1.  懒汉式写法
```
   private static Singleton instance;  
     private Singleton (){}  
     public static synchronized Singleton getInstance() {  
     if (instance == null) {  
         instance = new Singleton();  
     }  
     return instance;  
     }
```
  2. 饿汉式
      
```

  public class Singleton{
     private static Singleton instance;
     private Singleton(){}
     public Singleton getInstance();
     return instance;
  }
```
   3. 静态内部类

```
  public class Singleton{
    private static class SingletonHolder{
      private static final Singleton INSTANCE = new Singleton(); 
    }

    private Singleton(){}
    public static final Singleton getInstance(){
      return SingletonHolder.getInstance();
    }
  }

```

    4. 枚举
    
```
  public enum Singleton{
    INSTANCE;
    public void whateverMethod{
    }
  
  }

```

- 工厂模式 
  1. 简单工厂模式：一个抽象的接口，多个抽象接口的实现类，一个工厂类，用来实例化抽象的接口
```
  abstract class Car{
    public void run();

    public void stop();
  
  }

  class Benz implements Car {
   public void run() {
       System.out.println("Benz开始启动了。。。。。");
   }

   public void stop() {
       System.out.println("Benz停车了。。。。。");
   }
}

class Ford implements Car {
   public void run() {
       System.out.println("Ford开始启动了。。。");
   }

   public void stop() {
       System.out.println("Ford停车了。。。。");
   }
}

  class Factory {
   public static Car getCarInstance(String type) {
       Car c = null;
       if ("Benz".equals(type)) {
           c = new Benz();
       }
       if ("Ford".equals(type)) {
           c = new Ford();
       }
       return c;
   }
}

public class Test {

   public static void main(String[] args) {
       Car c = Factory.getCarInstance("Benz");
       if (c != null) {
           c.run();
           c.stop();
       } else {
           System.out.println("造不了这种汽车。。。");
       }

   }
```

   2. 工厂方法模式：有四个角色，抽象工厂模式，具体工厂模式，抽象产品模式，具体产品模式。不再是由一个工厂类去实例化具体的产品，而是由抽象工厂的子类去实例化产品
```
  public interface Moveable {
     void run();
  }
  
  // 具体产品角色
  public class Plane implements Moveable {
     @Override
     public void run() {
         System.out.println("plane....");
     }
  }
  
  public class Broom implements Moveable {
     @Override
     public void run() {
         System.out.println("broom.....");
     }
  }
  
  // 抽象工厂
  public abstract class VehicleFactory {
     abstract Moveable create();
  }
  
  // 具体工厂
  public class PlaneFactory extends VehicleFactory {
     public Moveable create() {
         return new Plane();
     }
  }
  
  public class BroomFactory extends VehicleFactory {
     public Moveable create() {
         return new Broom();
     }
  }
  
  // 测试类
  public class Test {
     public static void main(String[] args) {
         VehicleFactory factory = new BroomFactory();
         Moveable m = factory.create();
         m.run();
     }
}



```
   3. 抽象工厂模式：与工厂方法模式不同的是，工厂方法模式中的工厂只生产单一的产品，而抽象工厂模式中的工厂生产多个产品

```
  public abstract class AbstractFactory{
   public abstract Vehicle createVehicle();
   public abstract Weapon createWeapon();
   public abstract Food createFood();
  }

  public class DefaultFactory extends AbstractFactory{
     @Override
     public Food createFood() {
         return new Apple();
     }
     @Override
     public Vehicle createVehicle() {
         return new Car();
     }
     @Override
     public Weapon createWeapon() {
         return new AK47();
     }
  }

  public class Test {
   public static void main(String[] args) {
       AbstractFactory f = new DefaultFactory();
       Vehicle v = f.createVehicle();
       v.run();
       Weapon w = f.createWeapon();
       w.shoot();
       Food a = f.createFood();
       a.printName();
   }
  }
```

### springboot Springcloud
76. 什么是springboot
SpringBoot是一个框架，一种全新的编程规范，他的产生简化了框架的使用，所谓简化是指简化了Spring众多框架中所需的大量且繁琐的配置文件，所以 SpringBoot是一个服务于框架的框架，服务范围是简化配置文件。

77. 为什么要用springboot
- Spring Boot使编码变简单
- Spring Boot使配置变简单
- Spring Boot使部署变简单
- Spring Boot使监控变简单

78. springboot的核心配置文件
- .propertory
- .yml

79. springboot 有哪些方式实现热部署
- 使用spring loaded
在项目中添加
```
  <build>
      <plugins>
        <plugin>
          <!-- springBoot编译插件-->
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-maven-plugin</artifactId>
          <dependencies>
              <!-- spring热部署 -->
              <!-- 该依赖在此处下载不下来，可以放置在build标签外部下载完成后再粘贴进plugin中 -->
              <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>springloaded</artifactId>
                <version>1.2.6.RELEASE</version>
              </dependency>
          </dependencies>
         </plugin>
      </plugins>
  </build>
  <添加完毕后使用mvn命令运行>
```
- 使用spring-boot-devtools
在项目的依赖文件中添加
```
  <dependency>
     <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-devtools</artifactId>
  </dependency>
```

80. jpa和hibernate的区别
- JPA Java Persistence API，是Java EE 5的标准ORM接口，也是ejb3规范的一部分
- Hibernate，当今很流行的ORM框架，是JPA的一个实现，但是其功能是JPA的超集
- JPA和Hibernate之间的关系，可以简单的理解为JPA是标准接口，Hibernate是实现。那么Hibernate是如何实现与JPA的这种关系的呢。Hibernate主要是通过三个组件来实现的，及hibernate-annotation、hibernate-entitymanager和hibernate-core
- hibernate-annotation是Hibernate支持annotation方式配置的基础，它包括了标准的JPA annotation以及Hibernate自身特殊功能的annotation。
- hibernate-core是Hibernate的核心实现，提供了Hibernate所有的核心功能。
- hibernate-entitymanager实现了标准的JPA，可以把它看成hibernate-core和JPA之间的适配器，它并不直接提供ORM的功能，而是对hibernate-core进行封装，使得Hibernate符合JPA的规范。

81. 什么是springcloud
Spring Cloud 就是致力于分布式系统、云服务的框架。
Spring Cloud 是整个 Spring 家族中新的成员，是最近云服务火爆的必然产物。
Spring Cloud 为开发人员提供了快速构建分布式系统中一些常见模式的工具，例如：
- 配置管理
- 服务注册与发现
- 断路器
- 智能路由
- 服务间调用
- 负载均衡
- 微代理
- 控制总线
- 一次性令牌
- 全局锁
- 领导选举
- 分布式会话
-  集群状态
- 分布式消息

82. springcloud断路器的作用是什么

- 在Spring Cloud中使用了Hystrix 来实现断路器的功能，断路器可以防止一个应用程序多次试图执行一个操作，即很可能失败，允许它继续而不等待故障恢复或者浪费 CPU 周期，而它确定该故障是持久的。断路器模式也使应用程序能够检测故障是否已经解决，如果问题似乎已经得到纠正，应用程序可以尝试调用操作。

- 断路器增加了稳定性和灵活性，以一个系统，提供稳定性，而系统从故障中恢复，并尽量减少此故障的对性能的影响。它可以帮助快速地拒绝对一个操作，即很可能失败，而不是等待操作超时（或者不返回）的请求，以保持系统的响应时间。如果断路器提高每次改变状态的时间的事件，该信息可以被用来监测由断路器保护系统的部件的健康状况，或以提醒管理员当断路器跳闸，以在打开状态。

83. springcloud的核心组件有哪些
    1. 服务发现——Netflix Eureka
    一个RESTful服务，用来定位运行在AWS地区（Region）中的中间层服务。由两个组件组成：Eureka服务器和Eureka客户端。Eureka服务器用作服务注册服务器。Eureka客户端是一个java客户端，用来简化与服务器的交互、作为轮询负载均衡器，并提供服务的故障切换支持。Netflix在其生产环境中使用的是另外的客户端，它提供基于流量、资源利用率以及出错状态的加权负载均衡。
    2. 客服端负载均衡——Netflix Ribbon
        Ribbon，主要提供客户侧的软件负载均衡算法。Ribbon客户端组件提供一系列完善的配置选项，比如连接超时、重试、重试算法等。Ribbon内置可插拔、可定制的负载均衡组件。
    3. 断路器——Netflix Hystrix
        断路器可以防止一个应用程序多次试图执行一个操作，即很可能失败，允许它继续而不等待故障恢复或者浪费 CPU 周期，而它确定该故障是持久的。断路器模式也使应用程序能够检测故障是否已经解决。如果问题似乎已经得到纠正，应用程序可以尝试调用操作。
    4. 服务网关——Netflix Zuul
        类似nginx，反向代理的功能，不过netflix自己增加了一些配合其他组件的特性。
    5. 分布式配置——Spring Cloud Config
    这个还是静态的，得配合Spring Cloud Bus实现动态的配置更新。



### MyBatis
84. mybatis中#{}和${}的区别是什么
 - #{}预编译处理,${}是字符串统计
 - Mybatis在处理#{}时，会将sql中的#{}替换为?号，调用PreparedStatement的set方法来赋值
 - Mybatis在处理${}时，就是把${}替换成变量的值
 - 使用#{}可以有效的防止SQL注入，提高系统安全性
85. 有几种分页方式
  - 数组分页
  - SQL分页
  - 拦截器分页
  - RowBounds分页

86. mybatis 逻辑分页和物理分页的区别是什么
 - 物理分页速度上并不一定快于逻辑分页，逻辑分页速度上也并不一定快于物理分页
 - 物理分页总是优于逻辑分页：没有必要将属于数据库端的压力加诸到应用端来，就算速度上存在优势,然而其它性能上的优点足以弥补这个缺点。

87. mybatis是否支持延时加载？延时加载的原理是什么
    1. Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。在Mybatis配置文件中，可以配置是否启用延迟加载lazyLoadingEnabled=true|false。
    2. 它的原理是，使用CGLIB创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用a.getB().getName()，拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来，然后调用a.setB(b)，于是a的对象b属性就有值了，接着完成a.getB().getName()方法的调用。这就是延迟加载的基本原理。

88. mybatis和hibernate的区别有哪些
  - Mybatis和hibernate不同，它不完全是一个ORM框架，因为MyBatis需要程序员自己编写Sql语句。
  - Mybatis直接编写原生态sql，可以严格控制sql执行性能，灵活度高，非常适合对关系数据模型要求不高的软件开发，因为这类软件需求变化频繁，一但需求变化要求迅速输出成果。但是灵活的前提是mybatis无法做到数据库无关性，如果需要实现支持多种数据库的软件，则需要自定义多套sql映射文件，工作量大。
  - Hibernate对象/关系映射能力强，数据库无关性好，对于关系模型要求高的软件，如果用hibernate开发可以节省很多代码，提高效率。

89. mybatis有哪些执行器(Executor)?
mybatis有三种基本执行器:
   - SimpleExecutor 每执行一次update或select，就开启一个Statement对象，用完立刻关闭Statement对象。
   - ReuseExecutor 执行update或select，以sql作为key查找Statement对象，存在就使用，不存在就创建，用完后，不关闭Statement对象，而是放置于Map内，供下一次使用。简言之，就是重复使用Statement对象。
   - BatchExecutor 执行update（没有select，JDBC批处理不支持select），将所有sql都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个Statement对象，每个Statement对象都是addBatch()完毕后，等待逐一执行executeBatch()批处理。与JDBC批处理相同。

90. mybatis分页插件实现的原理是什么
分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数。


### Kafka
91. kafka 可以脱离 zookeeper 单独使用吗？为什么？
kafka 不能脱离 zookeeper 单独使用，因为 kafka 使用 zookeeper 管理和协调 kafka 的节点服务器。

92. kafka有几种数据保留策略
kafka 有两种数据保存策略：按照过期时间保留和按照存储的消息大小保留。

93. kafka 同时设置了 7 天和 10G 清除数据，到第五天的时候消息达到了 10G，这个时候 kafka 将如何处理？
这个时候 kafka 会执行数据清除工作，时间和大小不论那个满足条件，都会清空数据。

94. 什么情况会导致kafka变慢
  - cpu性能瓶颈
  - 磁盘读写瓶颈
  - 网络瓶颈

95. 使用kafka集群以为注意什么
  - 集群数量不是越多越好，最好不要超过7个，因为节点越多，消息复制需要的时间也越长,整个集群的吞吐量就低
  - 集群数量最好是单数，因为超过一半故障集群就不能用了，设置为单数容错率更高。

### zookeeper

96. zookeeper是什么
zookeeper 是一个分布式的，开放源码的分布式应用程序协调服务，是 google chubby 的开源实现，是 hadoop 和 hbase 的重要组件。它是一个为分布式应用提供一致性服务的软件，提供的功能包括：配置维护、域名服务、分布式同步、组服务等。

97. zookeeper有哪些功能
  - 集群管理  监控节点的存活状态、运行请求等
  - 主节点选举  主节点挂掉之后开始进行新一轮的选举，zookeeper可协助完成选举过程
  - 分布式锁  zookeeper提供两种锁：独占锁和共享锁。。独占锁即一次只能有一个线程使用资源，共享锁是读锁共享，读写互斥，即可以有多线线程同时读同一个资源，如果要使用写锁也只能有一个线程使用。zookeeper可以对分布式锁进行控制。
  - 命名服务  在分布式系统中，通过使用命名服务，客户端应用能够根据指定名字来获取资源或服务的地址，提供者等信息。

98. zookeeper有几种部署方式
zookeeper三种部署方式
    1. 单机部署    一台集群上运行
    2. 集群部署    多台集群运行
    3. 伪集群部署  一台集群启动多个zookeeper示例运行


99. zookeeper怎么保证主从节点状态同步
zookeeper 的核心是原子广播，这个机制保证了各个 server 之间的同步。实现这个机制的协议叫做 zab 协议。 zab 协议有两种模式，分别是恢复模式（选主）和广播模式（同步）。当服务启动或者在领导者崩溃后，zab 就进入了恢复模式，当领导者被选举出来，且大多数 server 完成了和 leader 的状态同步以后，恢复模式就结束了。状态同步保证了 leader 和 server 具有相同的系统状态。

100. 集群为什么要有主节点
在分布式环境中，有些业务逻辑只需要集群中的某一台机器进行执行，其他的机器可以共享这个结果，这样可以大大减少重复计算，提高性能，所以就需要主节点。

101. 集群中有3台服务器，其中一个节点宕机，这个时候zookeeper还可以使用吗?
可以使用，单数服务器只要没超过一半服务器宕机都可以使用

102. zookeeper的通知机制
客户端端会对某个 znode 建立一个 watcher 事件，当该 znode 发生变化时，这些客户端会收到 zookeeper 的通知，然后客户端可以根据 znode 变化来做出业务上的改变。

### Redis
103. redis是什么，有哪些使用场景
Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。
 - redis使用场景
  1. 数据高并发的读写
  2. 海量数据的读写
  3. 对扩展性要求高的数据


104. redis用那些功能
  - 数据缓存功能
  - 分布式锁的功能
  - 支持数据持久化
  - 支持事务
  - 支持消息队列

105. Redis和memencache的区别
 - memcached所有的值均是简单的字符串，redis作为其替代者，支持更为丰富的数据类型
 - redis的速度比memcached快很多
 - redis可以持久化其数据

106. Redis为什么是单线程
- 因为 cpu 不是 Redis 的瓶颈，Redis 的瓶颈最有可能是机器内存或者网络带宽。既然单线程容易实现，而且 cpu 又不会成为瓶颈，那就顺理成章地采用单线程的方案了。

- 关于 Redis 的性能，官方网站也有，普通笔记本轻松处理每秒几十万的请求。 而且单线程并不代表就慢 nginx 和 nodejs 也都是高性能单线程的代表。

107. 什么是缓存穿透，怎么解决
缓存穿透是指：查询一个一定不存在的数据，由于缓存是不命中的只能从数据库中查询，查不到数据则不写入缓存，这将导致每次查击透询数据直接到数据库查询，造成缓存击透
解决方案： 最简单粗暴的方法如果一个查询返回的数据为空（不管是数据不存在，还是系统故障），我们就把这个空结果进行缓存，但它的过期时间会很短，最长不超过五分钟。

108. Redis支持的数据类型有哪些
string、list、hash、set、zset。

109. redis支持的java客户端有哪些
Redisson、Jedis、lettuce等等，官方推荐使用Redisson。

110. jedis和redission有哪些区别
Jedis是Redis的Java实现的客户端，其API提供了比较全面的Redis命令的支持。
Redisson实现了分布式和可扩展的Java数据结构，和Jedis相比，功能较为简单，不支持字符串操作，不支持排序、事务、管道、分区等Redis特性。Redisson的宗旨是促进使用者对Redis的关注分离，从而让使用者能够将精力更集中地放在处理业务逻辑上。

111. 怎么保证缓存和数据库一致性
 - 合理设置缓存过期时间
 - 新增、修改、删除数据库操作时同步更新Redis，可使用事务机制保证一致性

112. Redis持久化方式
  - RDB（Redis Database）：指定的时间间隔能对你的数据进行快照存储。
  - AOF（Append Only File）：每一个收到的写命令都通过write函数追加到文件中。

113. Redis怎么实现分布式事务锁
- Redis 分布式锁其实就是在系统里面占一个“坑”，其他程序也要占“坑”的时候，占用成功了就可以继续执行，失败了就只能放弃或稍后重试。
- 占坑一般使用 setnx(set if not exists)指令，只允许被一个程序占有，使用完调用 del 释放锁。

114. Redis分布式锁有哪些缺陷
Redis 分布式锁不能解决超时的问题，分布式锁有一个超时时间，程序的执行如果超出了锁的超时时间就会出现问题。

115. redis如何做内存优化
 - 尽可能使用散列表（hashes），散列表（是说散列表里面存储的数少）使用的内存非常小，所以你应该尽可能的将你的数据模型抽象到一个散列表里面。
 - 比如你的web系统中有一个用户对象，不要为这个用户的名称，姓氏，邮箱，密码设置单独的key,而是应该把这个用户的所有信息存储到一张散列表里面。

116. redis淘汰策略有哪些
- volatile-lru：从已设置过期时间的数据集（server. db[i]. expires）中挑选最近最少使用的数据淘汰。
- volatile-ttl：从已设置过期时间的数据集（server. db[i]. expires）中挑选将要过期的数据淘汰。
-  volatile-random：从已设置过期时间的数据集（server. db[i]. expires）中任意选择数据淘汰。
- allkeys-lru：从数据集（server. db[i]. dict）中挑选最近最少使用的数据淘汰。
-  allkeys-random：从数据集（server. db[i]. dict）中任意选择数据淘汰。
-  no-enviction（驱逐）：禁止驱逐数据。

117. redis的常见性能问题有哪些？该如何解决
 - 主服务器写内存快照，会阻塞主线程的工作，当快照比较大时对性能影响是非常大的，会间断性暂停服务，所以主服务器最好不要写内存快照。
 - Redis 主从复制的性能问题，为了主从复制的速度和连接的稳定性，主从库最好在同一个局域网内。


