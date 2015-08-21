# show engine innodb status

show engine innodb status可以查看innodb的一些内部状态，包括下面几个部分：

* BACKGROUND THREAD
* SEMAPHORES
* LATEST DETECTED DEADLOCK
* TRANSACTIONS
* FILE I/O
* INSERT BUFFER AND ADAPTIVE HASH INDEX
* LOG
* BUFFER POOL AND MEMORY
* ROW OPERATIONS

下面是一个真实的lofetr实例上获取得到的信息：

    *************************** 1. row ***************************
      Type: InnoDB
      Name:
    Status:
    =====================================
    150819 23:44:22 INNODB MONITOR OUTPUT
    =====================================
    Per second averages calculated from the last 26 seconds
    -----------------
    BACKGROUND THREAD
    -----------------
    srv_master_thread loops: 2937289 1_second, 2937284 sleeps, 293716 10_second, 1342 background, 1322 flush
    srv_master_thread log flush and writes: 2969682
    ----------
    SEMAPHORES
    ----------
    OS WAIT ARRAY INFO: reservation count 137610369, signal count 117098212
    Mutex spin waits 1050344572, rounds 6137763129, OS waits 85212739
    RW-shared spins 45869149, rounds 1245877095, OS waits 33142825
    RW-excl spins 14958319, rounds 594037281, OS waits 15353210
    Spin rounds per wait: 5.84 mutex, 27.16 RW-shared, 39.71 RW-excl
    ------------------------
    LATEST DETECTED DEADLOCK
    ------------------------
    150819 23:31:38
    *** (1) TRANSACTION:
    TRANSACTION 645CC8C44, ACTIVE 0 sec inserting
    mysql tables in use 1, locked 1
    LOCK WAIT 4 lock struct(s), heap size 1248, 3 row lock(s)

    MySQL thread id 600694, OS thread handle 0x7fa463521700, query id 6527126686 10.120.152.121 tl_new update
    INSERT INTO GT_User(UserId, EventType, EventSortType, LastPullTime,LastUpdateTime , LastFollowStars) VALUES (492777220, 10, 0, 1437547781, 0, '')
    *** (1) WAITING FOR THIS LOCK TO BE GRANTED:
    RECORD LOCKS space id 79 page no 57143 n bits 392 index `PRIMARY` of table `tl_new`.`GT_User` trx id 645CC8C44 lock_mode X insert intention waiting
    Record lock, heap no 1 PHYSICAL RECORD: n_fields 1; compact format; info bits 0
     0: len 8; hex 73757072656d756d; asc supremum;;

    *** (2) TRANSACTION:
    TRANSACTION 645CC8C45, ACTIVE 0 sec inserting
    mysql tables in use 1, locked 1
    4 lock struct(s), heap size 1248, 3 row lock(s)

    MySQL thread id 604919, OS thread handle 0x7fa4b5a33700, query id 6527126689 10.120.152.121 tl_new update
    INSERT INTO GT_User(UserId, EventType, EventSortType, LastPullTime,LastUpdateTime , LastFollowStars) VALUES (492777220, 0, 0, 1439982900, 0, '')
    *** (2) HOLDS THE LOCK(S):
    RECORD LOCKS space id 79 page no 57143 n bits 392 index `PRIMARY` of table `tl_new`.`GT_User` trx id 645CC8C45 lock_mode X
    Record lock, heap no 1 PHYSICAL RECORD: n_fields 1; compact format; info bits 0
     0: len 8; hex 73757072656d756d; asc supremum;;

    *** (2) WAITING FOR THIS LOCK TO BE GRANTED:
    RECORD LOCKS space id 79 page no 57143 n bits 392 index `PRIMARY` of table `tl_new`.`GT_User` trx id 645CC8C45 lock_mode X insert intention waiting
    Record lock, heap no 1 PHYSICAL RECORD: n_fields 1; compact format; info bits 0
     0: len 8; hex 73757072656d756d; asc supremum;;

    *** WE ROLL BACK TRANSACTION (2)
    ------------
    TRANSACTIONS
    ------------
    Trx id counter 645E4F3BB
    Purge done for trx's n:o < 645E4D05B undo n:o < 0
    History list length 2400
    LIST OF TRANSACTIONS FOR EACH SESSION:
    ---TRANSACTION 0, not started
    MySQL thread id 605367, OS thread handle 0x7fa4610b2700, query id 6529344068 localhost rdsadmin
    show engine innodb status
    ---TRANSACTION 645E4F32C, not started
    MySQL thread id 603635, OS thread handle 0x7fa4636e8700, query id 6529343832 10.120.153.217 tl_new
    ---TRANSACTION 645E4F380, not started
    MySQL thread id 603616, OS thread handle 0x7fa45d7be700, query id 6529343959 10.120.152.121 tl_new
    ---TRANSACTION 645E3FBA1, not started
    MySQL thread id 603489, OS thread handle 0x7fa45273c700, query id 6529264839 10.120.152.110 tl_new
    ---TRANSACTION 645E4D3B0, not started
    MySQL thread id 603303, OS thread handle 0x7fa44f431700, query id 6529331670 10.120.152.110 tl_new
    ---TRANSACTION 645E4F0B8, not started
    MySQL thread id 603214, OS thread handle 0x7fa48a3b5700, query id 6529342956 10.120.152.121 tl_new
   1 lock struct(s), heap size 376, 0 row lock(s)
    , undo log entries 1
    MySQL thread id 604735, OS thread handle 0x7fa450679700, query id 6529344063 10.120.152.121 tl_new
    --------
    FILE I/O
    --------
    I/O thread 0 state: waiting for completed aio requests (insert buffer thread)
    I/O thread 1 state: waiting for completed aio requests (log thread)
    I/O thread 2 state: waiting for completed aio requests (read thread)
    I/O thread 3 state: waiting for completed aio requests (read thread)
    I/O thread 4 state: waiting for completed aio requests (read thread)
    I/O thread 5 state: waiting for completed aio requests (read thread)
    I/O thread 6 state: waiting for completed aio requests (write thread)
    I/O thread 7 state: waiting for completed aio requests (write thread)
    I/O thread 8 state: waiting for completed aio requests (write thread)
    I/O thread 9 state: waiting for completed aio requests (write thread)
    Pending normal aio reads: 0 [0, 0, 0, 0] , aio writes: 0 [0, 0, 0, 0] ,
     ibuf aio reads: 0, log i/o's: 0, sync i/o's: 0
    Pending flushes (fsync) log: 0; buffer pool: 0; flash cache 0
    801937425 OS file reads, 2835219322 OS file writes, 39800680 OS fsyncs
    424.06 reads/s, 16384 avg bytes/read, 1188.19 writes/s, 16.19 fsyncs/s
    -------------------------------------
    INSERT BUFFER AND ADAPTIVE HASH INDEX
    -------------------------------------
    Ibuf: size 112619, free list len 77233, seg size 189853, 51288543 merges
    merged operations:
     insert 894742657, delete mark 0, delete 0
    discarded operations:
     insert 0, delete mark 0, delete 0
    Hash table size 30598537, node heap has 8717 buffer(s)
    621.90 hash searches/s, 4679.24 non-hash searches/s
    ---
    LOG
    ---
    Log sequence number 14286481917369
    Log flushed up to   14286481479274
    Last checkpoint at  14285720071263
    0 pending log writes, 0 pending chkp writes
    2300379099 log i/o's done, 952.42 log i/o's/second
    ----------------------
    BUFFER POOL AND MEMORY
    ----------------------
    Total memory allocated 15831187456; in additional pool allocated 0
    Dictionary memory allocated 120118
    Buffer pool size   943680
    Free buffers       0
    Database pages     934963
    Old database pages 345112
    Modified db pages  212542
    Pending reads 0
    Pending writes: LRU 0, flush list 0, single page 0
    Pages made young 1058818236, not young 0
    533.13 youngs/s, 0.00 non-youngs/s
    Pages read 801926175, created 15640055, written 524533636
    424.10 reads/s, 4.50 creates/s, 231.64 writes/s
    Buffer pool hit rate 985 / 1000, young-making rate 20 / 1000 not 0 / 1000
    Pages read ahead 0.00/s, evicted without access 0.00/s, Random read ahead 0.00/s
    LRU len: 934963, unzip_LRU len: 0
    I/O sum[33122]:cur[158], unzip sum[0]:cur[0]
    Async Flush: 7876638, Sync Flush: 0, LRU List Flush: 17308740, Flush List Flush: 507224896
    --------------
    ROW OPERATIONS
    --------------
    0 queries inside InnoDB, 0 queries in queue
    1 read views open inside InnoDB
    Main thread process no. 20855, id 140344289113856, state: sleeping
    Number of rows inserted 1332200381, updated 546301524, deleted 48975173, read 7031442248
    313.64 inserts/s, 278.84 updates/s, 6.19 deletes/s, 2465.71 reads/s
    ----------------------------
    END OF INNODB MONITOR OUTPUT
    ============================

