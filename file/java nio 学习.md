## java nio
### 什么是NIO
+ 是为所有的原始类型（boolean类型除外）提供缓存支持的数据容器，使用它可以提供非阻塞式的高伸缩性网络。 
### NIO于BIO的区别
+ BIO是阻塞IO
+ NIO是非阻塞IO
+ 共同点：两者都是同步操作，必须先进行IO操作才能进行下一步操作
+ 不同点：BIO多线程对某资源进行IO操作时会出现阻塞，即一个线程进行IO操作完才会通知另外的IO操作线程，必须等待。<br>
    NIO多线程对某资源进行IO操作时会把资源先操作至内存缓冲区。然后询问是否IO操作就绪，是则进行IO操作，否则进行下一步操作，然后不断的轮询是否IO操作就绪，直到iIO操作就绪后进行相关操作。
### NIO是怎么工作的
+ 调用Selector的静态工厂创建一个选择器，创建一个服务端的Channel，绑定到一个Socket对象，并把这个通信信道注册到选择器上，把这个通信信道设置为非阻塞模式。然后就可以调用Selector的selectedKeys方法来检查已经注册在这个选择器上的所有通信信道是否有需要的事件发生，如果有某个事件发生，将会返回所有的selectedKeys，通过这个对象的Channel方法就可以取得这个通信信道对象，从而读取通信的数据，而这里读取的数据是Buffer，这个Buffer是我们可以控制的缓冲器。
### NIO组成
+ channle
channle(通道) Channle和IO中的Stream是差不多一个等级的。 不过Stream是单向的。例如：inputStream、outputStream。而channle是双向的，即可用来读，也可用来写。
nio中常用的channle的实现:
   1. FileChannle 文件IO
   2. datagromChannle UDP
   3. SocketChannle TCP client
   4. ServerSocketChannle   TCP server
+ buffer
  * buffer(容器) 缓冲区，实际上是一个容器，一个连续数组。channle提供从文件、网络读取数据的渠道，但读写数据都得通过buffer。
  * buffer的属性：
     1. capacity: 容量
        buffer中元素的个数，不为负数，不能变化
     2. limit：限制
       是buffer所维护数组中的一个下标
     3. position：定位  
       是数组中下一个将要被读或者被写的下标
  * buffer的使用
     1. 分配空间 ByteBuffer buf = ByteBuffer.allocate(1024)
     2. 写入数据到Buffer(int bytesRead = fileChannel.read(buf);
     3. 调用filp()方法 buf.filp();
     4. 从buffer读取数据 (char)buf.get();
     5. 掉用clear()方法
+ selector
  - Selector运行单线程处理多个Channel，如果你的应用打开了多个通道，但每个连接的流量都很低，使用Selector就会很方便。例如在一个聊天服务器中。要使用Selector, 得向Selector注册Channel，然后调用它的select()方法。这个方法会一直阻塞到某个注册的通道有事件就绪。一旦这个方法返回，线程就可以处理这些事件，事件的例子有如新的连接进来、数据接收等。
