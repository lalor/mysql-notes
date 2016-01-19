# most important function

1. 首先要弄清楚instance chunk block frame page 之间的关系

                       +------------------------------------------------------------+
                       |                                                            |
    +-+-+-+-+-+-+-+-+-----------+------+------+------+-----+-----+----+-----+-----+-----+
    | | | | | | | | | +-+       |      |      |      |     |     |    |     |     | +^  |
    | | | | | | | | | | |       |      |      |      |     |     |    |     |     |     |
    | | | | | | | | | | |  ^    |      |      |      |     |     |    |     |     |     |
    +++-+-+-+-+-+-+-+-+-+-------+------+------+------+-----+-----+----+-----+-----+-----+
     |                     |
     |                     |
     +---------------------+


# 关键函数
* buf_page_get_gen 页的读取
* buf_pool_init 初始化
* buf_LUR_search_and_free_block 页的分配
* buf_LRU_add_block 将新读的块放入到冷区
* buf_block_make_young 将冷区的页移到热区

# 难点
* `freed_page_clock` 每次有页被替换出去，该值就加一，通过 `block_t`中的free_page_clock可以判断该页被替换出去的风险
    参见`buf_make_block_young`和`buf_block_peek_if_too_old`。
* `ulint_clock`每次往热区添加一块，`buf_pool->ulit_clock` 就自增1，同时 `block->LRU_position`记录了该块进入热区时的值，该值越大，说明热度越高，被替换出去的可能性越小

# 与学术界的缓冲区替换算法的区别

相对于学术界的缓冲区替换算法，还考虑了以下几点：
1. 磁盘的特性
    * 随机预读
    * 顺序预读
    * 刷新临近页
2. 并发
    * 只有干净页才会被替换，正在被访问的页不能替换
    * 为了加快脏页的查找速度，脏页同时存在于2个链表中
    * 并不是每次访问以后，都会移到MRU位置
3. 数据库的特性
    * checkpoint

#数据结构

* buf_block_t
* buf_page_t
* buf_pool_t



# insert into lru

1. buf_LRU_add_block
1. buf_LRU_make_block_young

# make young

* buf_page_make_young_if_needed(
* buf_page_peek_if_too_old(
    *     buf_page_peek_if_young(
* buf_page_make_young(bpage);
	* buf_LRU_make_block_young(bpage);
	    * buf_LRU_remove_block(bpage);
	    * buf_LRU_add_block_low(bpage, FALSE);


# physical read
* buf_read_page
* buf_read_page_low
* buf_page_init_for_read

# 不懂的地方
* innodb_old_blocks_time
* innodb_max_dirty_pages_pct
* innodb_adaptive_flushing //InnoDB uses an algorithm to estimate the required rate of flushing, based on the speed of redo log generation and the current rate of flushing.
* buf_flush_page_cleaner_thread buf0flu.c

# 脏页刷新目的
* 加快crash recovery速度，由checkpoint完成，Flush List Flush，脏页刷新列表按照  oldest_modification排序，从5.6.2开始，有专门的Page Cleaner线程负责
* 增加内存可用buffers数量，LRU List Flush

部分函数
----------------

    /************************************************************************               
    Recommends a move of a block to the start of the LRU list if there is danger            
    of dropping from the buffer pool. NOTE: does not reserve the buffer pool                
    mutex. */                                                                               
    UNIV_INLINE                                                                             
    ibool                                                                                   
    buf_block_peek_if_too_old(                                                              
    /*======================*/                                                              
                    /* out: TRUE if should be made younger */                               
        buf_block_t*    block)  /* in: block to make younger */                             
    {                                                                                       
        if (buf_pool->freed_page_clock >= block->freed_page_clock                           
                    + 1 + (buf_pool->curr_size / 1024)) {                                   
                                                                                            
            return(TRUE);                                                                   
        }                                                                                   
                                                                                            
        return(FALSE);                                                                      
    } 

# 以前的学习笔记

buf_page_is_accessed 是否某页被访问过
buf_page_set_accessed 设置某页的访问时间
buf_page_peek_if_too_old 决定某页是否需要移动到yong链表中

分三种情况：

buf_page_make_yong_if_needed:
        buf_page_make_yong
            buf_LRU_make_block_young
                buf_LRU_remove_blocK
                buf_LRU_add_block_low

innobase_init
    innobase_stat_or_create_for_mysql
        buf_pool_init
            buf_pool_init_instance
                buf_chunk_init
                    buf_block_init
                        /*add the block to the free list */
                        UT_LIST_ADD_LAST(list, buf_pool_free, &block->page)


# old page move to new

基本思路：访问一个页，然后判断是否有必要将该页变成young page。

先看一下，读取一个页，并移动到MRU位置的流程：
buf_page_get_gen(general)
    buf_page_set_accessed
    buf_page_is_accessed
    buf_page_make_young_if_needed
        buf_page_peek_if_too_old
            buf_page_peek_if_young( 若从page 上一次移动到buf_pool 的LRU head以后，buf pool在此期间evict的page数量超过buf_pool LRU_young_list
            的长度的1/4，那么，说明本page 已经不够年轻，本次访问需要移动page到LRU head否则说明page属于MRU,不需要移动page。)
        buf_page_make_young
            buf_LRU_make_block_young
                buf_LRU_remove_block(UT_LIST_REMOVE)
                buf_LRU_add_block_low(UT_LIST_ADD_FIRST)

# new page became old

在以下三种情况，可能会将buffer中的yong page 变成old page:

1. 在数据库启动阶段，预热以后，page达到BUF_LRU_OLD_MIN_LEN

，调用buf_LRU_old_init 初始化buf pool -> LRU_old 指针。与此同时，调用buf_LRU_old_adjust_len 将部分yong page 变成old page.（1.刚开始将所有的page 均设置为old，长度为512;2.调用buf_LRU_old_adjust_LEN函数调筝LRU链表，重新计算LRU_young 与LRU_old 部分占比;3. 由于LRU_old 的占比默认为3/8 ，因此计算新的LRU_old 部分的长度为189 将LRU_old 头指针，从LRU链表头开始向后移动，直至LRU_old 部分的长度小于等于189+20(BUF_LRU_OLD_TOLERANCE)为止。其中，BUF_LRU_OLD_TOLERANCE 参数指定了LRU_old 部分长度可以偏离正常长度的振幅。指定此参数，可以减少LRU 链表重整次数，提高LRU链表性能。

若LRU链表程度超过BUF_LRU_OLD_MIN_LEN设置，则每一个page 的加入都会引起LRU list 的调整，重整算法与前面步骤一致。

2. 访问一页以后(buf_page_get_gen)，会将该页变成young ，然后调用buf_LRU_old_adjust_len 调整LRU 中的yong和old。

3. InnoDB Buffer Pool 管理调研第7页，还没有怎么弄懂。

buf_LRU_make_block_old
    buf_LRU_add_block_to_end_low
