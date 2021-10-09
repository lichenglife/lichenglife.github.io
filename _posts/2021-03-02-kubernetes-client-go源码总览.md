---
layout:     post
title:      kubernetes-client-go源码总览
subtitle:   client-go源码总览
date:       2021-10-08
author:     LI
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - client-go
---



## client-go 源码总览



![](img/client-go.png)

![](C:\Users\Lenovo\Documents\GitHub\lichenglife.github.io\img\client-go.png)

根据上图总览，可以将client-go分为以下模块，后面逐一展开解析各个模块的详细代码；

- Reflector:  Reflector 向apiserver watch 特定类型的资源，拿到变更通知后将其添加至DaltaFIFO 队列中，等待进一步的消费处理
- Informer:  Informer 从DaeltaFIFO 中 pop 相应的对象，然后经过Indexer 将对象和索引添加至Cache中，再触发响应的事件处理函数（Resource Event Handler）运行；
- Indexer: Indexer 主要提供一个对象根据一定条件的检索能力，通过namespace/name构造key,通过Thread Safe Store 存储对象
- Workqueue  一般使用延迟队列实现，将监听到的对象的key放入 workqueue中，然后自定义Worker进行消费
- ClientSet ： 与apiserver 交互，实现资源的CRUD
- Resource Event Handler ：对于监听到的事件，我们在Resource Event Handler 中添加过滤条件，将需要关注的对象事件（经过过滤）加入到Workqueue中
- Worker: 我们自己定义的业务处理过程，从workqueue中获取任务，通过ClientSet 实现与apiserver 交互