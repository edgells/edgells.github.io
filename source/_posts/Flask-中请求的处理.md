---
title: Flask处理请求
date: 2017-10-10 22:43:23
tags: Flask
---


##### Flask 中处理请求

在常见的web开发后端中, 我们一般都需要围绕请求和响应来处理业务逻辑.
哪么我们就需要对http请求和响应进行处理, Flask给我们提供了一系列的工具函数

* request
    request 是Flask 对http请求信息的封装, 目的就是让我们很便利的访问http请求信息
    常见的请求参数, 请求头, 请求体数据, 上传文件... 等等场景下, 实现快速实现业务.
    
* session
    在服务端保存用户凭证的技术, 通过在cookie 中存放对应的sessionId 与服务器上的session对应
    主要用来保存用户状态, 在Flask-session 扩展中实现了不同存储后端, 比如redis, db, file.
    
* jsonify
    对响应数据进行json格式化, 能够序列化json所支持的数据类型

* make_response
    内部其实是一个多分支判断, 但主要功能是生成响应对象
    
总结:
    通过上面Flask 所提供的工具函数, 我们可以快速完成业务. 但是也存在一些问题:
    
* 对不支持json的类型, Flask并没有处理, 还需要我们自己手动处理.
主要在jsonEncode和jsonDecode这一块.

* 需要自己封装验证逻辑
* 语法太过灵活, 会让新手很选择
