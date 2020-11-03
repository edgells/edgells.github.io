---
title: Flask 的Hello World
date: 2017-10-10 22:49:26
tags: Flask
---
#### ***本文章内容归作者所有, 如需转载请注明来源***
* Flask 是一个python世界的微框架, 由于python语言的web服务器一般都是和web框架分离的.
  现在就来看看它有多么微

* pip install flask  
* 创建一个python 脚本
```python
from flask import Flask, jsonify

# 实例化 Flask app 对象 
app = Flask(__name__)

# 通过app的route 装饰器将函数变成视图函数, 主要的参数
# url -> /
@app.route("/", methods=['GET'])    # UrlPath, HttpMethod
def index():
    # 我们知道python函数可以返回多个值, 在调用方会被组包成元组.
    # 这里的返回值格式一般为: str_data, status_code, headers(dict)
    return "Hello FLask", 200

# url -> /json/ | /json(访问这个url时, 会被转发)
@app.route("/json/", methods=['GET'])
def json():
    data = {}
    resp = jsonify(data)    # jsonify 返回一个 Flask Reponse 实例
    resp.status_code = 200  # 设置状态码
    return  resp

```
* 上面的代码就实现了一个简单的api接口, 由于Flask诞生的比较晚, 所以api 也比较现代化.
  它希望大部分情况下, 都当作flask 视图函数都当作api来使用.

* Flask 依赖项:
    1. 提供web开发所需的 Werkzeug wsgi工具箱(提供flask 处理web开发常用的工具)
    2. jinjia2 模板引擎用来渲染模板
* web服务器:
    Gunicorn 部署
    
比起的其它的web框架动则几个十几个的依赖简直不要太爽, 自己学习起来也比较舒服.
