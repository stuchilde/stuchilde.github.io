---
title: 一条更新的SQL语句是如何执行的？
date: 2019-03-16 15:09:41
tags:
   - MySQL
---

>一条更新SQL语句的执行过程

### 提出问题
`UPDATE student SET score = score + 1 WHERE uid = 666;`
以上就是一条最简单的SQL更新语句，想要知道上面这句SQL语句是怎么执行的先要了解MySQL数据库的逻辑架构。

<!-- more -->

<div align="center">
<img src="https://blog-1252574663.cos.ap-shanghai.myqcloud.com/mysql%E7%BB%93%E6%9E%84%E5%9B%BE.png" width="500px"/></div>
UPDATE语句也不例外的执行这个流程，先连接数据库（连接器），然后将SQL语句进行词法分析，并检测SQL语法（分析器），然后优化对应的查询操作（优化器），最后真正的去执行这个语句（执行器）。

具体到上面的UPDATE语句，先取出uid=666的所有行，然后将这些行的score字段的值加1，并写入内存中。接下来的过程与查询语句的查询流程就不一样了，查询语句只需要返回查询结果即可，但是更新语句需要去真的修改数据库中的数据，所以更新语句相对来讲要复杂一些。

说到SQL的更新语句就不得不提到**重做日志(redo log)** 和**归档日志(binlog)**，这两个日志在MySQL中起到了巨大的作用，这两个日志的相互配合也是很有意思的设计，接下来就要详细给大家讲下这两种日志的作用、它们是如何工作的、以及它们之间的相互配合。

### redo log

redo log是为了**解决crash-safe问题**而产生的，是一种物理日志，我们知道数据库是用来存储数据的，crash-safe问题对于数据库来说是非常重要的，在开启redo log之后MySQL的异常重启之前提交的数据都不会丢失，这样就能保证异常crash后数据不会丢失。

redo log是**InnoDB引擎层**的一种日志，是用来记录这个页"做了什么改动"。在MySQL中经常会说道**WAL**技术，WAL的全称是**Write Ahead Logging**，WAL的核心思想就是日志先行，举个例子，执行一条更新语句，InnoDB就会先把记录写到redo log里面，然后更新到内存，等到系统比较空闲的时候再写入磁盘。redo log的文件大小是固定的，是通过循环写的
实现的。

有了redo log就能保证InnoDB即使发生异常重启也不会丢失数据，这种能力也叫做crash-safe的能力

### binlog

binlog是一种逻辑日志，是**Server层**的一种日志，记录了所有的sql语句，主要是用来配合备份来恢复数据库的，只要我们有最近一次的备份和这期间完整的binlog就能够恢复数据库了。
下面我们来简单看下binlog文件，我是ubuntu系统，这个文件是放在`/var/log/mysql/`文件夹下面的，
<div align="center">
<img src="https://blog-1252574663.cos.ap-shanghai.myqcloud.com/binlog1.png" width="500px"/></div>

从上面的图片我们能看到文件名字是依次增加的，与redo log的循环写不同，binlog是追加写的。
我们执行下面的命令行就能看到binlog记录的sql语句是什么样的，还有一些binlog文件内容的参照 <a href="https://dev.mysql.com/doc/refman/8.0/en/mysqlbinlog-row-events.html">官方文档</a>操作。

```shell
sudo mysqlbinlog /var/log/mysql/mysql-bin.000002 --base64-output=DECODE-ROWS --verbose —verbose
```
执行的结果如下图所示：
<div align="center">
  <img src="https://blog-1252574663.cos.ap-shanghai.myqcloud.com/mysqlbinlog.png" width="500px"/>
</div>

从上图来看很清晰的能看懂这个update语句执行的含义。

上面讲了这么多这两种日志的含义，下面简单总结下这两种日志的一些区别：
* redo log是一种物理日志，记录是这个页**做了什么改动**，而binlog是逻辑日志，记录是sql语句的原始逻辑。
* redo log的文件大小是固定的，会循环写入文件，所以会覆盖之前的日志。而binlog是追加写，不会覆盖之前的日志。
* redo log是InnoDB引擎层的日志，而binlog是server层的日志。

**有同学会问，为什么要搞两个日志呀？**

我们知道MySQL最开始默认的引擎是MyASIM引擎，根本就不存在crash-safe的问题，binlog只是用来做归档的。在MySQL5.5.5之后将InnoDB作为默认的存储引擎，这样InnoDB就拥有了crash-safe的能力，在MySQL的架构中，引擎是以插件的形式存在的，InnoDB引擎不是MySQL数据库必须的，所以也就好理解redo log也不是MySQL数据库必须的日志。

这也就好理解为什么要搞两个日志，一个是server层，一个是引擎层，他们负责不同的功能，相互合作。

那具体这两个日志是怎么合作的呢？他们怎么保证数据的一致性呢？

### 两阶段提交
先说下两阶段提交的具体过程：
* UPDATE语句的结果写入内存，同时将这个操作写入redo log，此时redo log处于prepare状态，并告知执行器随时可以提交事物。
* 执行器生成这个操作的binlog，并写入binlog日志。
* 执行器通知将之前处于prepare状态改为commit状态，更新完成。

两个阶段提交保证了redo log和binlog的一致性。
下面来分析下如果不是两个阶段提交会发生什么？

#### 先写redo log后写binlog
如果先写redo log再写binlog的话，当redo log写完的时候发生了crash，此时binlog里面是没有记录的。这时候发生重启，redo log会恢复crash的语句，但是如果用这产生时的binlog去恢复数据库就会丢失这条记录，此时两个日志恢复的数据库数据就产生了差异。

#### 先写binlog后写redo log
如果是先写binlog后写redo log，当写完binlog的时候发生了crash。这时候发生重启，redo log中还没写，此时异常重启后这个事务是无效的，所以无法恢复，但是binlog中有这条数据，当用此时的binlog文件去恢复数据库的时候，就会比当前的数据库数据多一条记录。


**从上面就可以明白，如果不用两阶段提交就有可能出现两个日志状态不一致。**
