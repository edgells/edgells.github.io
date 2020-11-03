---
title: django中理解mixins
date: 2017-10-22 13:45:01
tags: Django, 设计模式
---
#### ***本文章内容归作者所有, 如需转载请注明来源***
### Django Rest framework 体现的Mixin 模式

刚开始接触 rest framework 时, 有点难以理解. 甚至并没有掌握到这个框架的好处(最大的好处在于,快速撸完接口下班), 甚至因为性能问题没有对这个框架有一个全面的认识.
不过当你之掌握了, python世界的规律了之后你会发现真香. 

Rf 主要提供了视图扩展和数据序列化和反序列化, 两者共同合作撸一个api 简直不要太爽.

先说视图:
* APIView 继承之 django.View 并重写 as_view 方法
    
* GenericAPIView 继承之APIView 并扩展结合序列化类和查询集的通用操作
通过上面两者实现的功能, 你就会发现.APIView 简单扩展了原生View, 对于serializer的结合并不是特别深入.
而GenericAPIView 通过封装了, 多个通用的操作:

* get_queryset(self)
    1. 获取查询集
    
* get_object(self)
    1. 获取过滤后的查询集
    2. 通过设置的查询字段从查询集中通过字段获取model
    3. 检查是否有权限获取当前对象
    
* get_serializer(self)
    1. 获取声明时定义的class
    2. 构造serializer 相关的context
    3. 通过参数实例化serializer_class
    
* get_serializer_context(self)
    1. return 声明时定义的serializer_class
    
* filter_queryset(self)
    1. 根据定义的过滤后端过滤queryset
    
* paginate_queryset
    1. 根据定义的paginate_class 对queryset分页
    
在整个rest framework api 周期内部, 都是围绕serializer 来工作的. 因为它是model 的上层.
应该说是数据源的上层, 官方给serializer 的定位是模型和非模型数据处理. 

哪么现在我动手撸一个创建用户的api

现在我希望用户register的时候, 带上一些我需要的信息,

```python
class RegisterView(View):
    
    def post(self, request, *args, **kwargs):
        # get data
        # check data
        # create user model
        # return response
        return HttpResponse()
```
这是一个正常的Djanog 视图, 现在我扩展一下使用rest

serializer.py
```python
from rest_framework import serializers
from .models import User 
class UserSerializer(serializers.ModelSerialzier):
    password = serializers.CharField(max_length=1024, write_only=True)      # 声明此字段只用做反序列化
    
    class Meta:
        model= User
        fields = '__all__'

```

views.py
```python
from rest_framework import generics
from .serializers import UserSerializer
from .models import User


class RegisterFulView(generics.CreateAPIView,
                        generics.RetrieveAPIView):
    
    lookup_url_kwarg = 'id' # 注意通过api参数传递的参数名称
    serializer_class = UserSerializer
    queryset = User.objects.all()    

    
```

urls.py
```python

# 二级 url
path('/create/', RegisterFulView.as_view())
path('/<int:id>/', RegisterFulView.as_view())   # api参数要和视图中定义的一致

```

最后在urls 中注册路由, 然后你就完成了这个用户注册的接口. 什么这就完成了, 并且我们还完成了用户的信息获取
是不是不可思议, 的确有点扯. 但就是完成了, 借助python的动态特性和mixin模式, 完成了api.
注意我们的参数效验rest在底层帮我们做了, 但是验证规则没有, 所以我们还需要定义验证规则. 这个内容在rest framework 中有提到
##### 总结:
结合rest 实现api时
1. 找一个符合当前业务的GenericAPIView
2. 定义好serializer 和model
3. 定义好指定字段的验证逻辑
4. 注册你的api