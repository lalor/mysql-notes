# taobao的MySQL内核月报

淘宝有2份非常有价值的学习MySQL材料，分别是开源的patch和每个月的内核月报。

这篇文章是这些学习材料的学习笔记。

# patch

patch的地址是: <http://mysql.taobao.org/index.php?title=Patch_source_code>

# 内核月报

内核月报的地址是：<http://mysql.taobao.org/monthly/>


### MySQL内核月报2015-10

<http://mysql.taobao.org/monthly/2015/10/10/>

* MySQL · 引擎特性 · InnoDB 全文索引简介
* MySQL · 特性分析 · 跟踪Metadata lock
    介绍的并不详细，只是演示了MySQL 5.7中，可以通过performance schema来检索MDL锁阻塞情况，方便DBA来诊断问题
* MySQL · 答疑解惑 · 索引过滤性太差引起CPU飙高分析
    值得学习，索引过滤性很差的表现情况：语句hang住以及主机CPU耗尽。因此我们在设计表的时候，应该对业务上的数据有充分的估计，选择过滤性好的字段作为索引。**赖明星请注意：**应该学习这篇文章中，定位问题的方法。
* PgSQL · 特性分析 · PG主备流复制机制
* MySQL · 捉虫动态 · start slave crash 诊断分析
    该问题在最新的MySQL版本中依然存在，可以学习这里定位问题的思路。
* MySQL · 捉虫动态 · 删除索引导致表无法打开
* PgSQL · 特性分析 · PostgreSQL Aurora方案与DEMO
* TokuDB · 捉虫动态 · CREATE DATABASE 导致crash问题
* PgSQL · 特性分析 · pg_receivexlog工具解析

* MySQL · 特性分析 · MySQL权限存储与管理*
    介绍了MySQL权限管理的存储，包括物理存储位置和缓存中的存储位置，与此同时，介绍了更新权限的过程。

           以grant select on test.t1为例:
           1. 更新系统表mysql.user，mysql.db，mysql.tables_priv；
           2. 更新缓存acl_users，acl_dbs，column_priv_hash；
           3.  清空acl_cache。

* http://mysql.taobao.org/monthly/2015/05/

    - MySQL · 引擎特性 · InnoDB redo log漫游
    - MySQL · 专家投稿 · MySQL数据库SYS CPU高的可能性分析
    - MySQL · 捉虫动态 · 5.6 与 5.5 InnoDB 不兼容导致 crash
    - MySQL · 答疑解惑 · InnoDB 预读 VS Oracle 多块读
    - PgSQL · 社区动态 · 9.5 新功能BRIN索引
    - MySQL · 捉虫动态 · MySQL DDL BUG
    - MySQL · 答疑解惑 · set names 都做了什么
    - MySQL · 捉虫动态 · 临时表操作导致主备不一致
    - TokuDB · 引擎特性 · zstd压缩算法
    - MySQL · 答疑解惑 · binlog 位点刷新策略
