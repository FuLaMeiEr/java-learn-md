# SysBench Test
1. linux下 安装 mysql
[centos6.4安装mysql](https://blog.csdn.net/qq_37598011/article/details/93489404)

2. linux安装sysbench
[安装加性能测试](https://www.cnblogs.com/klb561/p/10513620.html)


3. [测试日志]https://github.com/FuLaMeiEr/java-learn-md/blob/master/report/xls/mysysbench.log
- 事务模式

并发数  | 数据总数  | qps  |  tps  |  总响应时间  | 前5%最大响应时间
---- | ----- | ------ | ------- |  --------- | --------
10  | 100w | 18020 |  901       | 1206452ms  |  1708.63ms
20  | 100w | 39700 |   1985       | 2406558ms |  1803.63ms




- 准备数据
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


- 开始测试并输出日志
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

- 清除数据
```
sysbench ./tests/include/oltp_legacy/oltp.lua \
--mysql-host=hwc1.kluster.xyz \
--mysql-port=3306 \
--mysql-user=root \
--mysql-password=****** \
--mysql-db=sbtest \
-oltp-tables-count=9 cleanup
```

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

