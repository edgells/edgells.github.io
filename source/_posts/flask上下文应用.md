---
title: flask 上下文应用
date: 2017-10-11 17:53:41
tags:
---
### Flask 上下文的应用
在flask 中存在两个上下文, request_context, app_context. 在项目中我们经常会用到. 哪么它们的底层原理是什么样的呢?
答案在Flask.wsgi_app函数的源码中, 描述的很清楚. 当一个请求进入时, 会为这个请求创建一个request_context, 并将其推入stack 中. 
![flask_request_push_stack](/images/flask_request_push_stack.png)

而 request_context 在push 函数里面, 可以清除的看到会先判断当前 _app_ctx_stack 栈顶是否存在当前app_context, 如果不存在就会创建一个app_context 并push到栈顶.
哪么一个简单流程就是

1. request -> app 时, create request context
2. request_context.push()
3. request_context.push 时会判断当前应用栈顶是否存在当前的应用上下文, 如果不存在就会创建并压入栈中.
4. 执行整request_context push 操作
5. 处理当前 request ctx 中session
6. match_request() 函数url请求

哪么 flask 为什么要使用这种方式来处理请求流程呢. 结合 flask.global 中的代码来解释就说的通了
```python
# context locals
_request_ctx_stack = LocalStack()
_app_ctx_stack = LocalStack()
current_app = LocalProxy(_find_app)
request = LocalProxy(partial(_lookup_req_object, "request"))
session = LocalProxy(partial(_lookup_req_object, "session"))
g = LocalProxy(partial(_lookup_app_object, "g"))

``` 
在全局范围内有, 一个 _request_ctx_stack, _app_ctx_stack, 它们分别存储了当前请求的上下文环境. 哪么根据flask 支持的服务器并发模型, 主线程创建新连接, 子线程处理新连接.
哪么势必有一个问题, 就是多线程的线程安全问题. 而flask 通过一个线程安全的LocalStack() 来隔离每个线程的上下文环境, 换句话讲就是每个线程有自己单独的数据存储区域. 而在LocalStack中
使用了dict 并使用 threadId/greenletId 作为key, 存储不同线程的上下文环境. 注意这个地方才是最巧妙的. 也就是说Flask 在设计的时候就想到了可能需要通过greenlet方式实现并发, 哪么也就是说
使用greenlet 作为底层支持的库, Flask都可以使用.
所以经常在部署flask 的时候推荐, Gunicorn 使用 eventlet 或者 gevent 作为worker_class. 但是对于一些底层使用 c 编写的扩展, 对于greenlet 支持有限, 所以flask 生态对于并发可选择又不多.
往往我们可能需要重新造轮子.

哪么回到上下文的地方来, 还有另一个好处就是, 对于当前的request 对象和app 对象, 可以在请求周期的任何地方出现, 哪么在编写非视图函数的工具类当中, 直接引入request 和 current_app, 就是能够对当前请求的
上下文做操作. 不得不说, 这样设计的巧妙. 但是,需要注意这两个对象都是通过代理模式拿到的对象, 如果需要对实际的对象做操作需要通过 对象的 _get_current_object实例方法获取当前的对象.
哪么session 对象和 g 对象的作用就不在详解了.