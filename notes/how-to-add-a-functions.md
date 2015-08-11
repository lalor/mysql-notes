# function

在sql目录下，执行`ack SQLCOM_SHOW_DATABASES`，就能大概看到如何增加一个函数的所需步骤。
### step 1 添加词法

sql/lex.h修改static SYMBOL symbols[]结构体，添加一个"DISK_USAGE"这个词法


    {"DISK_USAGE", SYM(DISK_USAGE_SYM)}


添加完成以后，词法分析会将DISK_USAGE这个词法当做一个token转化为内部枚举DISK__USAGE_SYM.

### step 2 添加词法分析

有了新的词法，需要为新的词法添加新的语法，修改sql/yacc.yy文件。添加一个token

    %token DISK_USAGE_SYM

### step 3 添加SHOW行为


    show:

    SHOW DISK_USAGE_SYM
    {
        LEX *lex = Lex;
        sql_command = SQLCOM_SHOW_DISK_USAGE;
    }

到这里为止，词法语法部分添加完毕，剩下的就是将词法与具体的函数实现绑定。

### step 4 添加新的命令

修改enum enum_sql_command，把SQLCOM_SHOW_DISK_USAGE作为enum添加进去。

     SQLCOM_SHOW_PROFILE,
      SQLCOM_SHOW_PROFILES,
      /*start*/
      SQLCOM_SHOW_DISK_USAGE,
      /*end*/
      /*
        When a command is added here, be sure it's also added in mysqld.cc
        in "struct show_var_st status_vars[]= {" ...
      */
      /* This should be the last !!! */
      SQLCOM_END
    };

此外，还需要修改sql/mysqld.cc中的SHOW_VAR com_status_vars[]

      {"show_authors",         (char*) offsetof(STATUS_VAR, com_stat[(uint)
              SQLCOM_SHOW_AUTHORS]), SHOW_LONG_STATUS},
      /*start*/
      {"show_disk_usage",      (char*) offsetof(STATUS_VAR, com_stat[(uint)
                SQLCOM_SHOW_DISK_USAGE]), SHOW_LONG_STATUS},
        /*end*/
      {"show_binlog_events",   (char*) offsetof(STATUS_VAR, com_stat[(uint)
                  SQLCOM_SHOW_BINLOG_EVENTS]), SHOW_LONG_STATUS},

不添加这部分会出现编译时ASSERT错误。

### step 5

接着把SQLCOM_DISK_USAGE指向具体函数，修改sql/sql_parse.cc，关键字mysql_execute_command，增加一个Case。


    switch (lex->sql_command) {
    /*start*/
      case SQLCOM_SHOW_DISK_USAGE:
            res = show_disk_usage_command(thd);
            break;
    /*end*/
      case SQLCOM_SHOW_EVENTS:

到这里，我们就把词法与函数关联起来了，下面要做的就是实现函数。

### step 6 实现函数

在sql/mysql_priv.h添加函数声明：


    bool show_disk_usage_command(THD * thd);

在sql/sql_show.cc添加一个具体的实现：


    bool show_disk_usage_command(THD *thd)
    {
      DBUG_ENTER("show_disk_usage");
      List field_list;
      List dbs;
      Protocol *protocol = thd->protocol;

      /*set fields*/
      field_list.push_back(new Item_empty_string("Database", 50));
      field_list.push_back(new Item_empty_string("Size (Kb)", 30));

      if (protocol->send_fields(&field_list, Protocol::SEND_NUM_ROWS|Protocol::SEND_EOF))
        DBUG_RETURN(TRUE);

      /*set data*/
      protocol->prepare_for_resend();
      protocol->store("test_row", system_charset_info);
      protocol->store("1024000", system_charset_info);
      if (protocol->write())
        DBUG_RETURN(TRUE);

      my_eof(thd);
      DBUG_RETURN(FALSE);
    }
    /*end*/

到这里，函数实现就完成了，执行make && make install就能够完成代码编译和安装了。
