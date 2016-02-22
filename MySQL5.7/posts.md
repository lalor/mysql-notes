# What's new in MySQL 5.7

- [MySQL 5.7 Release Notes][0]

    - Source: dev.mysql.com
    - Abstract: MySQL 5.7各个版本的release note

- [Improved Server Defaults in 5.7][1]

    - Source: MySQL Server Blog
    - Abstract: 介绍了MySQL 5.7中，默认参数配置的改变，涉及到复制、InnoDB、Performance_Schema、安全性、Runtime and General Improvements和优化器。
    - Content
        * Enable Simplified GTID recovery by default
        * Set ROW based binary log format by default
        * Make binlog_error_action=ABORT_SERVER the default
        * Enable crash safe binary logs by default
        * Lower default slave_net_timeout
        * Deprecate @@session.gtid_executed

- [Now featuring super_read_only and disabled_storage_engines][2]

    - Source: Morgan Tocker - MySQL Community Manager
    - Abstract: 引入了两个新的配置 super_read_only disabled_storage_engines


- [MySQL 5.7.8 - mysqlpump caveat][3]

    - Source: Morgan Tocker - MySQL Community Manager
    - Abstract: 介绍了一个mysqldump替换工具，提供了更多的特性，包括多线程dump、dump权限、进度展示等

- [Introducing mysqlpump][10]

    - Source: MySQL Server Blog
    - Abstract: 介绍了mysqlpump的使用方法

- [New Optimizer Hints in MySQL][4]

    - Source: MySQL Server Blog
    - Abstract: 执行SQL语句的时候，添加注释，以对优化器进行控制，MySQL 5.7.8增加了两个提示选项
    - Content
        - SEMIJOIN, NO_SEMIJOIN — enable or disable the named semi-join strategy.
        - SUBQUERY — affects whether to use subquery materialization or IN-to-EXISTS transformations.
        - BKA, NO_BKA — control use of the Batched Key Access algorithm for a particular table or query block.
        - BNL, NO_BNL — control use of the Block Nested-Loop algorithm for a particular table or query block.
        - MRR, NO_MRR — control the Multi-Range Read strategy for a particular index or table.
        - NO_ICP — disables the use of index condition pushdown for a particular index or table.
        - NO_RANGE_OPTIMIZATION — disables the use of the range access for a particular index or table.
        - MAX_EXECUTION_TIME — sets the statement execution timeout at N milliseconds.
        - QB_NAME — an auxiliary hint for naming a particular query block. This name can then be used in the later hint specification in order to greater simplify the use of hints in complex compound statements.
    - extra
       - https://dev.mysql.com/doc/refman/5.7/en/optimizer-hints.html

- [JSON Labs Release: Effective Functional Indexes in InnoDB][5]

    - Source: MySQL Server Blog
    - Abstract: 主要可以在Generated Columns创建索引、创建virtual的Generated columns, JSON支持Generated
    - Content:
        - Virtual Columns in InnoDB
        - Creating Indexes on Non-Materialized Virtual Columns
        - Queries Using A “Functional Index”

                mysql> show create table employees\G
                *************************** 1. row ***************************
                       Table: employees
                Create Table: CREATE TABLE `employees` (
                  `id` bigint(20) NOT NULL AUTO_INCREMENT,
                  `info` json DEFAULT NULL,
                  `name` varchar(100) GENERATED ALWAYS AS (jsn_extract(info, '$.name')) VIRTUAL,
                  PRIMARY KEY (`id`),
                  KEY `name` (`name`)
                ) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=latin1


        - limit. There are currently some restrictions around virtual indexes, some of which will be lifted later:
            - Primary Keys cannot contain any virtual columns
            - You cannot create an index on a mix of virtual and non-virtual columns
            - You cannot create a spatial or fulltext index on virtual columns (this limitation will be lifted later)
            - A virtual index cannot be used as a foreign key

- [Virtual Columns and Effective Functional Indexes in InnoDB][6]

    - Source:MySQL Server Blog
    - Abstract: 上一篇博客的延生，介绍了最新的变化、更多的例子和性能测试
    - Content:There are a few noteworthy and useful changes/additions since the initial Lab release:
        - A single “functional index” can now be created on a combination of both virtual columns and non-virtual generated columns. That is, you can create a composite index on a mix of virtual and non-virtual generated columns.
        - Users can create functional indexes ONLINE using the in-place algorithm so that DML statements can still be processed while the index is being created. In order to achieve that, the virtual column values used within the concurrent DML statements are computed and logged while the index is being created, and later replayed on the functional index.
        - Users can create virtual columns based on other virtual columns, and then index them.
        - The next improvement is less visible to user, but still worth mentioning. It is about enhancements to the purge related activities on indexed virtual columns. A new callback (WL#8841) provides a server layer function that can be called by InnoDB purge threads to compute virtual column index values. Generally this computation is done from connection threads (or sessions), however, since internal InnoDB purge threads do not correspond to connections/sessions and thus don’t have THDs or access to TABLE objects, this work was necessary in order to provide a server layer callback which enables the purge threads to make the necessary computations.

- [Resizing the InnoDB Buffer Pool Online][7]

    - Source: MySQL server Blog
    - Abstract: 介绍了MySQL 5.7 动态调整buffer pool的用法、有可能在调整buffer pool的时候会卡一下，卡多久取决于resize的关键路径（操作系统分配、回收内存的速度）以及shrink buffer pool需要做的事情，此外，调整buffer pool的过程中会关闭AHI
    - Content: You can now use the "SET GLOBAL innodb_buffer_pool_size = xxxx" command which causes a resizing job to begin in background.
    - extra
        - All allocate/free/search calls for pages within the buffer pool are only blocked during the critical path in the resizing operation. The critical path consists of the following parts:

            * Allocating memory from the OS and freeing memory back to the OS
            * Resizing internal hash tables (page_hash, zip_hash), if needed

        - The preparation phase for a resizing operation that shrinks the buffer pool may potentially take a long time, but it doesn’t block other concurrent transaction processing related tasks. The preparation phase consists of the following parts:

            * Reduce the number of pages in order to fit within the new smaller size
            * Relocate the pages from chunks that must now be freed

        - During the buffer pool resizing process the adaptive hash index (AHI) is disabled.

- [Building a Better CREATE USER Command][8]

    - Source: MySQL Server Blog
    - Abstract: 介绍了MySQL 5.7 对`create user`命令的改进，简单地说，就是把以前只能通过grant限制的功能，移到了create user命令里。
    - Content
        * No way to set both authentication plugin and password
        * No way to disable a user
        * No way to define user resource limitations
        * No way to set a non-default password expiration policy
        * No way to require SSL/x509

- [Improved ALTER USER Syntax Support in 5.7][11]

    - Source: MySQL Server Blog
    - Abstract: 介绍了MySQL 5.7 对`alter user`命令的改进
    - Content
        * Changing authentication plugin through ALTER USER
        * Updating proxy user mapping without update mysql.user directly
        * Separating account and privilege attributes. No longer do users have to resort to GRANT commands to modify account attributes, such as SSL. For backwards compatibility, such commands are still supported, but deprecated

- [MySQL 5.7 Labs — Inserting, Updating, and Deleting Records via HTTP][9]

    - Source: MySQL Server Blog


- [The MySQL SYS Schema in MySQL 5.7.7][12]

    - Source: MySQL Server Blog
    - Abstract: MySQL 5.7引入了新的系统库sys，用以对使用情况进行统计，能够显著地提高数据库的"可运维性"
    - Content
        * Who is taking up all the resources on my database server? (`select * from user_summary`)
        * Which hosts are hitting my database server those most?(`show tables like 'host%'`)
        * Which objects are accessed the most, and how?(` select * from sys.schema_table_statistics limit 5`)
        * What statements have the highest overall latency, and what statistics did they have?(`select * from statement_analysis limit 10`)
        * Which statements are using temporary tables on disk?(`select * from statements_with_temp_tables limit 5`)
        * Which tables take the most space in my InnoDB buffer pool?(` select * from innodb_buffer_stats_by_table limit 10`)
        * Where is all the memory going on my instance?(`select * from memory_global_by_current_bytes limit 10`)
        * Which database files cause the most IO, and what kind of IO pattern do they have?(`select * from io_global_by_file_by_latency limit 10;`)
        * Where is the time being spent the most within my instance?(`select * from waits_global_by_latency limit 20;`)

- [What's New in MySQL 5.7][13]

    - Source: MySQL Server Blog
    - Abstract: 比较全面地介绍了MySQL5.7的新特性
    - Content
        * Performance & Scalability: Improved InnoDB scalability and temporary table performance, enabling faster online and bulk load operations, and more.
        * JSON Support: With the newly added JSON support in MySQL, you can now combine the flexibility of NoSQL with the strength of a relational database.
        * Replication improvements for increased availability and performance. They include multi-source replication, multi-threaded slave enhancements, online GTIDs, and enhanced semi-sync replication.
        * Performance Schema delivering much better insights. We’ve added numerous new monitoring capabilities, reduced the footprint and overhead, and significantly improved ease of use with the new SYS Schema.
        * Security: We are fulfilling “secure by default” requirements and many new MySQL 5.7 features will help users keep their database secure.
        * Optimizer: We have rewritten large parts of the parser, optimizer, and cost model. This has improved maintainability, extendability, and performance.
        * GIS: Completely new in MySQL 5.7 and including InnoDB spatial indexes, use of Boost.Geometry, along with increased completeness and standard compliance.

- [Using SYS.SESSION as an alternative to SHOW PROCESSLIST][14]

    - Source: MySQL Server Blog
    - Abstract: 演示sys.session的用法
    - Content
        * 作者抛砖引玉，演示了sys数据库中的session用法，主要目的还是介绍sys数据库
        * sys数据库的类比：For Linux users I like to compare performance_schema to /proc, and SYS to vmstat.



[0]: https://dev.mysql.com/doc/relnotes/mysql/5.7/en/
[1]: http://mysqlserverteam.com/improved-server-defaults-in-5-7/
[2]: http://www.tocker.ca/2015/07/09/mysql-5-7-8-now-featuring-super_read_only-and-disabled_storage_engines.html
[3]: http://www.tocker.ca/2015/08/03/mysql-5-7-8-mysqlpump-caveat.html
[4]: http://mysqlserverteam.com/new-optimizer-hints-in-mysql/
[5]: http://mysqlserverteam.com/json-labs-release-effective-functional-indexes-in-innodb/
[6]: http://mysqlserverteam.com/virtual-columns-and-effective-functional-indexes-in-innodb/
[7]: http://mysqlserverteam.com/resizing-buffer-pool-online/
[8]: http://mysqlserverteam.com/building-a-better-create-user-command/
[9]: http://mysqlserverteam.com/mysql-5-7-labs-inserting-updating-and-deleting-records-via-http/
[10]: http://mysqlserverteam.com/introducing-mysqlpump/
[11]: http://mysqlserverteam.com/improved-alter-user-syntax-support-in-5-7/
[12]: http://mysqlserverteam.com/the-mysql-sys-schema-in-mysql-5-7-7/
[13]: http://mysqlserverteam.com/whats-new-in-mysql-5-7-generally-available/
[14]: http://mysqlserverteam.com/using-sys-session-as-an-alternative-to-show-processlist/
