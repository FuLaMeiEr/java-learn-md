
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

62. 

