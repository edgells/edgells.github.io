---
title: flask-sqlalchemy-复杂查询
date: 2017-10-11 16:41:48
tags: Flask, Sqlalchemy
---
## Flask Sqlalchemy 构造复杂查询

```python
from flask import Flask
from flask_sqlalchemy import Sqlalchemy

# config...

app = Flask(__name__)
db = Sqlalchemy(app)


```
