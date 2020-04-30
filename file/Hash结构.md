# Hash结构


### Hash结构

散列技术是指在记录的存储位置和它的关键字之间建立一个确定的对应关系f，使每一个关键字都对应一个存储位置。即：存储位置=f（关键字）。这样，在查找的过程中，只需要通过这个对应关系f 找到给定值key的映射f（key）。只要集合中存在关键字和key相等的记录，则必在存储位置f（key）处。我们把这种对应关系f 称为散列函数或哈希函数。

####  hashtable:
底层数组+链表实现，无论key还是value都不能为null，线程安全，实现线程安全的方式是 在修改数据时锁住整个HashTable，效率低，
初始size为11，扩容：newsize = olesize*2+1

#### hashmap:
1. hashmap结构：
底层数组+链表实现，可以存储null键和null值，线程不安全
初始size为16，扩容：newsize = oldsize*2，size一定为2的n次幂
扩容针对整个Map，每次扩容时，原来数组中的元素依次重新计算存放位置，并重新插入
插入元素后才判断该不该扩容，有可能无效扩容（插入后如果扩容，如果没有再次插入，就会产生无效扩容）

  - hashmap线程不安全的原因：
     + 多个线程同时使用put方法添加元素
     假设正好存在两个put的key发生了hash碰撞，那么根据hashmap的实现原理，最终会有一个线程的put的数据被覆盖
  
      +  多个线程同时检测到元素个数超过数组大小*加载因子
多个线程同时对hashmap进行扩容，都在重新计算元素位置及复制数据，但最终只会有一个线程扩容后的数据赋给table，其他线程的都会丢失，并且各自线程的put数据也会丢失。且会引起死循环的错误

  - 解决方法
     + 直接使用hashtable,但是当一个线程访问HashTable的同步方法时，其他线程如果也要访问同步方法，会被阻塞住

    + 通过 Collections.synchronizeMap(hashMap);
HashMap可以通过Map m = Collections.synchronizedMap(new HashMap())来达到同步的效果。

    + 使用ConcurrentHashmap

2. hashMap存取实现

![hashMap存取实现](https://raw.githubusercontent.com/FuLaMeiEr/java-learn-md/master/report/picture/wps1.jpeg
)


#### ConcurrentHashMap:
1. concurrenthashmap结构
 - 底层采用分段的数组+链表实现，线程安全
 - 通过把整个Map分为N个Segment，可以提供相同的线程安全，但是效率提升N倍，默认提升16倍。(读操作不加锁，由于HashEntry的value变量是 volatile的，也能保证读取到最新的值。)
 - Hashtable的synchronized是针对整张Hash表的，即每次锁住整张表让线程独占，ConcurrentHashMap允许多个修改操作并发进行，其关键在于使用了锁分离技术有些方法需要跨段，比如size()和containsValue()，它们可能需要锁定整个表而而不仅仅是某个段，这需要按顺序锁定所有段，操作完毕后，又按顺序释放所有段的锁

 - 扩容：段内扩容（段内元素超过该段对应Entry数组长度的75%触发扩容，不会对整个Map进行扩容），插入前检测需不需要扩容，有效避免无效扩容

2. hash冲突：
 - 什么是hash冲突
在理想的情况下，每一个 关键字，通过哈希函数计算出来的地址都是不一样的。但是在实际情况中，我们常常会碰到两个关键字key1≠key2,但是f(key1) = f(key2), 这种现象称为冲突，并把key1和key2称为这个散列函数的同义词。

3. 怎么解决hash冲突

 - 开放地址法
当关键字key的哈希地址p=H（key）出现冲突时，以p为基础，产生另一个哈希地址p1，如果p1仍然冲突，再以p为基础，产生另一个哈希地址p2，…，直到找出一个不冲突的哈希地址pi ，将相应元素存入其中。

 - 链地址法
这种方法的基本思想是将所有哈希地址为i的元素构成一个称为同义词链的单链表，并将单链表的头指针存在哈希表的第i个单元中，因而查找、插入和删除主要在同义词链中进行。由于链表的特点是：寻址困难，插入和删除容易，链地址法适用于经常进行插入和删除的情况。

 - 公共溢出区法
将哈希表分为基本表和溢出表两部分，凡是和基本表发生冲突的元素，一律填入溢出表

 - 这种方法是同时构造多个不同的哈希函数：
Hi=RHi（key）,i=1，2，…，k
当哈希地址Hi=RH1（key）Hi=RH1（key）发生冲突时，再计算Hi=RH2（key）Hi=RH2（key）……，直到冲突不再产生。这种方法不易产生聚集，但增加了计算时间。

#### HashSet()的实现

1. HashSet实际上是一个HashMap实例，都是一个存放链表的数组。它不保证存储元素的迭代顺序；此类允许使用null元素。HashSet中不允许有重复元素，这是因为HashSet是基于HashMap实现的，HashSet中的元素都存放在HashMap的key上面，而value中的值都是统一的一个固定对象private static final Object PRESENT = new Object();

2. HashSet中add方法调用的是底层HashMap中的put()方法，而如果是在HashMap中调用put，首先会判断key是否存在，如果key存在则修改value值，如果key不存在这插入这个key-value。而在set中，因为value值没有用，也就不存在修改value值的说法，因此往HashSet中添加元素，首先判断元素（也就是key）是否存在，如果不存在这插入，如果存在着不插入，这样HashSet中就不存在重复值。

 3. 所以判断key是否存在就要重写元素的类的equals()和hashCode()方法，当向Set中添加对象时，首先调用此对象所在类的hashCode()方法，计算次对象的哈希值，此哈希值决定了此对象在Set中存放的位置；若此位置没有被存储对象则直接存储，若已有对象则通过对象所在类的equals()比较两个对象是否相同，相同则不能被添加。

4. hashMap与hashSet的比较

![hashMap与hashSet的比较](https://raw.githubusercontent.com/FuLaMeiEr/java-learn-md/master/report/picture/wps2.jpeg
)
