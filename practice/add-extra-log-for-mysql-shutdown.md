# add extra logging on mysql shutdown


facebook的MySQL版本实现了一个功能，在收到shutdown的命令时，显示是谁执行了shutdown操作。

# diff

    git diff fc5819f 08fa975

# commit message


commit 08fa9756bde92cb8a7cba22ee58628a755ff68ab
    Author: Jason Obenberger <jobenber@fb.com>
    Date:   Mon Jul 22 15:37:52 2013 -0700

    Added extra logging on mysql shutdown

    Summary:
    Added extra logging to mysql shutdown sequence to log the requesting
    host/user

    Test Plan:
    Run shutdown and verify proper logging in the mysqld.1.err file
    Run the mysql_shutdown_logging test

    mysqltest.sh --big innodb.innodb-status-output

    Reviewers: jtolmer, pengt, steaphan

    Reviewed By: jtolmer, pengt, steaphan


# changes

diff --git a/mysql-test/r/mysql_shutdown_logging.result b/mysql-test/r/mysql_shutdown_logging.result

    new file mode 100644
    index 0000000..ceb742e
    --- /dev/null
    +++ b/mysql-test/r/mysql_shutdown_logging.result
    @@ -0,0 +1,3 @@
    +CREATE USER wl6587@localhost IDENTIFIED BY 'wl6587';
    +GRANT ALL ON *.* TO wl6587@localhost;
    +DROP USER wl6587@localhost;
    diff --git a/mysql-test/suite/innodb/t/innodb-status-output.test b/mysql-test/suite/innodb/t/innodb-status-output.test
    index cf18c18..c39d158 100644
    --- a/mysql-test/suite/innodb/t/innodb-status-output.test
    +++ b/mysql-test/suite/innodb/t/innodb-status-output.test
    @@ -71,7 +71,7 @@ COMMIT;
     --shutdown_server

     # We should not see any extra output.
    -let SEARCH_PATTERN= ready for connections.*\nVersion:.*\n.*Normal shutdown;
    +let SEARCH_PATTERN= ready for connections.*\nVersion:.*\n.*COM_SHUTDOWN received from host/user.*\n.*Got signal [0-9]+ to shutdown mysqld.*\n.*Normal shutdown;
     --source include/search_pattern_in_file.inc
     --remove_file $SEARCH_FILE

    diff --git a/mysql-test/t/mysql_shutdown_logging.test b/mysql-test/t/mysql_shutdown_logging.test
    new file mode 100644
    index 0000000..a8dd55d
    --- /dev/null
    +++ b/mysql-test/t/mysql_shutdown_logging.test
    @@ -0,0 +1,97 @@
    +# This test is to check that the logging on shutdown properly
    +# displays the host/user that is requesting the database shutdown
    +# This test is incomplete as we are unable to issue a request
    +# from a host whose ip doesn't have a corresponding host name.
    +# (so all shutdown loggings will have only the hostname)
    +
    +# restart server with log file pointing to $error_log
    +# $error_log will be deleted after each shutdown
    +let $error_log=$MYSQLTEST_VARDIR/log/testlog.err;
    +let SEARCH_FILE=$error_log;
    +--disable_reconnect
    +--exec echo "wait" > $MYSQLTEST_VARDIR/tmp/mysqld.1.expect
    +--exec $MYSQLADMIN -S $MASTER_MYSOCK -P $MASTER_MYPORT -uroot shutdown
    +--source include/wait_until_disconnected.inc
    +--enable_reconnect
    +--exec echo "restart:--log-error=$error_log" > $MYSQLTEST_VARDIR/tmp/mysqld.1.expect
    +--source include/wait_until_connected_again.inc
    +
    +# send shutdown command to sql server from root and verify SHUTDOWN was
    +# recieved from localhost/user
    +--disable_reconnect
    +--exec echo "wait" > $MYSQLTEST_VARDIR/tmp/mysqld.1.expect
    +--exec $MYSQLADMIN -S $MASTER_MYSOCK -P $MASTER_MYPORT -uroot shutdown
    +--source include/wait_until_disconnected.inc
    +let SEARCH_PATTERN=Got signal 15 to shutdown mysqld;
    +--source include/search_pattern_in_file.inc
    +let SEARCH_PATTERN=COM_SHUTDOWN received from host/user = localhost/root;
    +--source include/search_pattern_in_file.inc
    +--exec rm $error_log
    +
    +# restart the sql server
    +--enable_reconnect
    +--exec echo "restart:--log-error=$error_log" > $MYSQLTEST_VARDIR/tmp/mysqld.1.expect
    +--source include/wait_until_connected_again.inc
    +
    +# create user and attempt shutdown, expect failure
    +CREATE USER wl6587@localhost IDENTIFIED BY 'wl6587';
    +--error 1
    +--exec $MYSQLADMIN -S $MASTER_MYSOCK -P $MASTER_MYPORT -uwl6587 --password=wl6587 shutdown
    +
    +# give user SHUTDOWN privileges and re-try shutdown, expect success
    +GRANT ALL ON *.* TO wl6587@localhost;
    +
    +--disable_reconnect
    +--exec echo "wait" > $MYSQLTEST_VARDIR/tmp/mysqld.1.expect
    +--exec $MYSQLADMIN -S $MASTER_MYSOCK -P $MASTER_MYPORT -uwl6587 --password=wl6587 shutdown
    +--source include/wait_until_disconnected.inc
    +let SEARCH_PATTERN=Got signal 15 to shutdown mysqld;
    +--source include/search_pattern_in_file.inc
    +let SEARCH_PATTERN=COM_SHUTDOWN received from host/user = localhost/wl6587;
    +--source include/search_pattern_in_file.inc
    +--exec rm $error_log
    +
    +# restart server
    +--enable_reconnect
    +--exec echo "restart:--log-error=$error_log" > $MYSQLTEST_VARDIR/tmp/mysqld.1.expect
    +--source include/wait_until_connected_again.inc
    +
    +# verify hostname is logged properly (use actual hostname instead of localhost)
    +--disable_reconnect
    +--exec echo "wait" > $MYSQLTEST_VARDIR/tmp/mysqld.1.expect
    +--exec $MYSQLADMIN -h `hostname` -P $MASTER_MYPORT -uroot shutdown
    +--source include/wait_until_disconnected.inc
    +let SEARCH_PATTERN=Got signal 15 to shutdown mysqld;
    +--source include/search_pattern_in_file.inc
    +--exec grep -q "COM_SHUTDOWN received from host/user = `hostname`/root" $error_log
    +
    +--exec rm $error_log
    +
    +# THIS IS A BUG
    +# WHEN --skip-name-resolve is set, the host variable of thd->security_ctx is
    +# valid but "", so host_or_ip points to host ("") instead of valid ip
    +# once fixed, can turn on this test
    +# restart server mapping all hostname to their IP addresses only
    +#--enable_reconnect
    +#--exec echo "restart:--log-error=$error_log --skip-name-resolve --skip-grant-tables" > $MYSQLTEST_VARDIR/tmp/mysqld.1.expect
    +#--exec echo "restart:--log-error=$error_log" > $MYSQLTEST_VARDIR/tmp/mysqld.1.expect
    +#--source include/wait_until_connected_again.inc
    +
    +# verify hostname is logged properly (use actual hostname instead of localhost)
    +#--disable_reconnect
    +#--exec echo "wait" > $MYSQLTEST_VARDIR/tmp/mysqld.1.expect
    +#--exec $MYSQLADMIN -h `hostname --ip-address` -P $MASTER_MYPORT -uroot shutdown
    +#--source include/wait_until_disconnected.inc
    +#let SEARCH_PATTERN=Got signal 15 to shutdown mysqld;
    +#--source include/search_pattern_in_file.inc
    +#let SEARCH_PATTERN=COM_SHUTDOWN received from host/user = `hostname --ip-address`/root;
    +#--source include/search_pattern_in_file.inc
    +#--exec rm $error_log
    +
    +# restart server in default manner after last test
    +--exec echo "restart" > $MYSQLTEST_VARDIR/tmp/mysqld.1.expect
    +--enable_reconnect
    +--source include/wait_until_connected_again.inc
    +
    +# drop user to leave framework in clean state
    +DROP USER wl6587@localhost;
    diff --git a/sql/mysqld.cc b/sql/mysqld.cc
    index 540402a..80972bd 100644
    --- a/sql/mysqld.cc
    +++ b/sql/mysqld.cc
    @@ -3468,9 +3468,7 @@ pthread_handler_t signal_hand(void *arg __attribute__((unused)))
         case SIGTERM:
         case SIGQUIT:
         case SIGKILL:
    -#ifdef EXTRA_DEBUG
           sql_print_information("Got signal %d to shutdown mysqld",sig);
    -#endif
           /* switch to the old log message processing */
           logger.set_handlers(LOG_FILE, opt_slow_log ? LOG_FILE:LOG_NONE,
                               opt_log ? LOG_FILE:LOG_NONE);
    diff --git a/sql/sql_parse.cc b/sql/sql_parse.cc
    index 7690d96..f28a1ba 100644
    --- a/sql/sql_parse.cc
    +++ b/sql/sql_parse.cc
    @@ -1811,6 +1811,10 @@ bool dispatch_command(enum enum_server_command command, THD *thd,
         DBUG_PRINT("quit",("Got shutdown command for level %u", level));
         general_log_print(thd, command, NullS);
         my_eof(thd);
    +    sql_print_information(
    +       "COM_SHUTDOWN received from host/user = %s/%s",
    +       thd->security_ctx->host_or_ip,
    +       thd->security_ctx->user);
         kill_mysql();
         error=TRUE;
         break;
