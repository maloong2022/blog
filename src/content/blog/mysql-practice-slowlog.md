---
author: Maloong🐎🐲
pubDatetime: 2023-03-24T17:34:00Z
title: 卷起来🐎🐲💪 -- MySQL实践之Mac Homebrew Install MySql打开慢日志
postSlug: mysql-base-logsystem
featured: false
draft: false
tags:
  - MySQL
ogImage: ""
description: Mac Homebrew Install MySql打开慢日志记录功能
---
Mac Homebrew Install MySQL打开慢日志记录功能。

## Table of contents

## Versions below 5.1.6

### 找到mysql的my.cnf文件

```bash
cd /opt/homebrew/Cellar/mysql/8.0.32/.bottle/etc
```

```bash
cp my.cnf ~/.my.cnf # copy the default setting
```

### 在.my.cnf文件中[mysqld]下面添加以下内容

```bash
## show slow log
slow_query_log=1
slow_query_log_file=~/log/mysql/mysql-slow.log
long_query_time=1
```

MySQL访问配置文件位置说明：

```sql
Default options are read from the following files in the given order:
/etc/my.cnf /etc/mysql/my.cnf /usr/etc/my.cnf ~/.my.cnf 
```

### 重启MySQL服务

```bash
brew services stop mysql
brew services start mysql
```

## Versions above 5.1.6

**1. Enter the MySQL shell and run the following command:**

```sql
set global slow_query_log = 'ON';
```

**2. Enable any other desired options. Here are some common examples:**

**Log details for queries expected to retrieve all rows instead of using an index:**

```sql
set global log_queries_not_using_indexes = 'ON'
```

**Set the path to the slow query log:(在8.0.32版本中不让修改此项！)**

```sql
set global slow_query_log_file ='/var/log/mysql/slow-query.log';
```

**Set the amount of time a query needs to run before being logged:**

```sql
set global long_query_time = 20;
     (default is 10 seconds)
```

**注意：此种方式mysql重启之后就没有效果了。这个版本同样可以使用前面修改my.cnf的方式修改，而且重启之后仍然有效。**

### 验证是否开启成功

#### 查看参数是否修改成功

```sql
MySQL root@(none):(none)> show variables like '%slow%';
+-----------------------------+----------------------------+
| Variable_name               | Value                      |
+-----------------------------+----------------------------+
| log_slow_admin_statements   | OFF                        |
| log_slow_extra              | OFF                        |
| log_slow_replica_statements | OFF                        |
| log_slow_slave_statements   | OFF                        |
| slow_launch_time            | 2                          |
| slow_query_log              | ON                         |
| slow_query_log_file         | ~/log/mysql/mysql-slow.log |
+-----------------------------+----------------------------+
```

由slow_query_log=ON和slow_query_log_file=~/log/mysql/mysql-slow.log 可知已经开启成功。

#### 实验记录慢SQL

set long_query_time = 0;保证此session的所有SQL语句都打印（为了实验而已）。

```sql
MySQL root@(none):study> set long_query_time = 0;
                      -> select * from t;
Query OK, 0 rows affected
Time: 0.001s

+----+----+----+
| id | c  | d  |
+----+----+----+
| 0  | 0  | 0  |
| 5  | 5  | 5  |
| 10 | 10 | 10 |
| 15 | 15 | 15 |
| 20 | 20 | 20 |
| 25 | 25 | 25 |
+----+----+----+

6 rows in set
Time: 0.005s
```

#### 查看慢SQL日志文件

查看日志文件可以看到

```bash
 148   | SET timestamp=1681712740;
 149   │ set long_query_time = 0;
 150   │ # Time: 2023-04-17T06:25:40.585060Z
 151   │ # User@Host: root[root] @ localhost []  Id:    11
 152   │ # Query_time: 0.000559  Lock_time: 0.000004 Rows_sent: 6  Rows_ex
       │ amined: 6
 153   │ SET timestamp=1681712740;
 154   │ select * from t;
```
