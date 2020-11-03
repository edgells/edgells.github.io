---
title: rest fm data validator
date: 2017-6-10 16:46:14
tags: rest, validator
---
### Rest Framework 参数效验

在Django 和 RestFramework结合中, 我们需要定义一个serializer用来处理序列化和反序列化数据
而在对数据的验证, 需要在serializer 中定义每个验证函数内部应该只有两种结果,

1. 成功正常 return value
2. 失败使用 serializers.ValidationError(message) 抛出错误信息到上层, 并返回响应


```python
from rest_framework import serializser

class XXXSerializer(serializser.Serializer):
    id = serializser.IntegerField()    
    username = serializser.CharField(max_length=1024)

    class Meta:
        fields = ['id', 'username', 'password',...]

    # 验证方式一
    def validate_fieldname(self, value):
        """验证指定field 是否符合规定"""
        pass

    # 验证方式二
    def validate(self, attrs):
        # attrs 包含了前端传递的参数, 是一个类dict
        pass

# 第三种
def validator_username(value):
    """
        定义的函数要在定义field时, 通过field 选项, validators指定, 以list形式传入
    """     
```
一般在验证数据之后, 就需要对数据进行处理. 而serializer 提供两个实现方法供我们实现. 
##### 如果不是模型类 serializer 需要自己在方法中调用模型类完成数据库操作
* create(validated_data) validated_data 为验证成功后的数据
* update(instance, validated_data) instance 为对应的实例对象, validated_data 同上

两者区别在, create 由于数据创建, 此时并不需要数据对象, 数据传递给data接收
update 使用时, 需要将数据对象传递, 并传入更新的字段值, 数据传递 instance=model_obj, data=update_data

serializer().is_valid() 验证数据, 结果返回 True/False   
如果 is_valid 传递 raise_exception=True 参数时, 异常将会抛出到调用方由调用方处理

serializer().save()     保存数据
serializer.data         获取序列化后的数据

