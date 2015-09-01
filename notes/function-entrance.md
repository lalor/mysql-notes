# entry point


## MySQL Server

### show slave status


`sql/sql_parse.cc`

    case SQLCOM_SHOW_SLAVE_STAT:
    {
      /* Accept one of two privileges */
      if (check_global_access(thd, SUPER_ACL | REPL_CLIENT_ACL))
        goto error;
      mysql_mutex_lock(&LOCK_active_mi);
      res= show_slave_status(thd, active_mi);
      mysql_mutex_unlock(&LOCK_active_mi);
      break;
    }

### SQL thread

`sql/rpl_slave.cc`

    handle_slave_sql
        exec_relay_log_event   #hold rli->data_lock
            apple_event_and_update_pos
                apply_event


## INNODB
