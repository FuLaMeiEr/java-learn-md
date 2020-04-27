# SysBench Test
1. linux下 安装 mysql
[centos6.4安装mysql](https://blog.csdn.net/qq_37598011/article/details/93489404)

2. linux安装sysbench
[安装加性能测试](https://www.cnblogs.com/klb561/p/10513620.html)


3. [测试日志]https://github.com/FuLaMeiEr/java-learn-md/blob/master/report/xls/mysysbench.log
- 事务模式

|并发数|数据总数|qps|tps|总响应时间|前5%最大响应时间|
|-|-|-|-|-|-|-|
|10|100w |18020 |901 | 1206452ms| 1708.63ms|
|20|100w |39700|1985| 2406558ms| 1803.63ms|


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


