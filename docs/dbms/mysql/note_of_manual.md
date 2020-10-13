# Notes of MySQL 8.0 Reference Manual


## 1 General Information

## 2 Installing and Upgrading MySQL

## 3 Tutorial

## 4 MySQL Programs

## 5 MySQL Server Administration

### 5.3 The mysql System Schema

-	Data Dictionary Tables
-	Grant System Tables
-	Object Information System Tables
-	Log System Tables
-	Server-Side Help System Tables
-	Time Zone System Tables
-	Replication System Tables
-	Optimizer System Tables
-	Miscellaneous System Tables

## 6 Security

## 7 Backup and Recovery

## 8 Optimization

## 9 Language Structure

## 10 Character Sets, Collations, Unicode

## 11 Data Types

## 12 Functions and Operators

## 13 SQL Statements

## 14 MySQL Data Dictionary

## 15 The InnoDB Storage Engine

### 15.1 Introduction to InnoDB

### 15.2 InnoDB and the ACID Model


### 15.3 InnoDB Multi-Versioning

表空间中存储的数据结构: 回滚段(rollback segment), 用于执行undo操作; 为一致性读构建行的早期版本.

undo日志分为:

- 插入undo日志: 只用于事务回滚, 事务提交立即丢弃.
- 更新undo日志: 也用于一致读, 只在没有指派了(一致读时需要更新undo日志构建行早期版本的)快照的事务存在时可以丢弃.

在行中添加了3个字段:

- DB_TRX_ID: 6B, 最近一次插入或更新行的事务ID, 内部将删除视为更新行中特殊位.
- DB_ROLL_PTR: 7B, 滚动指针, 只想回滚段中undo日志记录.
- DB_ROW_ID: 6B, 行ID, 新行插入时递增.

删除行操作:

- 不是立即物理删除的,
- 在丢弃为删除行操作记录的更新undo日志记录时, 才会物理删除行,
- 这种删除操作称为purge,
- `inndodb_max_purge_lag`系统变量: 调节purge线程.


索引处理:

- 聚集索引: 原地更新记录, 指向undo日志项的隐藏的系统列可以被重新构造,
- 二级索引: 没有隐藏的系统列, 不会原地更新.<br>
更新二级索引列时, 旧的二级索引就被标记为删除, 插入新的记录, 被标记为删除的记录最终被purge.<br>
当在较新的事务中, 二级索引记录为被标记为删除或二级索引页被更新时, InnoDB会查找聚集索引中的记录. 在聚集索引中检查记录的DB_TRX_ID, 如果在读事务初始化之后修改了记录, 会从undo日志中检索出记录的正确版本.

当在较新的事务中将二级索引标记为删除或更新了二级索引页, 不会使用覆盖索引技术, 会查找聚集索引中的记录.

如果开启了索引条件下推(index condition pushdown, ICP), 且WHERE条件的一部分可以只用索引中字段评估, MySQL server仍将这部分WHERE条件下推到存储引擎中使用索引来评估. <br>
如果没有找到匹配的记录, 不再查找聚集索引. 如果找到匹配的记录, 其中有被标记为删除的记录, InnoDB会查找聚集索引.

### 15.4 InnoDB Architecture

内存中结构:

- Buffer Pool: 缓冲池
- Change Buffer: 修改缓冲
- Adapative Hash Index: 自适应哈希索引
- Log Buffer: 日志缓冲

磁盘中结构:

- 系统表空间: ibdata1, Change Buffer
- File-Per-Table表空间: innodb_file_per_table=ON, .ibd(单个表数据)
- 通用表空间: .ibd(多个表数据)
- 临时表空间: ibtmp1(全局), .ibt(会话)
- 双写缓冲文件: ib_*_0.dblwr, ib_*_1.dblwr
- Undo表空间: undo_00x(系统), undo_00x.ibu(用户定义的)
- Redo日志: ib_logfile0, ib_logfile1


内存中结构与磁盘中结构通过操作系统缓存交互:

- Buffer Pool => 表空间
- Log Buffer => Redo日志

### 15.5 InnoDB In-Memory Structures

#### 15.5.1 Buffer Pool

Buffer Pool是InnoDB在内存中缓存表和索引数据的一块区域, 划分为一系列包含多行记录的页:

- 页的链表,
- 使用LRU算法的变种, 将不常使用的页移出缓存.


```
-> unused pages become old..................
...............accessed pages made young <-

|---------------------------|--------------|
|head.......5/8.........tail|head..3/8.tail|
|---------------------------|--------------|

......young sublist.........^..old sublist..
............................|...............
....................midpoint insertion......
```

算法操作:

- Buffer Pool中3/8专用于old sublist,
- midpoint是young sublist的尾部遇到old sublist头部的边界,
- 读取页到Buffer Pool时, 在midpoint(old sublist的头部)处插入,
- make it young: 访问old sublist中页, 将其移动到young sublist的头部, 访问操作会区分用户发起的操作和预读操作,
- age, make it old: 随着数据库运行, Buffer Pool中未访问的页增加年龄(age), 向列表尾端移动, <br>
当有页被make young时, young sublist和old sublist中的页age, <br>
当有页被插入midpoint时, old sublist中的页age,<br>
最终保持为未被使用的页达到old sublist的尾端, 被移出缓存.

默认情况下, 由查询读取的页被立即移动到young sublist中, 例如mysqldump操作的表扫描、没有WHERE子句的SELECT语句、预读背景线程加载的页等. 调节信息见15.8.3.3和15.8.3.4.

InnoDB标准监视器的输出中有 **BUFFER POOL AND MEMORY** 一节, 提供了一些Buffer Pool操作的度量值.


#### 15.5.2 Change Buffer

> Insert Buffer

在修改二级索引页时这些页不在Buffer Pool中, 会在Change Buffer中缓存这些修改. 这些修改可能是由INSERT、UPDATE、DELETE操作产生的, 后续当涉及的页被其他读操作加载入Buffer Pool中时, 这些修改被合并.

#### 15.5.3 Adaptive Hash Index


#### 15.5.4 Log Buffer


### 15.6 InnoDB On-Disk Structures

#### 15.6.1 Tables
#### 15.6.2 Indexes
#### 15.6.3 Tablespaces
#### 15.6.4 Doublewrite Buffer
#### 15.6.5 Redo Log
#### 15.6.6 Undo Logs


### 15.7 InnoDB Locking and Transaction Model

### 15.8 InnoDB Configuration


### 15.9 InnoDB Table and Page Compression
### 15.10 InnoDB Row Formats
### 15.11 InnoDB Disk I/O and File Space Management
### 15.12 InnoDB and Online DDL
### 15.13 InnoDB Data-at-Rest Encryption
### 15.14 InnoDB Startup Options and System Variables
### 15.15 InnoDB INFORMATION_SCHEMA Tables
### 15.16 InnoDB Integration with MySQL Performance Schema
### 15.17 InnoDB Monitors

两种监视器:

- 标准监视器<br>
主背景线程的工作 BACKGROUND THREAD<br>
信号量等待 SEMAPHORES<br>
最近的外键和死锁错误的信息 LATEST FOREIGN KEY ERROR, LATEST DETECTED DEADLOCK<br>
事务中锁等待 TRANSACTIONS<br>
活跃事务持有的表和记录锁<br>
pending I/O操作和相关统计量 FILE I/O<br>
Insert Buffer和自适应哈希索引的统计量 INSERT BUFFER AND ADAPTIVE HASH INDEX<br>
Redo日志信息 LOG<br>
Buffer Pool统计量 BUFFER POOL AND MEMORY<br>
行操作信息 ROW OPERATIONS<br>

- 锁监视器<br>
打印额外的锁信息.


开启系统变量:

```
SET GLOBAL innodb_status_output=ON;
SET GLOBAL innodb_status_output_locks=ON;
```

手动获取标准监视器输出:


```
SHOW ENGINE INNODB STATUS
```

重定向标准监视器输出到文件:

```
# 数据目录中的innodb_status.pd, 大概15s一次

# 启动选项
--innodb-status-file
```


### 15.18 InnoDB Backup and Recovery
### 15.19 InnoDB and MySQL Replication
### 15.20 InnoDB memcached Plugin
### 15.21 InnoDB Troubleshooting
### 15.22 InnoDB Limits
### 15.23 InnoDB Restrictions and Limitations

## 16 Alternative Storage Engines

## 17 Replication

## 18 Group Replication

## 19 MySQL Shell

## 20 Using MySQL as a Document Store

## 21 Using MySQL AdminAPI

## 22 MySQL NDB Cluster 8.0

## 23 Partitioning

## 24 Stored Objects

## 25 INFORMATION_SCHEMA Tables

## 26 MySQL Performance Schema

## 27 MySQL sys Schema

## 28 Connectors and APIs

## 29 MySQL Enterprise Edition

## 30 MySQL Workbench

## 31 MySQL on the OCI Marketplace

## A MySQL 8.0 Frequently Asked Questions

## B Error Messages and Common Problems

## C Indexes

## MySQL Glossary


!!! info "系统表空间"
		一个或多个包含InnoDB相关对象的元信息的数据文件(ibdata文件),<br>
		change buffer和doublewrite buffer的存储区域,<br>
		表在系统表空间中创建时的表数据和索引数据.<br>
		5.6.7之前默认将所有表和索引放在系统表空间中.

!!! info "file-per-table"
		8.0默认, 每个表和索引存放在独立的文件中(.ibd文件),<br>
		方便使用依赖于DYNAMIC和COMPRESSED行格式的特性: 表压缩, 页外列高效存储, 大索引键值前缀等.

!!! info "通用表空间"
		5.7.6引入, 使用.ibd文件.<br>
		用CREATE TABLESPACE创建的共享表空间,<br>
		可以在数据目录外创建,<br>
		支持多个表和所有行格式.<br>
