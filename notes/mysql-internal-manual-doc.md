# MySQL Internals Manual

<http://dev.mysql.com/doc/internals/en/index.html>

### Preface and Legal Notice
### 1 A Guided Tour Of The MySQL Source Code

简单介绍了MySQL的代码结构和重要的目录，其中，比较重要的是1.9节的`The Skeleton
of the Server Code`，介绍了一条SQL语句从发起到执行，都经历了哪些步骤。


### 2 Coding Guidelines

### 3 Reusable Classes and Templates

主要是想介绍如下内容，不过这一章是空白的，需要自行研究。这里列出的几个数据接口，可以拿到自己的项目中，方便地进行重用。

* 3.1.1 Array
* 3.1.2 I_P_List
* 3.1.3 I_List
* 3.2.1 MEM_ROOT

### 4 Building MySQL Server with CMake

### 5 Plugins

### 6 Transaction Handling in the Server

### 7 The Optimizer

### 8 Tracing the Optimizer

### 9 Memory Allocation

### 10 Important Algorithms and Structures

### 11 File Formats

### 12 How MySQL Performs Different Selects

### 13 How MySQL Transforms Subqueries

### 14 MySQL Client/Server Protocol

### 15 Stored Programs

### 16 Prepared Statement and Stored Routine Re-Execution

### 17 Writing a Procedure

### 18 Replication

本来要讲以下内容的，不过很多都空白，只有复制相关的源文件，比较有参考价值。

* 18.1 Chapter Organization
* 18.2 Source Code Files
* 18.3 Principles
* 18.4 Rules

### 19 The Binary Log

介绍了binary log，主要介绍了各种Event，Event的格式，和基于ROW格式的binlog。

http://dev.mysql.com/doc/internals/en/event-structure.html

[Event的类图](http://mingxinglai.com/mysql56-annotation/classLog__event.html)

### 20 MyISAM Storage Engine

### 21 InnoDB Storage Engine

### 22 Writing a Custom Storage Engine

### 23 Test Synchronization

### 24 Injecting Test Faults

### 25 How to Create Good Test Cases

### 26 Error Messages

### A MySQL Source Code Distribution

### B InnoDB Source Code Distribution
