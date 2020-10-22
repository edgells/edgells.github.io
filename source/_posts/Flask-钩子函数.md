---
title: Flask 钩子函数
date: 2017-10-13 22:48:46
tags: Flask 钩子函数
---
#### Flask 钩子函数:

在客户端和服务端网络交互的过程中, 有时需要对交互过程, 或者在交互前后,做一定的处理. 这个时候框架提供的接口就很重要了,
一般我们可能称这些接口为中间件或者事件钩子. 就是像是你在学校想叫个外卖, 然后你又不能出去. 这个时候你就找个钩子放到墙外, 告诉老板, 把东西放在钩子上.
哪么Flask 也为我们提供了一些钩子函数

1. before_first_request
    在第一次请求时触发

2. after_request
    在请求正常处理之后触发
    
3. before_request
    在请求进入视图函数处理之前触发
    
4. teardown_request
    在每次请求之后触发, 处理异常响应
    
使用方式也很简单, 就是装饰器一样

```python
    @app.before_request
    def handler_request():
        pass

```
注意 after_request 和 teardown_request 装饰的函数, 需要定义一个参数用来接收响应, 不然会出现问题.

说完了Flask 提供给的钩子, 哪么我们具体来说一下它们的实际应用场景

before_request 在请求进入视图函数之前做处理, 哪么我们想想, 哪些是需要在处理请求之前要做的事情.

* 验证用户信息
* 验证用户权限
* 验证请求参数
* 甚至为用户request 的添加属性, 或者绑定方法
