---
title: OS-Camp-2024 Part-1 of plus7wist
date: 2024-05-02 12:42:03
categories:
    - 2024春夏季开源操作系统训练营
tags:
    - author:plus7wist
---

# 总结

这是我（plus7wist）在 Os-Camp 2024 的第一阶段的总结。

## 构建系统

我有一定的 Rust 基础，大概几年前也做过一次 rustlings 的练习。但这次练习里有包含 build script 的部分，我印象里从前那次是没有做过的。

Rust 的 build script 从它的环境变量获取包的信息，将要写出的设置写到标准输出。这很好地完成了一种插件设计。

进程之间通信的基本方式有如下一些：

1. 命令行参数。
1. 环境变量。
1. 共享内存。
1. 文件：
   1. 标准输入和输出。
   1. 命名管道或者其他设备文件。
   1. 网络。

这其中使用环境变量是最直接简单的。如果传递给插件系统的数据并不复杂，那么这种做法就是最好的。它不需要插件程序要做出很多的解析。

可以从几个方面来评判这些方式。

1. 数据的复杂性。传递给进程的数据越复杂，就越需要某些协议，因此接收放需要解析这种协议。基于文件的方案需要的解析往往最复杂，命令行参数次之，环境变量最简单。

1. 可靠性。基于文件的消息传递都要处理 IO 错误，这往往都是很复杂的。而获取命令行参数和环境变量则少有错误。

1. 一次和多次。如果要向插件随着时序多次传递数据，命令行参数和环境变量都不合适了。这种情况选择文件，特别是标准输入文件是最常见的。

1. 通信效率。仅在效率真的非常重要的场景，人们才会考虑共享内存。

插件系统，特别是 Rust 的构建系统需要的插件，并不需要多次通信，输入数据也不复杂，和插件通信也并不是效率瓶颈，因此环境变量输入确实非常合适。

而 Language Server 这样的软件设计上则采取标准输入和输出来通信，从上面的分析来看，这也是很合适的。

最后通过共享内存通信的软件体系，一般会使用某种数据分发服务（DDS）来隐藏解析过程。

## 算法

OS-Camp 的 rustlings 修改版本上增加了一些算法题目。显然它们的质量不高，只是作为一种实现算法的示例而已。
