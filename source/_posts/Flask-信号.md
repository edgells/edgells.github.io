---
title: Flask 信号
date: 2017-10-13 22:48:54
tags: Flask, Signal
---

## Flask 信号机制

在大型的应用中, 随着业务越来越复杂, 代码的耦合读就会上升. 这时候我们就需要一些手段对复杂的业务解耦
根据Flask 框架所提供的能力, 可以通过Signal机制, 简单代码如下

```python
from flask import Flask, jsonify

from blinker import Namespace

# 创建信号
model_signal = Namespace()

model_saved = model_signal.signal('model-signal')

app = Flask(__name__)

app.config['DEBUG'] = True


class LuckyModel:

    def __init__(self):
        self.data = {}

    def save(self, id, name):
        self.data[id] = name
        # 根据每个函数执行的返回值, 可以确定函数的执行状态
        ret = model_saved.send(self, id, name)
        for res, sate in ret:
            # 如果返回值为False 证明函数是执行是失败的
            if not sate:
                res(id, name)


model = LuckyModel()


@model_saved.connect_via(model)
def add_balance(sender, **kwargs):
    print(sender, '\t', **kwargs)
    print("add balance 执行了")


@model_saved.connect_via(model)
def add_log(sender, **kwargs):
    print(sender, '\t', **kwargs)
    print("add log 执行了")


@app.route('/', methods=['GET', ])
def index():
    resp = jsonify({'data': "this is index page"})
    model.save(1, 'edges')
    resp.status_code = 200
    return resp


if __name__ == '__main__':
    app.run()


```
当 model.save() 执行时, model_signal触发, 就会通知订阅的函数, 执行相关操作, 并返回订阅的函数和函数的返回结果.
如果现在有一个订单业务需要在提交之前创建一个订单流水记录,正常的操作是在一个事务中创建一张流水记录, 紧接着创建订单表.
1. transaction begin
2. 创建order
3. insert data
4. update data
5. 创建order invoice
6. insert data
7.transaction end
但是现在我要求对创建订单后还要创建其它的数据呢, 哪么我们又要在代码里面修改吗? 遵循代码设计的开放封闭原则, 对已经完成的代码原则上不应该直接修改.
除非由重构的需要, 哪么这地方引入信号的机制的好处就很明显了.

```python 
# 要在订单之后发送信号, 注意这个地方会返回订阅函数的执行结果和订阅函数
# 所以根据返回结果来决定代码流程.
# 注意第一位位置参数应该是发送者
ret = Signal().send(sender, **kwargs)
```
哪么解决了代码耦合的问题, 对于函数执行失败的操作我们该如何处理, 并且要保证这些操作在一个事务里面. 失败好处理, 在代码流程中捕获并返回给发送者即可.哪么事务呢?
答案在flask_sqlalchemy源码里面, 当我们在request_context中调用的db.session都是一个, 因为request_context, 是在当前app_context 被推入stack中才有的

```python
 def create_scoped_session(self, options=None):
        if options is None:
            options = {}
        
        # _app_ctx_stack 代表着当前应用的上下文, 对于每个请求来讲它是线程安全的, __ident_func__用来识别不同线程
        scopefunc = options.pop('scopefunc', _app_ctx_stack.__ident_func__)
        options.setdefault('query_cls', self.Query)
        # 创建一个安全的session, 并设置scopefunc wei指定的左右域
        return orm.scoped_session(
            self.create_session(options), scopefunc=scopefunc
        )
```
哪么在全局中我们的session是一个的话, 直接调用db.session完成会话上面的数据add, 注意不要commit(), 在发起信号之后进行commit(), 就能够保证订阅函数的数据库操作与
业务函数的数据库操作在一个事务中.
