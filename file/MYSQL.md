# Mysql

## mysql压测

### 库：
hwc1.kluster.xyz
hwc2.kluster.xyz
hwc3.kluster.xyz



### MysqlSlap测试
[mysqlslap使用教程](https://my.oschina.net/moooofly/blog/152547)

#### 测试流程
1.  建立测试数据库database和待测试的表tables
2.  根据table的结构，利用脚本生成一定数量的有效随机数据
3.  利用mysqlslap对相应query语句进行测试
4.  结果数据的分析


### jmeter测试


hwc1 - hwc3 
 |线程数 | 异常率|
 |-|-|
 |150|0%|
 |200|24%-25%|
 |300|50%|


## mysql相关知识
### MySql 日志

[https://www.cnblogs.com/f-ck-need-u/p/9001061.html](https://www.cnblogs.com/f-ck-need-u/p/9001061.html#auto_id_2)

  1. 查询日志
  
  查询日志分为一般查询日志和慢查询日志，通过查询变量long_query_time的值进行判断，在超时时间内完成的查询是一般查询，默认是关闭的（建议关闭）。
  
  general_log = {0|1} 开启或关闭查询日志
  
  general_log_file = file_name 指定文件查询日志路径 默认hostname.err
  
  log_output = {TABLE|FILE|NONE}   定义一般查询日志和慢查询日志的输出格式，不指定时默认为file
  
  2. 慢查询日志
  
  log_slow_queries = {yes|no}    #是否启用慢查询日志，默认不启用
  
  slow_query_log = {1|ON|0|OFF}   也是是否启用慢查询日志，此变量和log_slow_queries修改一个另一个同时变化
  
  slow_query_log_file=/mydata/data/hostname-slow.log  默认路径为库文件目录下主机名加上-slow.log
  
  3. 错误日志
  
  记录MeriaDB/mysql 服务启动和停止正确和错误的信息，还记录mysql实例运行过程中的发生错误的时间信息
  
  log_error = filename 指定错误日志保存文件 默认的错误文件日志datadir下面的hostname.err 文件
  
  4. 二进制日志
  
  记录引起或者可能引起数据库改变的事件信息。语句以事件形式保存，包括时间，事件开始位置，结束位置等信息
  
  --log-bin = [on|off|file_name] 启用二进制日志
  
  mysqlbinlog --start-datatime"" --stop-datatime 查看某一时间段的日志
  
  show binlog events 查看日志进行了那些操作
  
  reset master 删除二进制日志
  
  PURGE { BINARY | MASTER } LOGS { TO 'log_name' | BEFORE datetime_expr } 删除指定时间之前的日志
  
  二进制日志记录格式：
  
  binlog_format:指定那种形式记录
  
  statement 所有操作记录为sql形式记录，缺点动态数据无法同步
  
  row 相关行的每一列操作记录记录下去缺点日志文件较大
  
  mixed 记录日志有mysql决定
  
  5. 中继日志
  
  将主服务的二进制日志复制到到从服务器的本地文件中，然后sql线程读取relay-log日志到从服务器，从而使主服务器和从服务器保持一致

### MySql事务
1. 事务的四大特性：

原子性: 事务内的操作要吗全部成功要么全部失败

一致性: 事务操作前后，数据的完整性处于一致性状态

持久性: 事务一旦提交，那么对数据库数据的修改就是永久的

隔离性: 多个用户并发访问数据库时，其中一个事务的操作不会影响到另一个事务的操作。

2. 事务隔离级别：

serializable:串行化 避免脏读、幻读、不可重复读

repeatable read（mysql默认事务隔离级别）:可重复读 避免 脏读、 幻读

read commited : 读已提交 避免 脏读

read uncomnited: 读未提交 什么都避免不了

事务并发处理可能会发生的问题：

脏读：一个事务读取了另一个事务修改了但并未提交的数据

不可重复读: 一个事务读取了另一个事务修改并提交的数据

幻读: 一个事务两次读取的结果不同

3. 事务传播特性：

  * 保证同一个事务中
 
  PROPAGATION_REQUIRED 支持当前事务，如果不存在 就新建一个(默认)
  
  PROPAGATION_SUPPORTS 支持当前事务，如果不存在，就不使用事务
  
  PROPAGATION_MANDATORY 支持当前事务，如果不存在，抛出异常
  
  * 保证没有在同一个事务中

  PROPAGATION_REQUIRES_NEW 如果有事务存在，挂起当前事务，创建一个新的事务
  
  PROPAGATION_NOT_SUPPORTED 以非事务方式运行，如果有事务存在，挂起当前事务
  
  PROPAGATION_NEVER 以非事务方式运行，如果有事务存在，抛出异常
  
  PROPAGATION_NESTED 如果当前事务存在，则嵌套事务执行

### Mysql索引

1. mysql索引的4种类型

逻辑索引:

普通索引：

基本数据库索引，没有任何限制

create index indexname on mytable(username)

drop index indexname on mytable

唯一索引:

索引列的值必须为唯一，允许有null值，如果是组合索引，则列值的组合必须唯一

create unique index index on mytble(username)

主键索引：

特殊索引，一个表只能有一个主键，不允许有null值，一般建表时创建

组合索引：

create index a_b_c on mytable(a,b,c)

减少开销: 假如对a、b、c创建组合索引，相当于创建了a,b,c3个索引

覆盖索引: 假设select a ,b ,c 3个字段，由于查询字段在索引页中，可以从索引页中直接获取，不需要回表查询

效率高: 对于a,b,c三列分别创建索引，mysql进行查询时只会选择辨识度高的作为索引。假设100w条数据，一个索引100*10%=10w的数据，组合索引100*10%*10%*10%=0.1 1000条数据

物理索引:

聚集索引:

该索引中键值的逻辑顺序决定表中相应行的物理顺序，由于聚集索引规定数据在表中的物理存储顺序，因此一个表只能包含一个聚集索引，对经常搜索的范围列比较有效

创建聚集索引：mysql中默认主键就是聚集索引

聚集索引结构：索引的叶子节点就相当于数据节点

![聚集索引](F:\Note\qqCF470C119BD3A5BA535BEC5DEC86B157\5fae393f17cb4ed4bfbfeb05494e2971\clipboard.png)

非聚集索引：

非聚集索引则就是普通索引了，仅仅只是对数据列创建相应的索引，不影响整个表的物理存储顺序。

非聚集索引的叶子节点的还是索引节点

![非聚集索引](F:\Note\qqCF470C119BD3A5BA535BEC5DEC86B157\e2b25717c37647459b7857a1cb10266c\clipboard.png)

2. 索引的优缺点及使用场景:

  + 索引的创建:

    - 建在经常查询的列上，提高查询速度
    
    - 建在主键列上 强制该列的唯一性和组织表中数据的排列结构
    
    - 建在外键列上，提高连接查询速度
    
    - 建在查询范围的列上(分组列)，建好索引后默认排好序(升序)，指定范围是连续的
    
    -  排序列
    
    - 条件列

  + 索引的优点：

    - 通过创建唯一索引，保证数据库表每一行数据的唯一性
  
    - 大大加快数据的检索效率
    
    - 加速表与表之间的连接
    
    - 分组排序列进行检索是时，显著减少检索时间
    
    - 通过使用索引，可以在查询的过程中，使用优化隐藏器，提高系统的性能。

  + 索引的缺点：

    - 创建索引和维护索引需要时间，这种时间随数据量增加而增加
    
    - 索引占用物理空间，除了数据表占数据空间之外，每一个索引还要占一定的物理空间，如果要建立聚集索引那么需要的空间就会更大。
    
    - 对数据表数据进行增删改时，索引需要动态维护，降低动态维护数据的速度

### MySql数据库引擎

[https://www.jb51.net/article/38004.htm](https://www.jb51.net/article/38004.htm)

1. 数据库引擎类型：

    - MyISAM: 不支持事务和行级锁。所以查询速度很快，性能优秀。可以对整张表加锁，支持并发插入，支持全文索引
    
    - InnoDB: 支持事务，行级锁，以及外键，拥有高并发处理能力。但是在创建索引和加载数据时，比MyISAM慢。(MySql默认数据库引擎)
    
    - MEMORY:支持Hash索引，内存表，Memory引擎将数据存储在内存中，表结构不是存储在内存中的，查询时不需要执行磁盘I/O操作，所以要比MyISAM和InnoDB快很多倍，但是数据库断电或是重启后，表中的数据将会丢失，表结构不会丢失。

2. 查看数据库支持的引擎:

    - 查看mysql已提供哪些引擎
    
        mysql> show engines
    
    - 看你的mysql当前默认的引擎:
    
          mysql> show variables like '%storage_engines%'
    
    - 你要看某个表用了什么引擎:
    
          mysql> show create table 表名;
    
    - 切换数据库引擎:
    
          create table table{} engine = innodb
    
    - 2.修改my.ini文件
    
          default-storage-engine = innodb
    
          alert tablename engine = innodb

### 数据库表设计原则:

1. 数据库表设计三大范式:

[三大范式](https://blog.csdn.net/u014458048/article/details/56678698)

  - 第一范式:列不可再分
  
  所用表的字段都是不可再分，例如字段属性‘地址’，如果经常访问的话就需要拆成‘省’，‘城市’，‘县’。
  
  - 第二范式:每列和主键相关 主键关系
  
  确保数据库中的每一列都和主键相关而不是一部分相关
  
  例如：商品表 订单表 订单表中包含有商品数据，这样设计不符合第二范式，需要再加一个联合主键表 商品_订单表 多对多关系 需要三张表
  
  一个表只保存一种类型的数据，不可以把多种数据保存到一张表中
  
  - 第三范式:每列和主键有直接关系 外键关系
  
  确保数据库表每一列与主键直接相关而不是间接相关
  
  例如：用户 订单 订单表加外键对应用户 一对多关系

### MySql函数类型
1. 聚合函数
   - count
      * 返回指定字段数据的行数
      * select count(---) from tablename 函数忽略值为null的行
      * select count(*) from tablename 函数包含值为null的行
   - sum
      * 返回指定字段的数据之和
      * sum()函数忽略为null的列值
      * select sum(---) from tablename 
   - avg
      * 返回指定数据的平均值
      * avg()函数忽略值为null的行
      * select avg(---) from tablename
   - max min
      * 返回指定字段的最大值、最小值
      * select max(---) from tablenename 函数忽略值为null的行
      * 字段若为字符串，先按字符串比较，然后返回值
2. 用于处理字符串的函数    
   - concat(str1,str2,str3)
      * 将多个字符串合并成一个字符串
      * 传有null值返回null值
   - strcmp(str1,str2)
      * 用于比较两个字符串的大小。左大于右时返回1，左等于右时返回0，，左小于于右时返回-1，
      * select strcmp("a","b")
   - length(str)
      * 获取数据的字节长度
   - char_length(str)
      * 获取数据的字符长度
   - 字符大小写转换
      * select upper("abc") 数据可以使单个字母或者字符串
      * select lower("AGB") 数据可以使单个字母或者字符串
   - 字符串查找函数
      * locate(str,str1) 返回str在str1的位置
      * inser(str2,str1) 返回str1在str1的位置
      * field(str,str1,str2,str3) 返回str在str1,str2,str3的位置
   - 获取指定位置的字符串
      * elt(index,str1,str2,str3) 返回指定位置的字符串
      * left(index,len) 截取字符串左边的len个字符
      * right(index,len) 截取字符串右边边的len个字符
      * substring(str,index,len) 截取str从index位置往后len的字符
   - 字符串去空函数
      * ltrim(str) 去除字符串左边的空格
      * rtrim(str) 去除字符串左边的空格
      * trim(str) 去除字符串两边的空格
   - 字符串替换函数
      * insert(str,index,len,str1) str1替换字符串str从index开始len个长度位置
      * replace(str,str1) str1替换str
3. 用于处理数值的函数
   - 绝对值函数abs(x)
      * 返回x的绝对值
   - 向上取整函数 ceil(x)
      * 返回x向上取整数的值
   - 向下取整函数 floor(x)
      * 返回x向下取整数的值
   - 取模函数 mod(x,y)
      * 返回x mod y的值
   - 随机数函数 rand()
      * 返回0-1内的随机数
   - 四舍五入函数 round(x,y)
      * 返回x 带有 y位小数的值（四舍五入）
   - 数值截取函数 truncate(x,y)
      * 返回x 截取 y位小数的值（不四舍五入）
4. 用于处理时间的函数
   - 返回当前日期 curdate()
   - 返回当前时间 curtime()
   - 获取当前日期时间 now()
   - 从日期中选择月份,周,年 moth(date),mothname(date),week(date)，year(date)
   - 从时间中选择时，分 hour(time), minute(time)
   - 从时间中选择今天是周几 weekday(date),dayname(date)

