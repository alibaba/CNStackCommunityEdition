# CNStack社区版

## 简介

CNStack社区版（CNStack Community Edition, CNStack CE）是[阿里云云原生Stack（CNStack）](https://cn.aliyun.com/activity/middleware/cnstack)产品家族中的一员。CNStack社区版可以免费下载和使用，支持在有限的资源上进行部署和运行。通过CNStack社区版，用户可以体验ACK敏捷版的大部分能力，以及使用组建中心拉起各种开源软件及中间件，方便用户评估自身的软件系统是否可以正常地运行于CNStack环境中。

最新 release 为 1.1.0 版本，包括容器服务敏捷版的体验功能，后续我们将陆续添加更多产品能力到CNStack社区版中。

敬请期待Roadmap以及更多CNStack社区版功能。

## 产品优势

* 安全可靠：采用 ACK Distro 作为 K8s 基础，核心组件来自公有云ACK并经历了线上、线下众多场景考验
* 轻量方便：最小功能集可以运行在笔记本上。通过 sealer 进行打包和交付，仅需数分钟便可以完成部署
* 体验一致：社区版中容器服务敏捷版与商业版同源，仅有部分功能有差异。未来会有更多功能开放
* 迭代快速：版本节奏更快，更灵活，可以快速体验新功能
* 兼容适配：通过网络插件[hybridnet](https://github.com/alibaba/hybridnet)和本地存储插件[open-local](https://github.com/alibaba/open-local)，最大限度适配多种基础设施。

## 产品部署

### 最小功能集，一键部署

单节点，一键部署

环境要求：

* 规格：推荐8C12GB
* 磁盘：根目录50GB以上可用磁盘空间
* OS：CentOS 7.8或者以上，推荐CentOS 7.8

```bash
# 获取sealer工具
wget -c "http://sealer.oss-cn-beijing.aliyuncs.com/sealers/sealer-v0.5.2-linux-amd64.tar.gz" && tar -xvf sealer-v0.5.2-linux-amd64.tar.gz -C /usr/bin

sealer run registry.cn-qingdao.aliyuncs.com/sealer-apps/cnstack-ce:1.1.0 -m `hostname -i` -p $passwd
```

多节点，配置IP参数，一键部署：

```bash
# 获取sealer工具
wget -c "http://sealer.oss-cn-beijing.aliyuncs.com/sealers/sealer-v0.5.2-linux-amd64.tar.gz" && tar -xvf sealer-v0.5.2-linux-amd64.tar.gz -C /usr/bin

# 示例IP：192.168.0.1(master) 192.168.0.2(worker) 192.168.0.3(worker) 192.168.0.4(worker)
sealer run registry.cn-qingdao.aliyuncs.com/sealer-apps/cnstack-ce:1.1.0 -m 192.168.0.1 -n 192.168.0.2,192.168.0.3,192.168.0.4 -p $passwd
```

部署完成后，通过浏览器访问 `$Master_IP_1:30088` 即可以访问CNStack社区版-容器服务敏捷版的控制台（默认用户名：`admin` 默认密码：`Ab123456`）。

注意，上述部署完成后，仅包括容器服务敏捷版基础能力，更多高级功能，请通过下面配置 Clusterfile 的方式，部署具备更多高级功能的集群。

### 通过Clusterfile，实现高级配置

#### 增加数据盘，启用 [open-local](https://github.com/alibaba/open-local)和更多集群组件

环境要求：

* 规格：推荐16C32GB
* 磁盘：系统盘+数据盘各一块；系统盘50GB，数据盘50GB
* OS：CentOS 7.8或者以上，推荐CentOS 7.8

下载[Clusterfile](./deploy/Clusterfile)到本地并编辑，特别注意以下参数：

* 分别搜索 `$MASTER_IP` 和 `$WORKER_IP`，根据实际部署环境替换IP
* `$PASSWD`
* 搜索`VG_DEV=#DataDiskDeviceName#`，替换`#DataDiskDeviceName#`，例如：`VG_DEV=/dev/vdb`
  * `#DataDiskDeviceName#` 是你数据盘的设备名，可以通过 `lsblk -p` 查看
  * 如果是多节点，需要所有节点都有同名的数据盘设备
* 完成上述修改后，Clusterfile中默认开启了监控、日志以及弹性伸缩功能
  * （可选）运行 `cat Clusterfile | grep -n -e MASTER -e WORKER -e DataDiskDeviceName -e PASSWD` 确定Clusterfile是否完成了替换

执行：

```bash
# 获取sealer工具
wget -c "http://sealer.oss-cn-beijing.aliyuncs.com/sealers/sealer-v0.5.2-linux-amd64.tar.gz" && tar -xvf sealer-v0.5.2-linux-amd64.tar.gz -C /usr/bin

# 部署集群
sealer apply -f Clusterfile
```

## 快速开始

* 访问[创建第一个Application](./doc/first-app.md)，了解如何配置平台，配额，完成第一个应用的创建

## 已知问题

* 在资源受限的基础设施，例如笔记本环境，安装平台，服务启动后，用户登陆UI有可能会发现主机等资源并未显示，也没有错误信息显示。这是因为相关服务需要等待若干分钟获取基础设施资源，稍等几分钟资源等信息就会出现。

## 清理部署

```bash
# 执行sealer delete删除安装的管理集群
sealer delete -a --force
# 如果使用定制安装，启用了卷管理服务，删除平台创建的数据卷
vgremove open-local-pool-0 --force 
```

## 使用手册

* 访问[阿里云-云原生CNStack](https://www.aliyun.com/activity/middleware/cnstack)，获取CNStack更多产品信息。
* 访问[阿里云-云原生CNStack社区版使用手册](https://apsarastackdocument.oss-cn-hangzhou.aliyuncs.com/12_ApsaraACK/%E7%A4%BE%E5%8C%BA%E7%89%88/v1.1.0/%E9%98%BF%E9%87%8C%E4%BA%91%20CNStack%20V1.1.0%20%E7%A4%BE%E5%8C%BA%E7%89%88%20%E7%94%A8%E6%88%B7%E6%8C%87%E5%8D%97%2020220110.pdf?spm=a2c4g.14484272.agile.29&file=%E9%98%BF%E9%87%8C%E4%BA%91%20CNStack%20V1.1.0%20%E7%A4%BE%E5%8C%BA%E7%89%88%20%E7%94%A8%E6%88%B7%E6%8C%87%E5%8D%97%2020220110.pdf)，获取产品使用手册。

## 依赖组件介绍

* K8s发行版：[ACK Distro](https://github.com/AliyunContainerService/ackdistro)
* 集群部署工具：[sealer](https://github.com/alibaba/sealer)
* 网络插件：[hybridnet](https://github.com/alibaba/hybridnet)
* 本地存储插件：[open-local](https://github.com/alibaba/open-local)

## 参考

* [ACK Distro](https://github.com/AliyunContainerService/ackdistro)
* [阿里云云原生Stack（CNStack）](https://cn.aliyun.com/activity/middleware/cnstack)
