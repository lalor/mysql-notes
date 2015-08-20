# 使用MySQL自身复制来恢复binlog

<http://www.orczhou.com/index.php/2013/11/use-mysql-replication-to-recove-binlog/>

### 使用mysqlbinlog会有什么问题

在MySQL手册中一直是推荐使用mysqlbinlog工具来实现指定时间点的数据恢复，事实上，这是一个经常"让人郁闷"的办法。更好的办法是，使用MySQL内部复制线程中的SQL Thread来做恢复。

这个idea来自Lazydba同学；在Google稍作搜索，在Xaprb上Baron Schwartz也很早提到了使用类似的方法来恢复binlog，在那篇讨论中，还可以看到Jeremy Cole也提到：使用MySQL手册中推荐的方法是困难重重的，而且mysqlbinlog这个办法从逻辑上来说也是一个错误--因为这样MySQL不得不在两个不同的地方实现一套相同的逻辑，最终难免会出错。使用mysqlbinlog来恢复，你可能会需要以下“让人郁闷”的问题：

    (*) Max_allowed_packet问题
    (*) 恼人的Blob/Binary/text字段问题
    (*) 特殊字符的转义问题
    (*) 没有"断点恢复"：执行出错后，没有足够的报错，也很难从失败的地方继续恢复


### 如果使用MySQL自身复制来恢复binlog

优点：实施简单；缺点：需要关闭一次数据库(不确定不关闭数据库行不行)；

思路：直接将要恢复的binlog拷贝到relay log目录，并修改slave-info相关的文件，让MySQL把binlog当做relay log来执行

简单的操作步骤：

* 关闭当前实例
* 将binlog拷贝到对应的relay log目录(datadir或者relay-log参数指定的目录)
* 打开relay-log-info-file参数指定的relay-log.info文件(默认是datadir目录下的relay-log.info文件)，修改文件前面两行。
这两行的意义分别是：当前执行的relay log文件；当前执行到relay log文件的位置(position)
* 打开relay-log-index文件(由参数--relay-log-index，默认是数据目录下的host_name-relay-bin.index)将需要恢复的binlog文件全路径列表存在该文件中
* 启动数据库，并start slave io_thread
