---
title: Flask shell 的便利性
date: 2017-10-10 00:04:46
tags: Flask
---
### Flask 中的shell 工具

1. flask cmd 工具

* 将app 所在的脚本文件设置到环境变量中
```
set FLASK_APP=.\path\app.py
```

* 使用 flask run 可以直接运行flask 提供的开发服务器
```
flask run 
-h "host"
-p port
--cert path 用来使用https协议的证书路径
```
另一个好用的工具就是 routes
* flask routes 可以得到项目的所有路由信息
```
flask routes
```

* flask shell 用来在命令行完成一些测试型的操作, 比如获取模型实例, 或者执行一小段flask程序代码
```
flask shell
```

1. flask cli
我们在使用migrate 的时候, 会发现安装扩展之后提供migrate命令, 而这个命令是怎么加入的呢
```python
import click
from flask import Flask


app = Flask(__name__)


@app.cli.command("create_user")
@click.argument("name")
def create_user(name):
    
    print(name)

```
通过app.cli.command 装饰完成命令添加, 参数由click处理. 注意需要设置FLASK_APP环境变量, 不然无法成功.
最后善用工具你的效率会变得比别人快的多