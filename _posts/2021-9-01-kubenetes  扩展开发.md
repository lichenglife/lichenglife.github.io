---
layout:     post
title:     kubernets扩展开发
subtitle:  kubernets扩展开发
date:       2021-09-01
author:     LI
header-img: img/kubernetes.png
catalog: true
tags:
    - 源码
    - kubernetes

---

## 一 、kubernetes  扩展开发点

kuernetes 系统从设计上支持很多层面的扩展开发，开发者在使用kubernets 时，如果不满足生产使用条件，可以进行自定义扩展，目前社区已经有很多kubernetes 的扩展开发实践。如果想了解并上手kubernets开发，需要对kubernetes 编程非常熟悉。本文仅记录kubernetes 扩展开发的一些基础知识。

下面这张图是kubernetes 官网Extension Points

![](https://lichenglife.github.io/img/extension-points.png)

​                                     https://kubernetes.io/docs/concepts/extend-kubernetes/



kubernetes  系统扩展点：

1. kubectl 插件开发
2. API 服务扩展开发
3. 自定义资源CR
4. 调度器扩展开发，自定义调度器
5. 自定义控制器
6. 网络插件cni 
7. 存储插件csi





##  二 、client-go 源码分析



## 三、controller-runtime  解析

[controller-runtime]: https://github.com/kubernetes-sigs/controller-runtime

是一组用于构建kubernetes 控制器的go 库，controller-runtime 封装了client-go 库，使用起来更简单，kubebuilder 和operator 都使用它进行开发控制器。

controller-runtime的源码分析 链接   待完成

##  四、kubebuilder 介绍

[Kubebuilder](https://github.com/kubernetes-sigs/kubebuilder) 是一个使用 CRDs 构建 K8s API 的 SDK，主要是：

- 提供脚手架工具初始化 CRDs 工程，自动生成 boilerplate 代码和配置；
- 提供代码库封装底层的 K8s go-client；

### 1. kubebuilder 架构图
下面这张图是来自kubuilder官网的架构图https://book.kubebuilder.io/architecture.html

![](https://lichenglife.github.io/img/architect-kubebuilder.svg)

- Process:   控制器运行后的主进程，一般自定义控制器中只有一个，可以采用高可用，通过main.go文件进行启动
- Manager :  控制器中的管理器，作为核心组件一个进程中会有一个，主要做以下工作：
  - Metrics:   Metrics暴露
  - 加载配置参数，包括webhook证书、kubernets config等
  - 初始化全局共享的  Cache
  - 初始化全局的clients用于和APIServer的通信
  - 启动所有controller，manager可以管理多个controller
  - 启动所有的webhook,每个webhook 作为http server 的handler 进行注册
- Client:  全局的kubernetes 客户端，在controller的 Reconcile 中，可以通过Client与APIServer 进行通信对自定义资源进行  创建、更新、删除操作
- Cache:  负责同步Controller 关心的资源，通过List And  Get 操作，将Controller关心的资源缓存到本地，本质是 通过Informer 机制获取APIServer 资源
- Controller: 一个 Manager 可以管理多个Controller, Controller 实现 Reconcile接口，APIServer 与自定义控制器之间进行事件同步，Controller 监听到事件Event 后，进行Reconcile逻辑。同时可以添加Predicate 进行事件过滤，过滤掉不关心的事件。
- Reconcile:  Reconcile 通过事件驱动+ 定时同步=无限循环的机制(Reconcile loop)，不停监听资源的实际状态与期望状态，将期待状态修改为实际状态。基本流程：
  - 用户发起期望状态（YAML文件定义状态）
  -  apiserver 将期待状态写入etcd 
  - apiserver以事件形式将资源的状态变化通知所有监听者
  - 控制器作为监听者（由client-go informer 机制实现），监听到事件，并在Reconcile中将资源对象的实际状态修改为期待状态，控制器通过client-go  与 apiserver通讯， 修改etcd 中的资源状态
  - apiserver 通知其他组件进行实施
- Webhook :   kubebuilder 实现 webhookserver ，开发者可以自定义逻辑实现Handler 注册至 http 处理链，实现对资源的校验与修改
-  Scheme:  Scheme 定义了资源序列化和反序列化的方法以及资源类型和版本的对应关系，其是一个内存型的资源注册表 ，这张表记录了GVK --> （Type）go Struct 的相互映射。自定义资源需要通过Scheme相关注册接口进行注册.


### 2. kubebuilder与kubernets 的交互

下面这张图来自网上，可以通过这张图看出，kubebuilder 开发的控制器如何让与apiserver 进行交互。

![](https://lichenglife.github.io/img/architecture-kubebuilder.drawio.png)



###   3.kubebuilder  实战

见项目实践

## 五、operator 开发



- operator可以用来开发自定义控制器

- operator 目的是控制有状态应用的  部署、编排



