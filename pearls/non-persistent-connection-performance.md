# non persistent connection performance

<http://yoshinorimatsunobu.blogspot.com/2012/12/non-persistent-connection-performance.html>

There are many performance improvements in 5.6, so I'm not sure which fix contributed the most. I assume the biggest one is that THD (quite a large C++ class inside MySQL) destructor is no longer called during holding a global mutex. Prior to 5.6, THD destructor is called under a global LOCK_thread_count mutex. This is not efficient. I experienced some serious connection/disconnection problems caused by the global mutex in MySQL Cluster a few years ago, and there were some fixes in MySQL Cluster source tree. Now in 5.6, THD destructor is called outside of the LOCK_thread_count mutex as below. This is great news. 

    5.1/5.5: one_thread_per_connection_end() -> unlink_thd()
      (void) pthread_mutex_lock(&LOCK_thread_count);
      thread_count--;
      delete thd;
      ...


    5.6.9: one_thread_per_connection_end()
      ...
      mysql_mutex_unlock(&LOCK_thread_count);
      delete thd;
