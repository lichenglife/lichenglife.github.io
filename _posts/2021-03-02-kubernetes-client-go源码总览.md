---
layout:     post
title:      kubernetes-client-go源码总览
subtitle:   client-go源码总览
date:       2021-03-02
author:     LI
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - 源码
    - client-go
    - kubernetes
---



## 前言

client-go 使用go语言编写的kubernetes交互客户端库，提供与apiserver的交互，是kubernetes的核心处理框架，熟悉client-go 的源码同样对operator 开发至关重要，本内容为记录学习client-go 的笔记总结，内容为参考网上优秀的分析以及《kubernetes源码剖析》，目的是以写作输出的方式加深对client-go 的理解，以刻意练习形式最终实现掌握熟练开发operator的技术功底。

## 目录结构

目录结构如下：

- 1.discovery:  提供DiscoveryClient  发现客户端
- 2.dynamic : 提供DynamicClient  客户端
- 3.informers ：每种informers的代码实现
- 4.kubernetes: clientSet客户端
- 5.listers: 为每种k8s资源对象提供lister 查询功能
- 6.plugin ： 提供外部插件
- 7.rest: 实现restClient，与K8sApiserver 执行Restful 操作
- 8.scale：对含有实例数replicas的资源对象进行扩容操作
- 9.tools: 其他常用的工具，
- 10.transport ： 提供安全的tcp 连接，同时支持http stream, 支持客户端与容器二进制流交互 exec、attach
- 11.utlils: 提供常用的方法



## client-go 源码总览


![]( https://lichenglife.github.io/img/client-go.png)



根据上图总览，可以将client-go分为以下模块，后面逐一展开解析各个模块的详细代码；

- Reflector:  Reflector 向apiserver watch 特定类型的资源，拿到变更通知后将其添加至DaltaFIFO 队列中，等待进一步的消费处理
- Informer:  Informer 从DaeltaFIFO 中 pop 相应的对象，然后经过Indexer 将对象和索引添加至Cache中，再触发响应的事件处理函数（Resource Event Handler）运行；
- Indexer: Indexer 主要提供一个对象根据一定条件的检索能力，通过namespace/name构造key,通过Thread Safe Store 存储对象
- Workqueue  一般使用延迟队列实现，将监听到的对象的key放入 workqueue中，然后自定义Worker进行消费
- ClientSet ： 与apiserver 交互，实现资源的CRUD
- Resource Event Handler ：对于监听到的事件，我们在Resource Event Handler 中添加过滤条件，将需要关注的对象事件（经过过滤）加入到Workqueue中
- Worker: 我们自己定义的业务处理过程，从workqueue中获取任务，通过ClientSet 实现与apiserver 交互