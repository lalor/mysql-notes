# What's new in MySQL 5.7

- [MySQL 5.7 key features][1]

    - Abstract: 介绍了MySQL 5.7中的关键特性
    - Content
        * 复制优化
            - 多源复制
            - show slave status不会被阻塞
            - show slave status的信息可以在percona_schema中获取
            - 在线过滤复制
            - 执行change master to不需要stop slave
            - 真正的并行复制
            - 在线开启GTID
        * Innodb 改进
            - 在线修改buffer pool size
            - RENAME INDEX不需要重建
            - 分区表支持Transportable Tablesspace
            - 更好的innobacksum
            - 支持空间索引
            - 支持innodb buffer pool的dump和reload，并且可以指定dump的比例
        * 触发器
            - 每个表支持多个触发器
        * 性能提升
            - Bulk data load提升
        * 优化器提升
            - Explan for connection，对已经运行的查询explain
            - UNION ALL不使用临时表
            - explain JSON提升
            - 支持虚拟列
        * 安全提升
            - 密码过期策略
            - 锁住用户
            - 更简单的安装，安装完成以后仅root@localhost用户

[1]: https://www.percona.com/blog/2015/05/27/mysql-5-7-key-features/
