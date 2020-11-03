---
title: python包打包分发
date: 2018-09-03 20:53:11
tags: build
---
#### ***本文章内容归作者所有, 如需转载请注明来源***
# python 包的打包与构建
由于python的源码都是开源的形式, 而有的时候我们在传输文件的时候又希望直接传递一个文件就可以.
或者将我们自己编写的python分发给其他人使用, 这个时候我们就需要对自己编写的python 包进行build打包
具体操作如下
1. 在项目目录下建立文件:
```
packaging_tutorial
├── LICENSE
├── README.md
├── example_pkg
│   └── __init__.py
├── setup.py
└── tests
```
* license 用来指明这个包遵循什么样的开源许可证
* readme 告诉别人这个包的一些信息
* example_pkg 则是这个包的实际代码
* test 是这个包的test用例代码
* setup.py 用来build 包的脚本

在整个build过程中我们只需要编写setup.py 中的一些信息即可
```python
from setuptools import setup
# setup
setup(
    name='sample',   # 用于在pypi上面显示的名字
    version='1.2.0',    # 项目的当前版本
    description='A sample Python project',
    long_description='',
    long_description_content_type='text/x-rst', 
    url='https://github.com/xxxx/sampleproject',    # 项目的托管地址
    author='A. Random Developer',
    author_email='author@example.com',
    license='MIT',
    keywords='sample setuptools development', #用于此项目的搜索关键字
    install_requires=['peppercorn'],    # 此项目的依赖
    python_requires='>=3',          # 支持的python 版本
    # 还有许多暂时用不到的选项...
)

```

* 然后通过python 执行setup, 就会在当前工程下, dist目录创建一个tar格式的压缩包.
```
python setup.py sdist
```
* 如果需要打包成wheel
```
pip install wheel
python setup.py bdist_wheel
```
