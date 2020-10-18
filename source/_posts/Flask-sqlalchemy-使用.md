---
title: Flask-sqlalchemy 使用
date: 2017-10-10 13:00:01
tags: Flask, Sqlalchemy, db
---

#### Flask-Sqlalchemy 通过扩展Sqlalchemy 在Flask中引入完善的ORM功能
ORM 是一种工具思想, 不管在哪个语言里面基本上都是通过映射元数据和表, 字段类型, 建立关联.
而Sqlalchemy 是python世界比较出色的orm框架, 在设计上很多地方都借鉴了Java Hibernate的设计思想. 所以在很多地方它们都具有共同性.
现在看看Sqlalchemy 对Mysql 的操作, 首先在你的虚拟环境中安装依赖

1. pip install sqlalchemy
2. pip install pymysql

```python
from sqlalchemy import create_engine, sessionmaker, scoped_session
from sqlalchemy import Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base

# 建立处理数据库连接的engine, echo 参数用于让sqlalchemy 将数据库操作显示在console 中
engine = create_engine("mysql+pymysql://user:password@host:port/dbname", echo=True)

# 建立与数据库的映射, 注意需要显示申明与那个engine 建立联系
Base = declarative_base(bind=engine)

# 创建数据库会话管理
Session = scoped_session(sessionmaker(bind=engine)) # 注意, 在个人使用的时候不要直接使用sessionmaker, 即使官方示例是这样. 应该使用scoped_session包装
db = Session()   # 实例化当前做作用域的数据库会话


# 现在我们定义一张表
class User(Base):
    __tablename__ = 'user'
    
    id = Column(Integer, primary_key=True, autoincremnet=True)
    name = Column(String(255), unique=True, comment='用户名称')


Base.metedata.create_all()  # Base会识别出当前作用域下面的Base 子类, 并在数据库中创建相对应的表

# 然后我们通过 db 和模型类, 对数据库进行crud操作
```

这么一看, 还挺麻烦的, 哪么我们在Flask 中也要这样吗. 其实Flask-Sqlalchemy 中已经处理好这些东西, 我们只需要在Flask配置中进行参数配置即可

```python

    from flask_sqlalchemy import Sqlalchemy

    db = Sqlalchemy(app)    # 这里的app是Flask app 实例

    class Config:
        SQLALCHEMY_DATABASE_URI = 'db_url'
        #... 还有许多配置, 可以在官方文档中查阅
    
    
    

    class User(db.Model):
        __tablename__ = 'user'
        id = db.Column(db.Integer, primary_key=True, autoincrement=True)
        name = db.Column(db.String(255), unique=True)

    # 然后我们就可以通过模型类和 db 对数据库进行操作
    # add
    user = User(name='test')
    db.session.add(user)
    db.session.commit()

    # select
    user_id = 1   
    # user = User.query.filter(User.id == user_id).first()
    # user = db.session.query(User).filter(User.id == user_id).first()
    
    # update
    user = User.query.filter()
    user.name = 'test1'
    db.session.add(user)
    db.session.commit()

    user = User.query.filter().update({"field": "value"})
    # update tablename set field=value where conditions...
    db.session.commit()

    # delete
    db.session.delete(user)

    # 对于查询语句还有更多的操作, 我会放在另一篇文章中描述
```
