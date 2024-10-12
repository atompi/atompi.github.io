---
title: "关于阿童木"
date: 2024-10-12 21:12:23
type: "about"
comments: false
---

---

## <center>联系方式</center>

+ Name: 阿童木
+ Email: coder.atompi@gmail.com
+ Tel: 156****5674
+ GitHub: [atompi](https://github.com/atompi)

---

## <center>技术栈</center>

<center>

|开发语言|DevOps 工具|操作系统|SRE|开发框架|中间件/其他|
|:---:|:---:|:---:|:---:|:---:|:---:|
|Go|Docker|Linux|Prometheus|Gin|Nginx|
|Python|Ansible|AIX|Elastic Stack|Django|Tomcat|
|Shell|Jenkins||Loki|Flask|Redis|
|JavaScript|K8s(Completed CKA Certification)||BPF|Ant Design|MySQL|
||||||Istio|

![CKA](/images/about/cka-ouyangkang.jpg)

</center>

## <center>开源项目</center>

- [**activemq-exporter**](https://github.com/atompi/activemq_exporter) : A Prometheus exporter for ActiveMQ via jolokia API.
- [**pushgatewaybot**](https://github.com/atompi/pushgatewaybot) : Push exporters data to pushgateway.
- [**webhookbot**](https://github.com/atompi/webhookbot) : Integration of Lark-robot for Prometheus Alertmanager via webhook.
- [**budget_exporter**](https://github.com/atompi/budget_exporter) : A metrics exporter to facilitate budget management and monitoring.
- [**PrometheusStack**](https://github.com/atompi/docker-prometheus-stack) : docker-compose 快速部署 Prometheus 相关组件。
- [**Earthling**](https://gitee.com/atompi/Earthling) : docker-compose 快速部署 Elastic Stack相关组件。
- [**losyncd**](https://gitee.com/autom-studio/losyncd) : 文件同步工具，守护进程启动，无限循环执行 rsync 同步指令。
- [**AutoM**](https://github.com/atompi/autom) : Next-generation XOps platform, born to empower the happiness of Site Reliability Engineers (SREs).
- [**grabber**](https://github.com/atompi/grabber) : Grabber is a simple multi-web file parallel download tool.
- [**nginx-exporter**](https://github.com/atompi/nginx_exporter) : Nginx stub_status 指标采集器。
- [**tuping**](https://github.com/atompi/tuping) : A network connectivity testing tool. Supports testing via ICMP, TCP.
- [**culvert**](https://github.com/atompi/culvert) : A tool to quickly open ssh forwarding tunnels.
- [**webdavd**](https://github.com/atompi/webdavd) : A Go WebDav server implementation.
- [**punch**](https://gitee.com/autom-studio/punch) : 一个根据 go template 格式的模板配置文件生成目标配置文件的小工具。
- [**CoreDNS**](https://gitee.com/autom-studio/coredns) : 可构建数据中心内网高可用 DNS 服务器的 coredns 分支。
- [**librarian**](https://gitee.com/autom-studio/librarian) : Gitee 集群内 clone 代码仓库工具，用于在集群内通过 git 协议批量导出仓库。
- [**gitee-exporter**](https://gitee.com/oscstudio/gitee-exporter) : Prometheus Gitee API exporter.

<br>
<br>

## <center>社区活动</center>

- Gitee Talk 讲师 - [Gitee DevOps实战——打造自己的持续集成工作流](https://www.bilibili.com/video/BV1ou411o7tf?share_source=copy_web)

## <center>工作经历</center>

### 2023 年 6 月 1 日 ~ 至今：华润万象生活 - 运维高级主管

> 负责万象生活总部业务系统运维：资源管控、采购管理、云基础设施运维、应急响应；统一运维体系建设、运维标准化建设；运维开发（运维平台、监控组件）。

- 工作内容：华润万象生活智能与数字化部运维组正编。

1. 负责万象生活总部业务系统运维，资源管控、采购管理；
2. 云基础设施：ecs、k8s集群、中间件、数据库；公有云平台：阿里云、腾讯云；DevOps：CI/CD、变更管理、应急响应；
3. 统一运维体系建设：多业务系统统一运维、统一运维组织架构，开发统一运维平台；
4. 运维标准化建设：制定业务系统基础组件“技术选型-引入-生命周期管理”标准规范；制定业务系统交付部署架构标准规范（资源池配置、部署架构、持续交付、子网规划、安全组与防火墙规划）；

- 工作业绩：

1. 一点万象APP负载入口架构改造，实现生产环境无感割接，废除旧技术架构，提升负载入口稳定性及可扩展性；
2. 万象生活停车系统负载入口改造，引入 varnish 实现服务端静态资源缓存，降低业务系统服务器负载（入职第二周在通宵三个晚上后解决了困扰业务系统两个月的问题，再次期间停车系统业务高峰期经常宕机）；
3. 万象生活停车系统云边协同监控、管理体系建设，开发pushgatewaybot（Golang，已开源）解决边缘节点监控指标上报问题；
4. 万象生活FinOps体系建设，基于既有办公系统设计预算管理表，实现“业务规模-资源需求-资源消费记录-财务单元-年度预算及预算余额”管理模型，实现多系统混部场景下业务系统级别财务分账，并通过对业务系统实施“预算余额”限购、“资源评估”流程机制，严格管控业务系统新资源申请；
5. 万象生活业务系统统一可观测体系建设：统一监控平台（Prometheus Stack）、统一日志平台（Elastic Stack）、统一链路追踪平台（SkyWalking）；
6. 运维开发：Zonebot（基于CoreDNS的内网分布式DNS解决方案，开发中）、activemq_exporter（0入侵activemq监控指标采集端，兼容Prometheus指标体系，已开源）、pushgatewaybot（边缘节点监控指标上报云端解决方案，已开源）、grabber（多文件并发下载工具，可作为配置文件同步工具，已开源）、aliyunbot（阿里云API调用cli工具，完善中，已开源）

### 2017 年 2 月 22 日 ~ 2023 年 5 月 31 日：开源中国 - 运维主管

> 生产环境运维团队管理，运维系统方案设计与实施，服务云原生改造及上 Kubernetes 集群部署方案设计与实施；开发环境 CI 流水线设计与实施，生产环境 CD 流水线设计与实施；推动运维体系建设及 SRE 转型。

### 2016 年 3 月 ~ 2017 年 2 月：安徽航天信息科技有限公司 - 研发工程师

> 主要负载服务容器化改造，容器平台构建与维护（服务器端容器可视化管理）；大数据平台运维等工作。

## <center>项目经历</center>

- 生产环境日志系统架构设计及实施
- 生产环境监控系统架构设计及实施，自定义监控采集端开发，云原生监控系统架构设计及实施（基于 Prometheus Operator ）
- 生产环境统一登录系统（基于 Teleport ）
- 生产环境运维平台架构设计及开发实现
- 生产环境 CD 流水线架构设计及实施
- 全服务云原生改造，生产环境云原生架构设计及实施
- 开发环境 CI 流水线架构设计及实施
- 生产环境机房建设，整机房迁移总负责人
- 生产环境中间件、数据库（redis、MySQL）高可用集群架构设计及实施

## <center>生涯规划</center>

- SRE
- 运维开发
- 运维架构
