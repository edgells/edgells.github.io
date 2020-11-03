---
title: Eventlet与flask-sqlalchemy的结合
date: 2018-11-03 14:36:19
tags: Eventlet, Flask, Sqlalchemy
---
#### ***本文章内容归作者所有, 如需转载请注明来源***
###  Eventlet与 flask-sqlalchemy的结合
在python中, 常因为性能原因苦恼, 偶然之间发现了eventlet 这个神器, 用来在单线程中模拟协程操作搭配多个gunicorn进程实现并发的手段.
eventlet 基于epoll IO模型实现非阻塞io, 并封装了greenlet实现协程调度. 但由于python解释器的一些限制. 在一些orm中不能直接使用.
通过翻看文档和借鉴 openstack的处理方式.

```python
from flask import Flask
from sqlalchemy import pool
from flask_sqlalchemy import Sqlaclhemy
import eventlet.pool
import MySQLdb

app = Flask(__name__)


# 方式一
# 此方法来源于openstack nova 注意使用MySQLldb
url_args = {}
app.config['SQLALCHEMY_ENGINE_OPTIONS'] = {'creater': eventlet.pool.ConnectionPool(MySQLdb, **url_args).create}
db = Sqlaclhemy(app)

# 方式二
eventlet.monkey_patch(thread=True)
# 由于flask中封装了 scoped_session 所以我们可以在patch后直接使用session, 它是协程安全的

```