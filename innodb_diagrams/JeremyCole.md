innodb_diagrams
<https://github.com/jeremycole/innodb_diagrams.git>

A quick introduction to innodb_ruby

<http://blog.jcole.us/2013/01/03/a-quick-introduction-to-innodb-ruby/>


a lot of blogs about InnoDB internals, structures, and behavior.
<http://blog.jcole.us/innodb/>

1. On learning InnoDB: A journey to the core: An introduction to the innodb_ruby and innodb_diagrams projects.
1. A quick introduction to innodb_ruby: How to set up innodb_ruby and a few demos of what it can do.
1. The basics of InnoDB space file layout: How InnoDB structures its space files and the pages they contain.
1. Page management in InnoDB space files: Structures related to management of file segments, extents, and pages within space files.
1. Exploring InnoDB page management with innodb_ruby: Interactive exploration of the page management data structures from a real InnoDB space file.
1. The physical structure of InnoDB index pages: A description of InnoDB’s index pages, where data is stored, and how records are placed in them.
1. B+Tree index structures in InnoDB: A logical high-level exploration of InnoDB’s B+Tree indexes and their efficiency.
1. The physical structure of records in InnoDB: A low-level illustration of InnoDB’s row storage formats.
1. Efficiently traversing InnoDB B+Trees with the page directory: A deep examination of efficiency in traversing B+Trees in InnoDB.
1. InnoDB bugs found during research on InnoDB data storage: An explanation about 7 different bugs found during the research for this work
1. How does InnoDB behave without a Primary Key?: A short discussion about InnoDB’s implicit ROW_ID column which is used in tables without a suitable PRIMARY KEY.
1. InnoDB Tidbit: The doublewrite buffer wastes 32 pages (512 KiB): How the file segment allocation used in InnoDB plus a bit of programming laziness caused 512 KiB to be wasted in every InnoDB system tablespace.
1. The basics of the InnoDB undo logging and history system: A short introduction to multi-version concurrency control, undo logging, InnoDB’s history system, and how they are all related.
1. A little fun with InnoDB multi-versioning: A fun and somewhat scary look at the “hidden” effects of multi-versioning and InnoDB’s history system.
1. InnoDB with reduced page sizes wastes up to 6% of disk space: InnoDB’s required bookkeeping information for each extent wastes many pages, and it gets a lot worse with reduced (4k or 8k) page sizes.
1. Visualizing the impact of ordered vs. random index insertion in InnoDB: Using the space-lsn-age-illustrate and space-extents-illustrate modes of innodb_space to visualize the efficiency of index builds.
