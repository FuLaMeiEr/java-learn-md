# SysBench Test
###  linux下 安装 mysql
[centos6.4安装mysql](https://blog.csdn.net/qq_37598011/article/details/93489404)

###  linux安装sysbench
[安装加性能测试](https://www.cnblogs.com/klb561/p/10513620.html)


### [测试日志]https://github.com/FuLaMeiEr/java-learn-md/blob/master/report/xls/mysysbench.log



### 性能测试 压力测试
  - 性能测试 ：通常收集所有和测试有关的所有性能，通常被不同人在不同场合下进行使用。 性能测试是一种“正常”测试，主要测试使用时系统是否满足要求，同时可能为了保留系统的扩展空间而进行的一些稍稍超过“正常”范围的测试（比如：当前系统使用用户100人，可能未来人数会增多到300人，所以要让系统能够在300人情况下正常运行）
  - 压力测试（Stress Test）：压力测试的目标是测试在一定的负载下系统长时间运行的稳定性，但是这个负载不一定是应用系统本身造成的。比如我们经常利用脚本或工具事先吃掉服务器的一部分cpu、内存或带宽等，创造出一定的负载环境并测试被测应用系统在此环境下的事物处理能力，响应时间等等。压力测试尤其关注大业务量情况下长时间运行系统性能的变化（例如是否反应变慢、是否会内存泄漏导致系统逐渐崩溃、是否能恢复）。



### TPS QPS
 - TPS：Transactions Per Second，意思是每秒事务数，一个事务是指一个客户机向服务器发送请求然后服务器做出反应的过程。客户机在发送请求时开始计时，收到服务器响应后结束计时，以此来计算使用的时间和完成的事务个数。
 每个事务包括了如下3个过程：
　　a. 用户请求服务器
　　b. 服务器自己的内部处理（包含应用服务器、数据库服务器等）
　　c. 服务器返回给用户
如果每秒能够完成N次这三个过程，tps就是N；有时，可能会将多个请求定义为一个事务，那么，完成一次这几个请求，就算一个tps。
 - QPS:  Queries Per Second，意思是每秒查询率，是一台服务器每秒能够响应的查询次数，是对一个特定的查询服务器（比如是读写分离的架构，就是读的服务器）在规定时间内所处理流量多少的衡量标准。


### 压测过程
1. 准备数据
```
sysbench ./tests/include/oltp_legacy/oltp.lua \
 --mysql-host=hwc1.kluster.xyz \
 --mysql-port=3306 \
 --mysql-user=root \
 --mysql-password=***** \
 --mysql-db=sbtest \
 --oltp-test-mode=complex \
 --oltp-tables-count=10 \
 --oltp-table-size=100000 
 --threads=10 \
 --time=120 \
 --report-interval=10 prepare
```


2.开始测试并输出日志
```
sysbench ./tests/include/oltp_legacy/oltp.lua \
--mysql-host=hwc1.kluster.xyz \
--mysql-port=3306 \
--mysql-user=root \
--mysql-password=******\
--mysql-db=sbtest \
--oltp-test-mode=complex \
--oltp-tables-count=10 \
--oltp-table-size=100000 \
--threads=10 \
--time=120 \
--report-interval=10 run >> /usr/local/sysbeanch/sysbench-1.0.19/mytest/mysysbench.log
```

3. 清除数据
```
sysbench ./tests/include/oltp_legacy/oltp.lua \
--mysql-host=hwc1.kluster.xyz \
--mysql-port=3306 \
--mysql-user=root \
--mysql-password=****** \
--mysql-db=sbtest \
-oltp-tables-count=9 cleanup
```

4. 压测报告

| 线程数 | 请求数 | 数据量(w) | qps(r/s) | tps(r/s) | 95%(ms) | min | avg | max | totaltime(s) |  |
|----|----|----|----|----|----|----|----|----|----|----|
| 10 |  | 100 |  |  | 1708.63 |  |  |  |  |  |
| 20 |  | 100 |  |  | 1803.63 |  |  |  |  |  |
| 10 |  | 200 | 144.79 | 7.24 | 2159 |  |  |  |  |  |
| 10 |  | 200 | 154.72 | 7.74 | 2009 |  |  |  | 122.988 |  |
| 20 |  | 200 | 304.55 | 15.22 | 2159 |  |  |  |  |  |
| 20 |  | 200 | 323.55 | 16.18 | 1836 |  |  |  | 121.2194 |  |
| 50 |  | 200 | 724.26 | 36.18 | 1938 |  |  |  | 121.3085 |  |
| 60 | 64840 | 200 | 534.27 | 26.71 | 3448 | 952.96 | 2230.96 | 10713.33 | 121.3593 |  |
| 80 | 69040 | 200 | 563.29 | 28.16 | 4280 | 1059.94 | 2807.41 | 12169.78 | 122.56443 |  |
| 100 | 66500 | 200 | 539.11 | 26.96 | 5709 | 1176 | 3660 | 38341 | 123.3486 |  |
| 100 |  | 200 | 683.56 | 34.18 | 4437 |  |  |  | 122.6212 |  |
| 120 | 77460 | 200 | 629 | 31.48 | 5813 | 1337.78 | 3763.61 | 20688.52 |  |  |
| 130 | 73820 | 200 | 578.7 | 28.94 | 6960 | 1331.48 | 4293.1 | 22252.68 | 127.56 |  |
| 140 | 69260 | 200 | 550.42 | 27.52 | 8484 | 1171 | 4935 | 57630 | 125.8301 |  |
| 150 |  | 200 |  |  |  |  |  |  |  | failed |
| 200 |  | 200 |  |  |  |  |  |  |  | failed |


结论： 200w数据 50个线程每秒查询数(qps)达到最大值

### linux查看数据库指标
1. 连接远程数据库 ./mysql -h hwc1.kluster.xyz -P 3306 -u root -p
2. 查看所有库 show databeses
3. 使用某一库 user  sbtest (sbtest)库名
4. 显示当前数据库使用引擎 show variables like ‘%storage_engine%’;
5. 查看数据库最大连接数 show global status like ‘max_used_connections’;
6. 我们设置的最大连接数 show variables like ‘max_connections%’






### 性能测试
1. 最大连接数

mysql> show global status like 'Max_used_connections';
+----------------------+-------+
| Variable_name        | Value |
+----------------------+-------+
| Max_used_connections | 152   |
+----------------------+-------+
1 row in set (0.06 sec)

mysql> show variables like 'max_connections%';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 151   |
+-----------------+-------+
1 row in set (0.05 sec)

通常max_connections的大小应该设置为比Max_used_connections状态值大，Max_used_connections状态值反映服务器连接在某个时间段是否有尖峰，如果该值大于max_connections值，代表客户端至少被拒绝了一次，可以简单地设置为符合以下条件：Max_used_connections/max_connections=0.8
2. 线程连接数命中数

mysql> show global status like 'threads_%';
+-------------------+-------+
| Variable_name     | Value |
+-------------------+-------+
| Threads_cached    | 5     |    //代表当前此时此刻线程缓存中有多少空闲线程
| Threads_connected | 6     |  //代表当前已建立连接的数量，因为一个连接就需要一个线程，所以也可以看成当前被使用的线程数
| Threads_created   | 2538  |  //代表从最近一次服务启动，已创建线程的数量
| Threads_running   | 1     |  //代表当前激活的（非睡眠状态）线程数
+-------------------+-------+
4 rows in set (0.15 sec)

mysql> show global status like 'connections';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| Connections   | 6908  |
+---------------+-------+
1 row in set (0.04 sec)

线程缓存命中率=1-Threads_created/Connections   = 36.7%

我们设置的线程缓存个数
mysql> show variables like '%thread_cache_size%';
+-------------------+-------+
| Variable_name     | Value |
+-------------------+-------+
| thread_cache_size | 9     |
+-------------------+-------+
1 row in set (0.04 sec)

根据Threads_connected可预估thread_cache_size值应该设置多大，一般来说250是一个不错的上限值，如果内存足够大，也可以设置成thread_cache_size值和threaads_connected值相同；

或者通过观察threads_created值，如果该值很大或一直在增长，可以适当增加thread_cache_size的值；在休眠状态下每个线程大概占用256KB左右的内存，所以当内存足够时，设置太小也不会节约太多内存，除非该值已经超过几千。

3. 缓存命中率

mysql> show global status like 'innodb_buffer_pool_read%';
+---------------------------------------+----------+
| Variable_name                         | Value    |
+---------------------------------------+----------+
| Innodb_buffer_pool_read_ahead_rnd     | 0        |
| Innodb_buffer_pool_read_ahead         | 42       |    //预读的页数
| Innodb_buffer_pool_read_ahead_evicted | 0        |
| Innodb_buffer_pool_read_requests      | 30626411 |   //从缓冲池中读取的次数
| Innodb_buffer_pool_reads              | 21520    |  //表示从物理磁盘读取的页数
+---------------------------------------+----------+
缓冲池命中率 = (Innodb_buffer_pool_read_requests)/(Innodb_buffer_pool_read_requests + Innodb_buffer_pool_read_ahead + Innodb_buffer_pool_reads)=99.92%

如果该值小于99.9%，建议就应该增大innodb_buffer_pool_size的值了，该值一般设置为内存总大小的75%-85%，或者计算出操作系统所需缓存+mysql每个连接所需的内存（例如排序缓冲和临时表）+MyISAM键缓存，剩下的内存都给innodb_buffer_pool_size，不过也不宜设置太大，会造成内存的频繁交换，预热和关闭时间长等问题。

4. 临时表使用情况

mysql> show global status like 'created_tmp%';
+-------------------------+-------+
| Variable_name           | Value |
+-------------------------+-------+
| Created_tmp_disk_tables | 212   |
| Created_tmp_files       | 37    |
| Created_tmp_tables      | 43414 |
+-------------------------+-------+
3 rows in set (0.04 sec)

mysql> show variables like '%tmp_table_size%';
+----------------+----------+
| Variable_name  | Value    |
+----------------+----------+
| tmp_table_size | 16777216 |
+----------------+----------+
1 row in set (0.04 sec)

可看到总共创建了43414 张临时表，其中有212张涉及到了磁盘IO，大概比例占到了0.004，证明数据库应用中排序，join语句涉及的数据量太大，需要优化SQL或者增大tmp_table_size的值，该比值应该控制在0.2以内。

5. binlog_cache使用
mysql> show status like 'binlog_cache%';
+-----------------------+-------+
| Variable_name         | Value |
+-----------------------+-------+
| Binlog_cache_disk_use | 2230  |
| Binlog_cache_use      | 44237 |
+-----------------------+-------+
2 rows in set (0.04 sec)

mysql> show variables like 'binlog_cache_size';
+-------------------+-------+
| Variable_name     | Value |
+-------------------+-------+
| binlog_cache_size | 32768 |
+-------------------+-------+
1 row in set (0.13 sec)

Binlog_cache_disk_use表示因为我们binlog_cache_size设计的内存不足导致缓存二进制日志用到了临时文件的次数
Binlog_cache_use  表示 用binlog_cache_size缓存的次数
当对应的Binlog_cache_disk_use 值比较大的时候 我们可以考虑适当的调高 binlog_cache_size 对应的值

6. Innodb log buffer size的大小设置

mysql> show variables like '%innodb_log_buffer_size%';
+------------------------+----------+
| Variable_name          | Value    |
+------------------------+----------+
| innodb_log_buffer_size | 16777216 |
+------------------------+----------+
1 row in set (0.05 sec)

mysql> show status like 'innodb_log_waits';
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| Innodb_log_waits | 0     |
+------------------+-------+
1 row in set (0.04 sec)

innodb_log_buffer_size设置了16M；Innodb_log_waits表示因log buffer不足导致等待的次数，如果该值不为0，可以适当增大innodb_log_buffer_size的值。

7. Innodb_buffer_pool_wait_free

mysql> show global status like 'Innodb_buffer_pool_wait_free';
+------------------------------+-------+
| Variable_name                | Value |
+------------------------------+-------+
| Innodb_buffer_pool_wait_free | 0     |
+------------------------------+-------+
1 row in set (0.04 sec)

该值不为0表示buffer pool没有空闲的空间了，可能原因是innodb_buffer_pool_size设置太大，可以适当减少该值。

8. 慢查询

mysql> show global status like 'Slow_queries';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| Slow_queries  | 0     |
+---------------+-------+
1 row in set (0.06 sec)

该值表示mysql启动以来的慢查询个数，即执行时间超过long_query_time的次数，可根据Slow_queries/uptime的比值判断单位时间内的慢查询个数，进而判断系统的性能。

9. 表锁信息

mysql>  show global  status like 'table_lock%';
+-----------------------+-------+
| Variable_name         | Value |
+-----------------------+-------+
| Table_locks_immediate | 1627  |
| Table_locks_waited    | 0     |
+-----------------------+-------+
2 rows in set (0.05 sec)

这两个值的比值：Table_locks_waited /Table_locks_immediate 趋向于0，如果值比较大则表示系统的锁阻塞情况比较严重
