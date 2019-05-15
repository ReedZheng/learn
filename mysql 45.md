### mysql 45讲

> #### 第 1 讲

1. server层，包括连接器、查询缓存、分析器、优化器、执行器等，涵盖MySQL的大多数核心服务功能，以及所有的内置函数（如日期、时间、数学和加密函数等），所有跨存储引擎的功能都在这一层实现，比如存储过程、触发器、视图等。 
   * 连接器，负责跟客户端建立连接、获取权限、维持和管理连接。 （长连接问题）
   * 查询缓存 ，不推荐使用。
   * 分析器，分析器先会做“词法分析”。你输入的是由多个字符串和空格组成的一条SQL语句， MySQL需要识别出里面的字符串分别是什么，代表什么；做完了这些识别以后，就要做“语法分析”。根据词法分析的结果，语法分析器会根据语法规则，判断你输入的这个SQL语句是否满足MySQL语法。 
   * 优化器，经过了分析器， MySQL就知道你要做什么了。在开始执行之前，还要先经过优化器的处理。
     优化器是在表里面有多个索引的时候，决定使用哪个索引；或者在一个语句有多表关联（join）的时
     候，决定各个表的连接顺序。 
   * 执行器，MySQL通过分析器知道了你要做什么，通过优化器知道了该怎么做，于是就进入了执行器阶段，开始执行语句。开始执行的时候，要先判断一下你对这个表T有没有执行查询的权限，如果没有，就会返回没有权限的错误； 如果有权限，就打开表继续执行。打开表的时候，执行器就会根据表的引擎定义，去使用这个引擎提供的接口。  
   
   ```
   在数据库的慢查询日志中看到一个rows_examined的字段，表示这个语句执行过程中扫描了多少行。这个值就是在执行器每次调用引擎获取数据行的时候累加的。在有些场景下，执行器调用一次，在引擎内部则扫描了多行，因此引擎扫描行数跟rows_examined并不是完全相同的。
   ```
2. 存储引擎，负责数据的存储和提取。其架构模式是插件式的，支持InnoDB、 MyISAM、 Memory等多个存储引擎。现在最常用的存储引擎是InnoDB，它从MySQL 5.5.5版本开始成为了默认存储引擎。 



> #### 第 2 讲

1. redo log（重做日志）
   * 当有一条记录需要更新的时候， InnoDB引擎就会先把记录写到redo log里面，并更新内存，这个时候更新就算完成了。同时， InnoDB引擎会在适当的时候，将这个操作记录更新到磁盘里面，而这个更新往往是在系统比较空闲的时候做。
   *  InnoDB的redo log是固定大小的 。
   * 有了redo log， InnoDB就可以保证即使数据库发生异常重启，之前提交的记录都不会丢失，这个能力
     称为crash-safe 。
   * redo log是InnoDB引擎特有的日志 。
2. binlog（归档日志）
   * binlog是MySQL的Server层实现的，所有引擎都可以使用。 （binlog没有crash-safe能力）