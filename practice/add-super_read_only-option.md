# Super Read Only


MySQL 5.7增加了一个参数配置，即super_read_only，可以阻止具有Super权限的用户修改数据库。其功能与read_only类似，但是，read_only只能限制普通用户。

    | Super_read_only | read_only | comment |
    ________________________________________
    | ON  | ON  | 禁止所有用户修改                   |
    | OFF | ON  | Super用户可以修改，普通用户不能修改|
    | OFF | OFF | 默认取值，所有用户都可以操作数据库 |
    | ON  | OFF | MySQL数据库不允许出现这种情况      |


设计文档：<http://dev.mysql.com/worklog/task/?id=6799>

具体实现：<https://github.com/mysql/mysql-server/commit/f9a90e07f257511d1c30e049e0c01c73652b8f90>


MySQL 5.7的代码，在SQL目录下执行如下操作可以查看修改情况：

    rds-user@lalor:~/mysql/mysql/mysql-server/sql$ git diff --ignore-all-space   862770e f9a90e0  --stat .
    sql/auth/auth_common.h        |    2 ++
    sql/auth/sql_authorization.cc |   59 +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
    sql/handler.cc                |    8 +++-----
    sql/lock.cc                   |    7 +------
    sql/mysqld.cc                 |    1 +
    sql/mysqld.h                  |    1 +
    sql/sql_parse.cc              |   13 +++----------
    sql/sql_trigger.cc            |    7 +------
    sql/sql_update.cc             |    5 ++---
    sql/sys_vars.cc               |   83 +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
    sql/transaction.cc            |    7 +------
    11 files changed, 157 insertions(+), 36 deletions(-)

