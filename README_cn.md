# CNStack社区版

## 简介

CNStack社区版（CNStack Community Edition, CNStack CE）是[阿里云云原生Stack（CNStack）](https://cn.aliyun.com/activity/middleware/cnstack)产品家族中的一员。CNStack社区版可以免费下载和使用，支持在有限的资源上进行部署和运行。通过CNStack社区版，用户可以体验ACK敏捷版的大部分能力，以及使用组建中心拉起各种开源软件及中间件，方便用户评估自身的软件系统是否可以正常地运行于CNStack环境中。

最新 release 为 1.1.0 版本，包括容器服务敏捷版的体验功能，后续我们将陆续添加更多产品能力到CNStack社区版中。

敬请期待Roadmap以及更多CNStack社区版功能。

## 产品优势

* 安全可靠：采用 ACK Distro 作为 K8s 基础，核心组件来自公有云ACK并经历了线上、线下众多场景考验
* 轻量方便：最小功能集可以运行在笔记本上。通过 sealer 进行打包和交付，仅需数分钟便可以完成部署
* 体验一致：社区版中ACK敏捷版与商业版ACK敏捷版同源，仅有部分功能有差异。未来会有更多功能开放
* 迭代快速：版本节奏更快，更灵活，可以快速体验新功能
* 兼容适配：通过网络插件[hybridnet](https://github.com/alibaba/hybridnet)和本地存储插件[open-local](https://github.com/alibaba/open-local)，最大限度适配多种基础设施。

## 快速开始

单节点，一键部署最小功能集

```bash
# 获取sealer工具
wget -c "http://sealer.oss-cn-beijing.aliyuncs.com/sealers/sealer-v0.5.2-linux-amd64.tar.gz" && tar -xvf sealer-v0.5.2-linux-amd64.tar.gz -C /usr/bin

# 获取CNStack社区版集群镜像并拉起集群
sealer run registry.cn-qingdao.aliyuncs.com/sealer-apps/cnstack-ce:1.1.0 -m `hostname -i` -p $passwd
```

最小功能集中仅包括容器服务敏捷版的基础能力。请参考[部署指南](./deploy/deploy-guide.md)，通过配置Clusterfile的方式，部署更多高级功能。这些高级功能也可以在管理控制台增量部署。

## 使用手册

根据[部署指南](./deploy/deploy-guide.md)完成部署以后，通过浏览器访问 `$Master_IP_1:30088` 即可以访问CNStack社区版-容器服务敏捷版的控制台（默认用户名：admin，默认密码：Ab123456）。

访问[阿里云-云原生CNStack](https://www.aliyun.com/activity/middleware/cnstack)，获取CNStack更多产品信息。访问[阿里云-云原生CNStack社区版使用手册](https://www.aliyun.com/activity/middleware/cnstack)，获取产品使用手册。

## 依赖组件介绍

* K8s发行版：[ACK Distro](https://github.com/AliyunContainerService/ackdistro)
* 集群部署工具：[sealer](https://github.com/alibaba/sealer)
* 网络插件：[hybridnet](https://github.com/alibaba/hybridnet)
* 本地存储插件：[open-local](https://github.com/alibaba/open-local)

## 参考

* [ACK Distro](https://github.com/AliyunContainerService/ackdistro)
* [阿里云云原生Stack（CNStack）](https://cn.aliyun.com/activity/middleware/cnstack)
