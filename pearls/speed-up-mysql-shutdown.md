# 也说快速关闭MySQL/InnoDB

如果用的引擎是InnoDB，每次敲下mysqladmin -uroot -p shutdown关闭数据库的时候，总是很难预测这个命令会执行多久，实际经验表明，短则五秒，长则三十分钟一小时都有可能。也分享一下我的经验吧。

### 1. 为什么InnoDB关闭会慢？

事实上，并不是每次关闭InnoDB都很慢的。Why？InnoDB较之MyISAM，一个重要特性是InnoDB会在内存中开辟一个Buffer Pool来存储最近访问的数据块/索引块，使得下次再次访问这个块时速度能够很快。当InnoDB对需要修改数据块的时候，会先记录修改日志，然后直接对Buffer_Pool中的数据块的操作。记录日志是顺序写，对数据块的操作是内存操作，这让InnoDB在很多场景下有这很好的速度优势。

上面对内存块修改完成后，InnoDB就向客户端返回了。可这时实际磁盘上的数据块，还并没有被更新，我们把这样的page称为Dirty Page。在InnoDB的后台有一个专门的线程来做将内存数据块Flush到磁盘的工作。这个Flush操作就是主要影响InnoDB关闭时间的因素。在关闭MySQL/InnoDB时，所有的Dirty_Page都需要Flush，所以Dirty_Page越多，要Flush的数据块也就越多，意味着InnoDB关闭时间越长。

我们可以通过下面的命令来观察Dirty Page的数量：

    mysqladmin -uroot ext -i 1 |grep "Innodb_buffer_pool_pages_dirty"

### 2. 参数innodb_max_dirty_pages_pct

Buffer_Pool中Dirty_Page所占的数量，直接影响InnoDB的关闭时间。参数innodb_max_dirty_pages_pct可以直接控制了Dirty_Page在Buffer_Pool中所占的比率，而且幸运的是innodb_max_dirty_pages_pct是可以动态改变的。

所以，在关闭InnoDB之前先将innodb_max_dirty_pages_pct调小，强制数据块Flush一段时间，则能够大大缩短MySQL关闭的时间。

<http://www.orczhou.com/index.php/2010/12/more-about-mysql-innodb-shutdown/>
