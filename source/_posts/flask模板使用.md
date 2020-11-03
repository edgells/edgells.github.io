---
title: flask模板使用
date: 2017-10-10 18:26:17
tags: Flask, template
---
## Flask 模板使用
Flask 模板在jinja2 库之上进行一层封装, 便于在flask 中使用
而我们要想使用jinja2 所提供的完整功能, 可以根据需要查看官方文档. 

```python
from flask import Flask, render_template

app = Flask(__name__)


# 渲染模板
@app.route("/", methods=['GET'])
def index():
    return render_template("index.html")


if __name__ == '__main__':
    app.run()
```
有两个地方要注意
1. 存放模板文件的目录有两种方式
    * 模板与app 所在脚本在同一级目录
    * 模板目录是应用模块子目录
2. 在pycharm 中最好显示声明一下template folder

jinja2 解决了特殊字符转义功能, 这个在对于一些不安全的字符和xss处理上很有帮助. 
下面演示一些常见的模板语法
```jinja2
{{ title }} {# 模板变量使用#}

{#模板语法#}
{% for item in users%}
    {{ item }}
{% endfor %}

{# if 语法#}
{% if item.bool %}
    {# if 语法实现 #}

{% endif%}
```
当然这些还不能体现这个框架和flask 结合的强大之处哪么现在我们来看一下flash() 这个前后端不分离开发中的常用工具
这个工具需要在模板中使用 get_flashed_messages() 获取传递的数据.
```python
from flask import Flask, render_template, flash

app = Flask(__name__)


# 渲染模板
@app.route("/", methods=['GET'])
def index():
    flash("hello flask")
    return render_template("index.html")


if __name__ == '__main__':
    app.run()
```