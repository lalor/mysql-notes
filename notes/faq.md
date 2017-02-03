# MySQL数据库相关问题（可以作为面试题目）

## Innodb层相关的问题

1. redo undo 内存也 外存页 之间的修改顺序
1. redo如何rotate
1. 热备原理<http://mysql.taobao.org/monthly/2016/03/07/>
1. binlog与redo的区别
1. 几个不同的LSN分别是什么意思
1. 物理日志相对于物理逻辑日志，有什么优势
1. mini-trasaction的作用
1. 一条插入操作会写入哪些redo
1. 区与段的概念
1. 为什么有时候会出现不使用主键，比使用主键占用空间要大？默认主键是6个字节
1. 行记录的2个影藏列 回滚指针和记录事务的ID列
1. 回滚指针占用几个字节，为什么？7个字节
1. innodb如何在一个page中找到记录 slot的组织？
1. 如果有两个线程同时发起对同一个页面的读操作，这个页面又不在内存中，怎么处理
1. 事务还没有提交，但是修改了数据页，这时候数据页是脏页，进行checkpoint的时候遍历到这一页，要不要刷出磁盘
1. 缓冲区包含哪些信息？
1. 刷新时，其他线程是否可以访问该页
1. double write和redo之间的关系
1. lock和latch的区别
1. 自增锁如何优化
1. six锁的优势是什么
1. innodb有哪几种锁模式
1. innodb如何解决死锁？锁超时和死锁检测
1. 自适应hash索引是什么？是否持久化索引？如何修改可以持久化自适应hash索引
1. 怎么表示一个事务提交了？
1. 故障恢复的时候要不要写redo
1. 编写脚本查看b+的填充因子
1. 恢复时，redo和undo的顺序
1. undo重用是什么？什么情况下会进行undo重用
1. 有几种undo？为什么需要区分undo的类型
1. 事务系统段的位置是？(0, 5)，包含了哪些信息？(包含了4部分信息，事务、回滚段、double write、binlog)
1. 解释一下double write，什么时候可以关闭double write
1. trx_t包含哪些信息？
1. Innodb的B+树向左分裂还是向右分裂
1. Innodb的buffer pool包含几个链表？
1. read_view是啥？如何实现快照读

## Server层相关的问题

1. 简述MySQL的整体架构
2. MySQL Server包含几种日志 4种
3. Binlog的格式？
4. Binlog的Event组成？Binog为row格式，插入一条记录，会记录多少Event？
5. 简述group commit的实现
6. 简述线程池的原理和实现
7. MySQL如何解决内存碎片问题
8. MySQL如何提高读写效率？
