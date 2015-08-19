# SHOW STATUS

可以在MySQL客户端运行`show status`查看，也可以通过mysqladmin查看这些变量。


    show status like '%Select*'
    mysqladmin extended-status

查看增量变化：

    mysqladmin extended -r -i60 | grep Handler_

还可以使用percona公司的innotop工具。


还可以使用SQL语句：



### 线程和连接统计

* Connections, Max_used_connections, Threads_connected
* Aborted_clients, Aborted_connects
* Bytes_received, Bytes_sent
* Slow_launch_threads, Threads_cached, Threads_created, Threading_running

### 二进制日志相关

* Binlog_cache_use
* Binlog_cache_disk_use
* Binlog_stmt_cache_use
* Binlog_stmt_cache_disk_use

### 命令计数器

* `Com_*`
* Questions



### 临时表和文件

* `Created_tmp%`


### 句柄操作

* `Handler_%`


### 查询缓存

* `Qcache_%`

### SELECT 类型

* `Select_*`

### 排序

* `Sort_%`

### 表锁

* `Table_locks_immediate`
* `Table_locks_waited`


