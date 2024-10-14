---
title: 我应该使用哪个 Kubernetes apiVersion
date: 2019/12/10
tags: Kubernetes
description: Some old articles may have been deprecated.
---

## 0. Overview

Kubernetes 中的对象定义需要 `apiVersion` 字段。当 Kubernetes 发布（更改其 API 中的某些内容）发行版更新时，将创建一个新的 `apiVersion` 。但是，官方的 Kubernetes 文档几乎没有提供关于 `apiVersion` 的指导。本指南提供了要使用的版本的备忘单，同时介绍了每个版本及其发行时间表。

**PS**：**只列出 v1.10+ 版本**

## 1. kind apiVersion 对应表

|kind|apiVersion
|------
|CertificateSigningRequest|certificates.k8s.io/v1beta1
|ClusterRoleBinding|rbac.authorization.k8s.io/v1
|ClusterRole|rbac.authorization.k8s.io/v1
|ComponentStatus|v1
|ConfigMap|v1
|ControllerRevision|apps/v1
|CronJob|batch/v1beta1
|DaemonSet|apps/v1
|Deployment|apps/v1
|Endpoints|v1
|Event|v1
|HorizontalPodAutoscaler|autoscaling/v1
|Ingress|networking.k8s.io/v1beta1
|Job|batch/v1
|LimitRange|v1
|Namespace|v1
|NetworkPolicy|networking.k8s.io/v1beta1
|Node|v1
|PersistentVolumeClaim|v1
|PersistentVolume|v1
|PodDisruptionBudget|policy/v1beta1
|Pod|v1
|PodSecurityPolicy|policy/v1beta1
|PodTemplate|v1
|ReplicaSet|apps/v1
|ReplicationController|v1
|ResourceQuota|v1
|RoleBinding|rbac.authorization.k8s.io/v1
|Role|rbac.authorization.k8s.io/v1
|Secret|v1
|ServiceAccount|v1
|Service|v1
|StatefulSet|apps/v1

## 2.
