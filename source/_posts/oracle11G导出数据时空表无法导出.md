---
title: oracle11G导出数据时空表无法导出
date: 2016-07-06 08:49:02
categories: oracle
tags: oracle导出空表
---
  使用oracle数据库导出命令exp导出数据库的时候，数据库中的空表无法导出，因为oracle11g 建表时，默认不分配segment,以节省磁盘空间，当表插入第一条数据时，再分配segment。当然至于想不想在oracle创建表的时候就自动分配segment,是跟oracle的一个配置参数有关的，也就是
  “deferred_segment_creation”；默认该参数为true，说明不自动分配sgement，如果设置该参数为false时，则会从此刻以后每次创建表就自动分配segment。

> 注：segment就是oracle中段的概念，默认一个普通表会对应一个segment；这里的段就是物理存储的对象，而表(table)就是逻辑存储的对象。
  

**解决方法：**

        1）打开命令行工具执行命令：alter system set deferred_segment_creation=false;
        2）执行之后后期创建的空表就可以正常导出了，但是在命令执行之前的空表还是无法进行导出
        3）先查询一下哪些表是空的：select table_name from user_tables where NUM_ROWS=0;
        4）下面我们通过select 来生成修改语句：
            select 'alter table '||table_name||' allocate extent;' from user_tables where num_rows=0
             后复制生成的修改语句执行，问题解决！

**总结：解决方法共3个途径：**

	1：向空白插入一条数据，再删除掉，就分配空间了（空表比较少）。
    2：修改系统参数
		  alter system set deferred_segment_creation=false;
         （对以后新增的表系统会分配 segment），重启数据库。
    3：批量对现有的空表分配空间
         select 'alter table ' || table_name ||  ' allocate extent;' as sql from all_tables where num_rows = 0 and OWNER=username ;


