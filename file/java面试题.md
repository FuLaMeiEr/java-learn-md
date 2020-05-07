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

 



