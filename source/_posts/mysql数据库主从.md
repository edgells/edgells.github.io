---
title: mysql数据库主从
date: 2017-10-21 15:42:45
tags: Mysql, 主从
---
#### ***本文章内容归作者所有, 如需转载请注明来源***
### Mysql 主从
mysql 主从配置, 从根本上解决了读写分离的问题. 而读写分离是因为数据库应对并发的读写操作时, 应该将两种操作分离.因为读和写操作的io消耗不是一个程度量级.

哪么如何在架构中实现这样的功能呢.
    1. 在应用层做sql路由实现读写分离
    2. 在mysql 之间配置主从
   
   
### mysql master config
mysqld.ini



### mysql salve config
mysqld.ini