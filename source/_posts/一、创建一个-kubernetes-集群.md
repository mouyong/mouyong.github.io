---
title: 一、创建一个 kubernetes 集群
date: 2019-12-20 14:51:12
categories: k8s
tags:
- k8s
- 运维
english_title: Create-a-kubernetes-cluster
---
[前提摘要](https://kubernetes.io/docs/tutorials/kubernetes-basics/create-cluster/cluster-intro/)

**Minikube 是一个快速搭建单节点 Kubenetes 集群的工具**

1. 查看是安装 minikube 是否正确安装
`minikube version`

2. 启动集群
`minikube start`

3. 查看 kubectl 是否正确安装
`kubectl version`

```
Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.0", GitCommit:"e8462b5b5dc2584fdcd18e6bcfe9f1e4d970a529", GitTreeState:"clean", BuildDate:"2019-06-19T16:32:14Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
```

客户端版本是 kubectl 的版本，服务端版本是 master 节点中 Kubernetes 的版本。

4. 查看集群中的详细信息
`kubectl cluster-info`

5. 查看集群中的节点信息
`kubectl get nodes`
**Ready 状态的节点代表它已经准备好接受要部署的应用程序。**
