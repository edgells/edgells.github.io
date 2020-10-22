---
title: django中理解mixins
date: 2020-10-22 13:45:01
tags: Django, 设计模式
---

### Django Rest framework 体现的Mixin 模式

刚开始接触 rest framework 时, 有点难以理解. 甚至并没有掌握到这个框架的好处(最大的好处在于,快速撸完接口下班), 甚至因为性能问题没有对这个框架有一个全面的认识.
不过当你之掌握了, python世界的规律了之后, 你会发现真香. 

Rf 主要提供了视图扩展和数据序列化和反序列化, 两者共同合作撸一个api 简直不要太爽.

先说视图:
* APIView 继承之 django.View 并重写 as_view 方法
    
* GenericAPIView 继承之APIView 并扩展结合序列化类和查询集的通用操作
    