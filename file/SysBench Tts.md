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

<<<<<<< HEAD
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


### 性能测试
### 测试结论及优化建议
=======
4. 测试报告

![add image](https://raw.githubusercontent.com/FuLaMeiEr/java-learn-md/master/report/picture/Test1111.png)

queries: 查询总数及qps

transactions: 事务总数及tps


5. TPS QPS

 - TPS：Transactions Per Second，意思是每秒事务数，一个事务是指一个客户机向服务器发送请求然后服务器做出反应的过程。客户机在发送请求时开始计时，收到服务器响应后结束计时，以此来计算使用的时间和完成的事务个数。
 每个事务包括了如下3个过程：
　　a. 用户请求服务器
　　b. 服务器自己的内部处理（包含应用服务器、数据库服务器等）
　　c. 服务器返回给用户
如果每秒能够完成N次这三个过程，tps就是N；有时，可能会将多个请求定义为一个事务，那么，完成一次这几个请求，就算一个tps。
 - QPS:  Queries Per Second，意思是每秒查询率，是一台服务器每秒能够响应的查询次数，是对一个特定的查询服务器（比如是读写分离的架构，就是读的服务器）在规定时间内所处理流量多少的衡量标准。

>>>>>>> 98f6f985605a6688c21477aafcdffd986863362e
