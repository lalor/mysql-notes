# MySQL查询优化

- 查询性能衡量标准
- 影响查询性能的因素
- 查询计划分析
- 查询优化技巧

# 1. 查询性能衡量标准
* 查询时间
* 慢查询（几乎所有到达秒级别的查询都可以认为比较慢）
* 执行计划
    - type 查询的方式
    - key 使用的索引
    - Rows 结果集大小
    - Extra 提示信息

# 2. 影响查询性能的因素，查询优化技巧
* SQL解析时间  # 使用prepared stmt语句减少重复SQL的解析
* 查询优化算法 
    - 执行计划分析<http://dev.mysql.com/doc/refman/5.6/en/explain-output.html>
    - 索引优化
        - [表优化]建主键(unsigned int)
        - [表优化]字段尽量使用NOT NULL
        - [表优化]能使用enum的尽量不要使用varchar
        - [表优化]ip字段使用unsigned int 并使用INET_NTOA和INET_ATON
        - [索引优化]为频繁搜索的字段建立索引
        - [索引优化]为varchar text建立全文索引，避免使用like
        - [索引优化]避免使用blob字段，该字段只能建立前缀索引
        - [索引优化]最多匹配原则和最高区分度原则

    - 查询优化
        - 索引字段不参与计算，否则索引失效
        - 避免使用`select *`, select count(*)等
        - 知道结果数量的时候，使用limit，尽早结果过程
* Query Cache的使用 # 关了吧
* 磁盘IO次数 # IO优化，增大Buffer pool，开启MRR，避免select *
* 事务键锁的影响 # 锁优化： 避免使用大事务，RC比RR好，不使用GAP锁，避免使用select *，可能会锁全表
* Join优化 # 利用index nested-loop join算法，在没有索引的情况下，合理设置join_buffer_size
* 走索引OR全表扫描
* 结果集大小与运算过程
