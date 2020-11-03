---
title: python包打包分发
date: 2018-09-03 20:53:11
tags: build
---
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


然后通过python 执行setup
```
python setup.py sdist
```
就会在当前工程下, dist目录创建一个tar格式的压缩包.
