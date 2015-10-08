# MySQL 5.7可以禁止用户创建某种存储引擎的表，可以通过如下语句实现。

功能介绍见这里：<http://www.tocker.ca/2015/07/09/mysql-5-7-8-now-featuring-super_read_only-and-disabled_storage_engines.html>
详细设计文档：<http://dev.mysql.com/worklog/task/?id=8594>

功能实现比较简单，可以在源码目录，搜索"disabled_storage_engine"与"ha_is_storage_engine_disabled"

* 参数 ack disabled_storage_engines
* 参数实现 ack ha_is_storage_engine_disabled
