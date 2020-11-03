---
title: Flask-sqlalchemy 使用
date: 2017-10-10 13:00:01
tags: Flask, Sqlalchemy, db
---

#### Flask-Sqlalchemy 通过扩展Sqlalchemy 在Flask中引入完善的ORM功能
ORM 是一种工具思想, 不管在哪个语言里面基本上都是通过映射元数据和表, 字段类型, 建立关联.
而Sqlalchemy 是python世界比较出色的orm框架, 在设计上很多地方都借鉴了Java Hibernate的设计思想. 所以在很多地方它们都具有共同性.
现在看看Flask-Sqlalchemy 对Mysql 的操作, 首先在你的虚拟环境中安装依赖

1. pip install flask-sqlalchemy
2. pip install pymysql

```python
from flask import Flask
from flask_sqlalchemy import Sqlalchemy

app = Flask(__name__)   # 实例化app

# sqlalchemy config
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+pymysql://username:password@host:port/dbname'
app.config['SQLALCHEMY_ECHO'] = True    # 用来显示每次对数据库的操作
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = True     # 这个参数需要占用过多的内存来支持Flask-sqlalchemy 所提供的信号， 可以根据业务需要调整
app.config['SQLALCHEMY_BINDS'] = {}     # 多数据库字典用来实现连接多个数据库    {db1 : db_url, db2: db_url}
app.config['SQLALCHEMY_ENGINE_OPTIONS'] = {}    # 如果使用MySQLdb 可以在这里传递 creater 并利用eventlet 的功能
# conn pool config
app.config['SQLALCHEMY_POOL_SIZE'] = 5  # default 5
app.config['SQLALCHEMY_POOL_TIMEOUT'] = 1 # second
app.config['SQLALCHEMY_POOL_RECYCLE'] = 3600 # second 连接超时时间
app.config['SQLALCHEMY_MAX_OVERFLOW'] = 10 # 连接池被占用之后, 所有能溢出申请的连接数量


db = Sqlalchemy(app)    

# 定义模型类
class User(db.Model):
    __tablename__ = 'user'
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    name = db.Column(db.String(255), unique=True)

# 然后我们就可以通过模型类和 db 对数据库进行操作
# add
user = User(name='test')
# 将用户模型实例add这次数据库会话中， db.session 是线程安全的

db.session.add(user)
db.session.commit()

# select
User.query.filter(User.id == 1).first()  # 通过模型类查询, 一般简单的查询都可以通过模型类完成, 不存在返回None
db.session.query(User).filter(User.id == 1).first()  # 通过db 连接完成查询, 一般会有一些复杂的操作, 比如多表join, 应该考虑使用db, 并封装在model层面

# update
User.query.filter()
user.name = 'test1'
db.session.add(user)
db.session.commit()

# update tablename set field=value where conditions...
user = User.query.filter().update({"field": "value"})
db.session.commit()

# delete
db.session.delete(user)

# 对于查询语句还有更多的操作, 我会放在另一篇文章中描述
```
