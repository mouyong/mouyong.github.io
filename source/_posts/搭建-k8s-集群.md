---
title: 搭建 k8s 集群
date: 2019-12-26 18:42:44
categories: k8s
tags:
  - k8s
  - 环境
english_title: Build-k8s-cluster
---

## 安装

```
# 使得 apt 支持 ssl 传输
sudo apt-get update && sudo apt-get install -y apt-transport-https curl

# 下载 gpg 密钥
sudo curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | sudo apt-key add -

# 添加 k8s 镜像源，需要在 root 权限执行
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF

# 更新源列表
sudo apt-get update

# 下载 kubectl，kubeadm以及 kubelet
sudo apt-get install -y kubelet kubeadm kubectl
```

## 安装 docker 环境

[安装 docker 环境](https://blog.iwnweb.com/%E7%8E%AF%E5%A2%83/Installing-docker-environment/)


## 修改 docker cgroup driver 信息
```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["你的镜像加速地址"],
  "exec-opts": [ "native.cgroupdriver=systemd" ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 初始化 master 节点

### 关闭 swap

- 临时关闭
`swapoff -a`

- 永久生效
```
echo "vm.swappiness = 0" >> /etc/sysctl.conf
sysctl -p
```

### 初始化主节点
```
kubeadm config images pull \
--image-repository registry.aliyuncs.com/google_containers

kubeadm init \
--apiserver-advertise-address=10.8.9.1 \
--image-repository registry.aliyuncs.com/google_containers \
--pod-network-cidr=10.244.0.0/16 \
--ignore-preflight-errors=all
```

## 部署 flannel 网络

```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

## 修改启动配置

`vim /etc/systemd/system/kubelet.service.d/10-kubeadm.conf`
```
Environment="KUBELET_KUBECONFIG_ARGS=--network-plugin=cni --cni-conf-dir=/etc/cni/ --cni-bin-dir=/opt/cni/bin --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf"
```

## master 配置 kubectl 环境

- root

```
mkdir -p /root/.kube && \
cp /etc/kubernetes/admin.conf /root/.kube/config
```

- 普通用户

```
# 添加用户
sudo adduser kube

# 添加 sudo 权限
sudo usermod -aG sudo kube

sudo mkdir -p /home/kube/.kube
sudo cp -i /etc/kubernetes/admin.conf /home/kube/.kube/config
sudo chown -R $(id -u kube):$(id -g kube) /home/kube/.kube
```

## 添加工作节点

```
kubeadm config images pull \
--image-repository registry.aliyuncs.com/google_containers

kubeadm join 10.8.9.1:6443 --token gn6beu.yi356hhss5e5eyqv \
    --discovery-token-ca-cert-hash sha256:a3306fe8e54f2a31e86b9261d4ef6de7150e652e6d440558d193447fed649019 \
    --ignore-preflight-errors=all
```

## Flannel CNI 集成

- 下载 CNI 插件

https://github.com/containernetworking/plugins/releases

```
sudo mkdir -p \
  /etc/cni/net.d \
  /opt/cni/bin \
  /var/lib/kubelet \
  /var/lib/kubernetes \
  /var/run/kubernetes

sudo wget https://github.com/containernetworking/plugins/releases/download/v0.7.1/cni-plugins-amd64-v0.7.1.tgz
sudo tar -xzvf cni-plugins-amd64-v0.7.5.tgz --directory /opt/cni/bin/
```

参考：
[ubuntu 安装 k8s](https://www.jianshu.com/p/f2d4dd4d1fb1)
[kubeadm init 后 master 一直处于 notready 状态](https://uzshare.com/view/797550)
[Flannel网络部署](https://www.cnblogs.com/hwlong/p/9119044.html)