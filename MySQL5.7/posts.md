# What's new in MySQL 5.7

* [MySQL 5.7 Release Notes][0]

    - Title:MySQL 5.7 Release Notes
    - Source:dev.mysql.com
    - Abstrace:MySQL 5.7各个版本的release note

* [Improved Server Defaults in 5.7][1]

    - Title:Improved Server Defaults in 5.7
    - Source:MySQL Server Blog
    - Abstrace:介绍了MySQL 5.7中，默认参数配置的改变，涉及到复制、InnoDB、Performance_Schema、安全性、Runtime and General Improvements和优化器。
    - Content
        * Enable Simplified GTID recovery by default
        * Set ROW based binary log format by default
        * Make binlog_error_action=ABORT_SERVER the default
        * Enable crash safe binary logs by default
        * Lower default slave_net_timeout
        * Deprecate @@session.gtid_executed

* [Now featuring super_read_only and disabled_storage_engines][2]

    - Title:Now featuring super_read_only and disabled_storage_engines
    - Source:Morgan Tocker - MySQL Community Manager
    - Abstrace: 引入了两个新的配置 super_read_only disabled_storage_engines


* [MySQL 5.7.8 - mysqlpump caveat][3]

    - Title:MySQL 5.7.8 - mysqlpump caveat
    - Source:Morgan Tocker - MySQL Community Manager
    - Abstrace:介绍了一个mysqldump替换工具，提供了更多的特性，包括多线程dump、dump权限、进度展示等

* [New Optimizer Hints in MySQL][4]

    - Title:New Optimizer Hints in MySQL
    - Source:MySQL Server Blog
    - Abstrace:执行SQL语句的时候，添加注释，以对优化器进行控制，MySQL 5.7.8增加了两个提示选项
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


[0]: https://dev.mysql.com/doc/relnotes/mysql/5.7/en/
[1]: http://mysqlserverteam.com/improved-server-defaults-in-5-7/
[2]: http://www.tocker.ca/2015/07/09/mysql-5-7-8-now-featuring-super_read_only-and-disabled_storage_engines.html
[3]: http://www.tocker.ca/2015/08/03/mysql-5-7-8-mysqlpump-caveat.html
[4]: http://mysqlserverteam.com/new-optimizer-hints-in-mysql/
