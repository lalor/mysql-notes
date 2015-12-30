
* innobase_start_or_create_for_mysql(void)
* recv_recovery_from_checkpoint_start(LOG_CHECKPOINT,

		recv_sys_create();
		recv_sys_init();

	    err = recv_find_max_checkpoint(&max_cp_group, &max_cp_field);
		if (ut_dulint_cmp(checkpoint_lsn, max_flushed_lsn) != 0
	    	   || ut_dulint_cmp(checkpoint_lsn, min_flushed_lsn) != 0) {

* recv_find_max_checkpoint(&max_cp_group, &max_cp_field);
* recv_group_scan_log_recs(
* log_group_read_log_seg  每次读取64k/* Reads a specified log segment to a buffer. 
		                end_lsn = ut_dulint_add(start_lsn, RECV_SCAN_SIZE);
                        #define RECV_SCAN_SIZE		(4 * UNIV_PAGE_SIZE) */
* recv_scan_log_recs /* 将日志记录加入到hash表 */
* recv_parse_log_recs(
* recv_add_to_hash_table(

    	recv = mem_heap_alloc(recv_sys->heap, sizeof(recv_t));
    	recv->type = type;
    	recv->len = rec_end - body;
    	recv->start_lsn = start_lsn;
    	recv->end_lsn = end_lsn;


* recv_parse_log_rec /* Tries to parse a single log record and returns its length. */
* recv_apply_hashed_log_recs(FALSE);
* recv_recover_page  **重点** /* Applies the hashed log records to the page */
* buf_flush_recv_note_modification(block, start_lsn, end_lsn); /* 加入脏也链表 */


# 姜sir的书上说法

1. recv_parse_or_apply_log_rec_body 根据重做日志类型，调用不同的函数进行恢复
    * recv_parse_log_rec
    * recv_recover_page
2. recv_add_to_hash_table 将重做日志加入到hash表
3. recv_recover_page 将重做日志应用到页上
4. recv_read_in_area 
