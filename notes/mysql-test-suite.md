# MySQL Test Suite

MySQL测试文档是一份独立的文档，与MySQL Internals 一起作为Expert Guides。主要讲了如何运行MySQL的测试，MySQL测试的组成部分，以及，如何编写MySQL的测试。

## MySQL Test Framework Manual

<http://dev.mysql.com/doc/mysqltest/en/index.html>

1. This document describes the components of the MySQL test framework,
1. how the test programs work,
1. and the language used for writing test cases.
1. It also provides a tutorial for developing test cases
1. and executing them.

Within the mysql-test directory, test case input files and result files are stored in the t and r directories, respectively. The input and result files have the same basename, which is the test name, but have extensions of .test and .result, respectively. For example, for a test named “decimal,” the input and result files are mysql-test/t/decimal.test and mysql-test/r/decimal.result.

## How to run a test

* run `make test` from the source root directory
* run `./mysql-test-run.pl` from the mysql-test directory
* run `./mysql-test-run.pl test_name` for an individual test case
* `./mysql-test-run.pl --suite=suite_name`
* `--parallel=N` Run tests in N parallel threads (default=1) Use parallel=auto for auto-setting of N

<http://dev.mysql.com/doc/refman/5.6/en/mysql-test-suite.html>

##  How to writing a test

- [mysqltest Language Reference][1]
- [Writing Test Case][2]


* prepare file

Use the following procedure to write a new test case. In the examples, test_name represents the name of the test case. It is assumed here that you'll be using a development source tree, so that when you create a new test case, you can commit the files associated with it to the source repository for others to use.

Change location to the test directory mysql-version/mysql-test:

    shell> cd mysql-version/mysql-test


Create the test case in a file t/test_name.test. You can do this with any text editor. For details of the language used for writing mysqltest test cases, see Chapter 6, mysqltest Language Reference.

Create an empty result file:

    shell> touch r/test_name.result

Run the test:

    shell> ./mysql-test-run.pl test_name

Assuming that the test case produces output, it should fail because the output does not match the result file (which is empty at this point). The failure results in creation of a reject file named r/test_name.reject. Examine this file. If the reject file appears to contain the output that you expect the test case to produce, copy its content to the result file:

    shell> cp r/test_name.reject r/test_name.result

Another way to create the result file is by invoking mysql-test-run.pl with the --record option to record the test output in the result file:

    shell> ./mysql-test-run.pl --record test_name

Run the test again. This time it should succeed:

    shell> ./mysql-test-run.pl test_name

You can also run the newly created test case as part of the entire suite:

    shell> ./mysql-test-run.pl

* Sample Test Case

<http://dev.mysql.com/doc/mysqltest/2.0/en/writing-tests-sample-test-case.html>

    --disable_warnings
    DROP TABLE IF EXISTS t1;
    --enable_warnings
    SET SQL_WARNINGS=1;
    
    CREATE TABLE t1 (a INT);
    INSERT INTO t1 VALUES (1);
    INSERT INTO t1 VALUES ("hej");

The first few lines try to clean up from possible earlier runs of the test case by dropping the t1 table. The test case uses disable_warnings to prevent warnings from being written to the output because it is not of any interest at this point during the test to know whether the table t1 was there. After dropping the table, the test case uses enable_warnings so that subsequent warnings will be written to the output. The test case also enables verbose warnings in MySQL using the SET SQL_WARNINGS=1; statement.

Next, the test case creates the table t1 and tries some operations. Creating the table and inserting the first row are operations that should not generate any warnings. The second insert should generate a warning because it inserts a nonnumeric string into a numeric column. The output that results from running the test looks like this:

    DROP TABLE IF EXISTS t1;
    SET SQL_WARNINGS=1;
    CREATE TABLE t1 (a INT);
    INSERT INTO t1 VALUES (1);
    INSERT INTO t1 VALUES ("hej");
    Warnings:
    Warning 1265    Data truncated for column 'a' at row 1

Note that the result includes not only the output from SQL statements, but the statements themselves. Statement logging can be disabled with the disable_query_log test language command. There are several options for controlling the amount of output from running the tests.

[1]: http://dev.mysql.com/doc/mysqltest/2.0/en/mysqltest-reference.html
[2]: http://dev.mysql.com/doc/mysqltest/2.0/en/writing-tests.html
