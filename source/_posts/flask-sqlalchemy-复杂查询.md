---
title: flask-sqlalchemy-复杂查询
date: 2017-10-11 16:41:48
tags: Flask, Sqlalchemy
---
#### ***本文章内容归作者所有, 如需转载请注明来源***
## Flask Sqlalchemy 构造复杂查询

在日常的开发中难免会使用sqlalchemy 写一些复杂的sql语句。 而在sqlalchemy中查询的方法大体分为两种。
1. 使用模型类查询
2. 使用db.session 查询
两种方式都需要根据业务来做选择， 比如我只需要根据id查模型肯定第一种方式比较效率一点。 但在orm中需要认识到几个概念
* 查询器
    sqlalchemy 原生提供的查询器:
        * first 获取数据集的第一条数据
        * get   通过id 获取数据
        * all   获取所有数据
        
* 过滤器
        * filter_by 只支持字段参数
        * filter   只支持sql表达式
*         
两者最大的区别在于第二种支持丰富的sql构造
filter 处理复杂的语句
```python
# 条件查询
Model.query.filter(Model.field == 'value').first()      # 注意查询需要通过查询器触发

# and 查询
Model.query.filter(MOdel.field == 'value', Model.field1 == 'value1').first()
# or 查询
Model.query.filter(or_(Model.field=='value', Model.name.endswith('value1'))).all()
# not 查询
Model.query.filter(not_(Model.field=='value')).first()

# limit 使用
Model.query.limit(3).all()  # 获取所有数据的前三条数据
# offset 使用
Model.query.limit(3).offset(2).all() #获取所有数据中 从第二条数据开始的三条数据

# order 使用
Model.query.order_by(Model.id.desc()).all() # 对所有数据以id降序排列
Model.query.order_by(Model.id.asc()).all() # 对所有数据以id升序排列

# 模糊查询
Model.query(Model).filter(
#     # Model.field.startswith('eds'),  
#     # Model.field.endswith('1e'),     
#     # Model.field.contains('l')
     Model.field.like("__d%")
).all()

# 
```
