---
title: linux TCP参数说明
date: 2018-8-20 22:57:09
tags: TCP, Linux
---
#### ***本文章内容归作者所有, 如需转载请注明来源***
### Linux TCP 系统平台上的参数, 往往在特殊情况下, 需要根据自身业务进行调整. 这样才能发挥出机器在当前业务下的性能峰值
所以我们需要对这些参数有一定认识:

net.ipv4:
* 基本参数:
    * tcp_syn_retries 内核发送多少个SYN数据包会放弃该链接, 对应TCP 第一次握手阶段
    * tcp_synack_retries  内核发送多少个SYN + ACK, 才会放弃连接
    * tcp_keepalive_time tcp 发送keepalive 数据包频率, 默认2小时.