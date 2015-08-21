# MySQL 的main函数


虽然现在MySQL的main函数已经改得面目全非，代码散落在各处，其实，main函数的主要功能一点没有改变。主要包括以下几个步骤：


    int main(int argc, char **argv)
    {
        _cust_check_startup();
        (void) thr_setconcurrency(concurrency);
        init_ssl();
        server_init();                             // 'bind' + 'listen'
        init_server_components();
        start_signal_handler();
        acl_init((THD *)0, opt_noacl);
        init_slave();
        create_shutdown_thread();
        create_maintenance_thread();
        handle_connections_sockets(0);             // !
        DBUG_PRINT("quit",("Exiting main thread"));
        exit(0);
    }
