# 恢复
* innobase_start_or_create_for_mysql(void);
    * trx_sys_doublewrite_restore_corrupt_pages();
		* trx_doublewrite_init(doublewrite); 通过读取系统事务段，获取MAGIC判断是否使用了double write
        * 读入double write的数据到内存
        * 遍历double write中的页与相应的数据页，判断数据页是否被损坏，如果损坏，则使用double write中的页代替
			buf_page_is_corrupted(read_buf)
	    * fil_flush_file_spaces(FIL_TABLESPACE); 恢复操作完成以后，刷新表空间

# 内存结构

trx_doublewrite_t


    /* Doublewrite control struct */
    struct trx_doublewrite_struct{
    	mutex_t	mutex;		/* mutex protecting the first_free field and
    				write_buf */
    	ulint	block1;		/* the page number of the first
    				doublewrite block (64 pages) */
    	ulint	block2;		/* page number of the second block */
    	ulint	first_free;	/* first free position in write_buf measured
    				in units of UNIV_PAGE_SIZE */
    	byte*	write_buf; 	/* write buffer used in writing to the
    				doublewrite buffer, aligned to an
    				address divisible by UNIV_PAGE_SIZE
    				(which is required by Windows aio) */
    	byte*	write_buf_unaligned; /* pointer to write_buf, but unaligned */
    	buf_block_t**
    		buf_block_arr;	/* array to store pointers to the buffer
    				blocks which have been cached to write_buf */
    };


# 初始化段


* innobase_start_or_create_for_mysql(void)

    	if (srv_use_doublewrite_buf && trx_doublewrite == NULL) {
    		trx_sys_create_doublewrite_buf();
    	}

* trx_sys_create_doublewrite_buf();注意，这里分配了128+32个页


		for (i = 0; i < 2 * TRX_SYS_DOUBLEWRITE_BLOCK_SIZE
						+ FSP_EXTENT_SIZE / 2; i++) {
			page_no = fseg_alloc_free_page(fseg_header,
			 				prev_page_no + 1,
							FSP_UP, &mtr);
			if (page_no == FIL_NULL) {
				fprintf(stderr,
			"InnoDB: Cannot create doublewrite buffer: you must\n"
			"InnoDB: increase your tablespace size.\n"
			"InnoDB: Cannot continue operation.\n");

				exit(1);
			}

* log_make_checkpoint_at

# 使用

buf pool模块使用double write，使用方式如下：

1. 首先写入到double write
2. 如果满，则刷出到磁盘，刷出到double write以后，再将block写到磁盘（异步IO）

* buf_flush_write_block_low(block);
* buf_flush_post_to_doublewrite_buf
* buf_flush_buffered_writes(void)**核心函数**
