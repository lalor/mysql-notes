# InnoDB 存储笔记

学习Innodb存储，反复复习《InnoDB - A journey to the core - PLMCE 2013.pdf》就好了。


# extent vs segment

* 区是一个物理概念
* 段是一个逻辑概念

1. 碎片页从碎片区中申请， 每256M一个碎片区
2. 含有区描述符的区是碎片区
3. 碎片区不属于任何段，但是碎片区中的页属于某一段
