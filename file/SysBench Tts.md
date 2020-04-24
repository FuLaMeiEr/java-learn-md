# SysBench Test
linux下 安装 mysql
[centos6.4安装mysql](https://blog.csdn.net/qq_37598011/article/details/93489404)

linux安装sysbench
[安装加性能测试](https://www.cnblogs.com/klb561/p/10513620.html)

事务模式

并发10  100w条数据
qps 18020
tps 901
总响应时间 1206452ms
前95%的最大时间 1708.63ms


并发20 100w数据
qps 39700
tps 1985
总响应时间 2406558ms
前95%的最大响应时间 1803.63ms



[测试日志]https://github.com/FuLaMeiEr/java-learn-md/blob/master/report/xls/mysysbench.log



准备数据
sysbench ./tests/include/oltp_legacy/oltp.lua --mysql-host=hwc1.kluster.xyz --mysql-port=3306 --mysql-user=root --mysql-password=*****--mysql-db=sbtest --oltp-test-mode=complex --oltp-tables-count=10 --oltp-table-size=100000 --threads=10 --time=120 --report-interval=10 prepare

开始测试并输出日志
sysbench ./tests/include/oltp_legacy/oltp.lua --mysql-host=hwc1.kluster.xyz --mysql-port=3306 --mysql-user=root --mysql-password=******--mysql-db=sbtest --oltp-test-mode=complex --oltp-tables-count=10 --oltp-table-size=100000 --threads=10 --time=120 --report-interval=10 run >> /usr/local/sysbeanch/sysbench-1.0.19/mytest/mysysbench.log


清除数据
sysbench ./tests/include/oltp_legacy/oltp.lua --mysql-host=hwc1.kluster.xyz --mysql-port=3306 --mysql-user=root --mysql-password=****** --mysql-db=sbtest -oltp-tables-count=9 cleanup

