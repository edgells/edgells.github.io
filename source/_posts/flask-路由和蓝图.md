---
title: Flask 路由和蓝图
date: 2017-10-10 16:45:50
tags: Flask, route, Blueprint
---
### Flask - Route - Blueprint

在 flask 中创建路由是一件简单事情
```python
from flask import Flask, url_for


# app 本质也可以看成一个蓝图
app = Flask(__name__)

# 定义路由
@app.route('/', methods=['GET'])
def index():
    return "flask world"

# 方式二
app.add_url_rule('/', 'index', index)   # 注意这种方式的endpoint 需要自己手动提供

# route 内部做几件事情
# 1. 根据参数, 决定这个函数的url路径, 并将这个url添加到 当前蓝图的url_map中
# 2. 同时创建一个endpoint, 如果参数没有传递, 就将函数名作为endpoint, 好处就是当endpoint 作为函数id时, 在使用反向生成url会特别轻松
# 3. url访问的流程就是 url -> app -> endpoint -> view_func

url = url_for('.index')     # 当前蓝图下面的index函数的url

```

而flask 中的蓝图使用
```python
from flask import Flask, Blueprint

app = Flask(__name__)

user = Blueprint('user', __name__, url_prefix='/user')


@user.route("/info/", methods=['GET'])
def info():
    return "user info"


# 注意蓝图注册一定要在装饰函数之后, 不然蓝图没有装载函数, /user/info/ 就404错误了
# 这里向 app 内部 blueprints 一个字典, 存储蓝图并将其 append,app._blueprint_order列表中. 
app.register_blueprint(user)

if __name__ == '__main__':
    app.run()

```
当一个请求进入的时候, url 会根据 _blueprint_order中的蓝图顺序进行匹配, 然后在blueprints找到对应得一级路由. 在创建蓝图时设置的 url_prefix, 在根据url 找到对应的endpoint 进入视图函数处理
整个流程就是

    request -> app -> blueprints -> blue -> endpoint -> view_func