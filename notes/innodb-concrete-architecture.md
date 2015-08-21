# innodb的架构

阅读论文<[innodb concrete architecture](http://ece.ut.ac.ir/dbrg/seminars/AdvancedDB/Fall%202008/hashemi/Project_MySQL_Benchmark/References/InnoDB%20Concrete%20Architecture.pdf)>的笔记。

 各个目录的作用：

* pars 解析SQL
* eval 执行SQL
* lock 锁
* trx   事务
* log  日志
* ha hash table
* dyn 动态数组
* ut 辅助工具
* fil 文件管理
* fsp 表空间管理
* buf 缓冲区
* row 资源管理
