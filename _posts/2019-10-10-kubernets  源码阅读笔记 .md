---
layout:     post
title:      kubernets  源码阅读笔记
subtitle:   kubernets  源码阅读笔记
date:       2020-10-09
author:     li
header-img: img/kubernetes.jpg
catalog: true
tags:
    - kubernetes
    - 源码
 
---



# kubernets  源码阅读笔记



### 第一章

1.  kubernets组件在高可用部署下的分布式锁问题，基于Etcd的实践
2.  kubelet 既然实现了  CRO CNI CSI ,那么仔细阅读kubelet源码理论上就可以定制相应的Interface,然后基于企业级的Interface开发
3. kubelet   CRI  ,kubernetes源码 如何做到直接支持不同的容器运行时 ?
4. 关于kubernetes v1.20.4 不再支持docker 后，使用dockershime 安装kuernetes集群的实践https://draveness.me/whys-the-design-kubernetes-deprecate-docker/
5. kube-proxy  作为网络代理，是否与其他的网络代理istio 、mons  、sidecar 功能与实践类似？
6. kubernets 插件的实现，插件的开发？ 圈内如何开发kubernetes   插件？
7. kubernetes   源码中，组件启动的方式高度一致，如何debugkubernetes 代码？  模拟运行环境？
8. cobra command  执行流程？
9. kubernetes  中的代码生成器，go中的代码生成器及其工作原理？
10.  kubernets中使用所有的代码生成器，自定义生成一个自定义组件！！
11. Makefile 文件解读（语法）， golang kubernetes 项目中的Makefile文件解读
12. Golang Annotation 系统 - Gengo 实战  https://cloud.tencent.com/developer/article/1635822
13. k8s.io/gengo项目源码分析   
14.  golang 编译原理 https://blog.51cto.com/onebig/2510835
15.  kubernetes 组件间的grpc交互
16.  kubernetes Scheme 在 实践中的应用
17.  kubernetes 序列化器  、序列化器、编码器、解码器