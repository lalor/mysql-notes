# most important function

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


* buf_page_get_gen
* buf_pool_init

相对于学术界的缓冲区替换算法，还考虑了以下几点：
1. 磁盘的特性
    * 随机预读
    * 顺序预读
    * 刷新临近页
2. 并发
    * 只有干净页才会被替换，正在被访问的页不能替换
    * 脏页同时存在于2个链表中
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
