---
title: Kubernetes 各版本镜像列表
date: 2018/07/30
tags: Kubernetes
description: Some old articles may have been deprecated.
---

**以下镜像列表由 kubeadm v1.11.1 导出，若使用预下载镜像离线部署的方式部署，请使用 kubeadm v1.11.1 版本**

**导出各版本镜像列表（需要科学上网）：**

`kubeadm config images list --kubernetes-version=v1.11.1`

## 插件镜像

- 官方镜像

``` plain
quay.io/coreos/flannel:v0.10.0-amd64
gcr.io/kubernetes-helm/tiller:v2.9.1
k8s.gcr.io/kubernetes-dashboard-amd64:v1.8.3
gcr.io/google_containers/heapster-amd64:v1.5.4
gcr.io/google_containers/heapster-influxdb-amd64:v1.5.2
gcr.io/google_containers/heapster-grafana-amd64:v5.0.4
```
- 阿里云镜像

``` plain
# flannel:v0.10.0-amd64 占位
registry.cn-hangzhou.aliyuncs.com/google_containers/tiller:v2.9.1
registry.cn-hangzhou.aliyuncs.com/google_containers/kubernetes-dashboard-amd64:v1.8.3
registry.cn-hangzhou.aliyuncs.com/google_containers/heapster-amd64:v1.5.4
registry.cn-hangzhou.aliyuncs.com/google_containers/heapster-influxdb-amd64:v1.5.2
registry.cn-hangzhou.aliyuncs.com/google_containers/heapster-grafana-amd64:v5.0.4
```
## 核心镜像

### v1.10.0

- 官方镜像

``` plain
k8s.gcr.io/kube-apiserver-amd64:v1.10.0
k8s.gcr.io/kube-controller-manager-amd64:v1.10.0
k8s.gcr.io/kube-scheduler-amd64:v1.10.0
k8s.gcr.io/kube-proxy-amd64:v1.10.0
k8s.gcr.io/pause:3.1
k8s.gcr.io/etcd-amd64:3.1.12
k8s.gcr.io/coredns:1.1.3
```
- 阿里云镜像

``` plain
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver-amd64:v1.10.0
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager-amd64:v1.10.0
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler-amd64:v1.10.0
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy-amd64:v1.10.0
registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.1
registry.cn-hangzhou.aliyuncs.com/google_containers/etcd-amd64:3.1.12
registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.1.3
```
### v1.10.1

- 官方镜像

``` plain
k8s.gcr.io/kube-apiserver-amd64:v1.10.1
k8s.gcr.io/kube-controller-manager-amd64:v1.10.1
k8s.gcr.io/kube-scheduler-amd64:v1.10.1
k8s.gcr.io/kube-proxy-amd64:v1.10.1
k8s.gcr.io/pause:3.1
k8s.gcr.io/etcd-amd64:3.1.12
k8s.gcr.io/coredns:1.1.3
```
- 阿里云镜像

``` plain
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver-amd64:v1.10.1
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager-amd64:v1.10.1
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler-amd64:v1.10.1
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy-amd64:v1.10.1
registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.1
registry.cn-hangzhou.aliyuncs.com/google_containers/etcd-amd64:3.1.12
registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.1.3
```
### v1.10.2

- 官方镜像

``` plain
k8s.gcr.io/kube-apiserver-amd64:v1.10.2
k8s.gcr.io/kube-controller-manager-amd64:v1.10.2
k8s.gcr.io/kube-scheduler-amd64:v1.10.2
k8s.gcr.io/kube-proxy-amd64:v1.10.2
k8s.gcr.io/pause:3.1
k8s.gcr.io/etcd-amd64:3.1.12
k8s.gcr.io/coredns:1.1.3
```
- 阿里云镜像

``` plain
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver-amd64:v1.10.2
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager-amd64:v1.10.2
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler-amd64:v1.10.2
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy-amd64:v1.10.2
registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.1
registry.cn-hangzhou.aliyuncs.com/google_containers/etcd-amd64:3.1.12
registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.1.3
```
### v1.10.3

- 官方镜像

``` plain
k8s.gcr.io/kube-apiserver-amd64:v1.10.3
k8s.gcr.io/kube-controller-manager-amd64:v1.10.3
k8s.gcr.io/kube-scheduler-amd64:v1.10.3
k8s.gcr.io/kube-proxy-amd64:v1.10.3
k8s.gcr.io/pause:3.1
k8s.gcr.io/etcd-amd64:3.1.12
k8s.gcr.io/coredns:1.1.3
```
- 阿里云镜像

``` plain
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver-amd64:v1.10.3
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager-amd64:v1.10.3
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler-amd64:v1.10.3
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy-amd64:v1.10.3
registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.1
registry.cn-hangzhou.aliyuncs.com/google_containers/etcd-amd64:3.1.12
registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.1.3
```
### v1.10.4

- 官方镜像

``` plain
k8s.gcr.io/kube-apiserver-amd64:v1.10.4
k8s.gcr.io/kube-controller-manager-amd64:v1.10.4
k8s.gcr.io/kube-scheduler-amd64:v1.10.4
k8s.gcr.io/kube-proxy-amd64:v1.10.4
k8s.gcr.io/pause:3.1
k8s.gcr.io/etcd-amd64:3.1.12
k8s.gcr.io/coredns:1.1.3
```
- 阿里云镜像

``` plain
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver-amd64:v1.10.4
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager-amd64:v1.10.4
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler-amd64:v1.10.4
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy-amd64:v1.10.4
registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.1
registry.cn-hangzhou.aliyuncs.com/google_containers/etcd-amd64:3.1.12
registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.1.3
```
### v1.10.5

- 官方镜像

``` plain
k8s.gcr.io/kube-apiserver-amd64:v1.10.5
k8s.gcr.io/kube-controller-manager-amd64:v1.10.5
k8s.gcr.io/kube-scheduler-amd64:v1.10.5
k8s.gcr.io/kube-proxy-amd64:v1.10.5
k8s.gcr.io/pause:3.1
k8s.gcr.io/etcd-amd64:3.1.12
k8s.gcr.io/coredns:1.1.3
```
- 阿里云镜像

``` plain
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver-amd64:v1.10.5
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager-amd64:v1.10.5
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler-amd64:v1.10.5
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy-amd64:v1.10.5
registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.1
registry.cn-hangzhou.aliyuncs.com/google_containers/etcd-amd64:3.1.12
registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.1.3
```
### v1.10.6

- 官方镜像

``` plain
k8s.gcr.io/kube-apiserver-amd64:v1.10.6
k8s.gcr.io/kube-controller-manager-amd64:v1.10.6
k8s.gcr.io/kube-scheduler-amd64:v1.10.6
k8s.gcr.io/kube-proxy-amd64:v1.10.6
k8s.gcr.io/pause:3.1
k8s.gcr.io/etcd-amd64:3.1.12
k8s.gcr.io/coredns:1.1.3
```
- 阿里云镜像

``` plain
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver-amd64:v1.10.6
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager-amd64:v1.10.6
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler-amd64:v1.10.6
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy-amd64:v1.10.6
registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.1
registry.cn-hangzhou.aliyuncs.com/google_containers/etcd-amd64:3.1.12
registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.1.3
```
### v1.11.0

- 官方镜像

``` plain
k8s.gcr.io/kube-apiserver-amd64:v1.11.0
k8s.gcr.io/kube-controller-manager-amd64:v1.11.0
k8s.gcr.io/kube-scheduler-amd64:v1.11.0
k8s.gcr.io/kube-proxy-amd64:v1.11.0
k8s.gcr.io/pause:3.1
k8s.gcr.io/etcd-amd64:3.2.18
k8s.gcr.io/coredns:1.1.3
```
- 阿里云镜像

``` plain
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver-amd64:v1.11.0
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager-amd64:v1.11.0
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler-amd64:v1.11.0
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy-amd64:v1.11.0
registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.1
registry.cn-hangzhou.aliyuncs.com/google_containers/etcd-amd64:3.2.18
registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.1.3
```
### v1.11.1

- 官方镜像

``` plain
k8s.gcr.io/kube-apiserver-amd64:v1.11.1
k8s.gcr.io/kube-controller-manager-amd64:v1.11.1
k8s.gcr.io/kube-scheduler-amd64:v1.11.1
k8s.gcr.io/kube-proxy-amd64:v1.11.1
k8s.gcr.io/pause:3.1
k8s.gcr.io/etcd-amd64:3.2.18
k8s.gcr.io/coredns:1.1.3
```
- 阿里云镜像

``` plain
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver-amd64:v1.11.1
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager-amd64:v1.11.1
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler-amd64:v1.11.1
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy-amd64:v1.11.1
registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.1
registry.cn-hangzhou.aliyuncs.com/google_containers/etcd-amd64:3.2.18
registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.1.3
```
