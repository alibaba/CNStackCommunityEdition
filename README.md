# CNStack社区版

## 简介

CNStack（Cloud Native Stack）是一款开放的企业级云原生技术中台产品。它可以在多云、混合云、本地数据中心，边缘等环境下管理基础设施资源，统一编排，调度工作负载，并提供丰富的云原生全栈能力，包括打造大规模、高性能、业务连续的分布式应用系统，提升企业数字化转型的整体效能。

CNStack 2.0从原先的容器平台产品升级成全栈的云原生技术中台产品。它创建了“云服务”，“云组件”的标准规范和支持这些标准规范的线上集成发布服务[云原生交付平台ADP](https://www.aliyun.com/product/aliware/adp)。“云服务”通过服务的形式提供能力扩展，使用CNStack提供的用户，租户，鉴权，审计，许可证，多集群部署，UI框架等基础能力，与既有能力或其他服务无缝的协作。目前，已发布的云服务包括：多集群管理，分布式应用管理、分布式存储、虚拟化服务、云边协同、服务网格等。更多的云服务会陆续发布。

CNStack 2.0 社区版包括CNStack和多集群管理云服务。除了高可用部署模式外，CNStack 社区版提供了与商业版本一致的能力，包括“云服务”和“云组件”扩展。其他扩展平台能力的“云服务”也会有序放出。

## 产品优势

* 安全可靠：采用 ACK Distro 作为 K8s 基础，核心组件来自公有云ACK并经历了线上、线下众多场景考验
* 轻量方便：最小功能集可以运行在笔记本上。通过 sealer 进行打包和交付，仅需数分钟便可以完成部署
* 体验一致：社区版与商业版同源，仅有高可用部署等部分差异。未来会有更多功能开放
* 完整生态：“云服务”，“云组件”标准规范和线上集成发布服务[云原生交付平台ADP](https://www.aliyun.com/product/aliware/adp)提供灵活，方便的自助能力扩展
* 迭代快速：版本节奏更快，更灵活，可以快速体验新功能
* 兼容适配：通过网络插件[hybridnet](https://github.com/alibaba/hybridnet)和本地存储插件[open-local](https://github.com/alibaba/open-local)，最大限度适配多种基础设施。

## 产品部署

### 最小功能集，一键部署

环境要求：

* 规格：master节点8C16GB，worker节点2C4GB
* 磁盘：根目录50GB以上可用磁盘空间
* OS：CentOS 7.9，Anolis 8.6

#### 单节点，一键部署

```bash
# 获取sealer工具
wget http://ack-a-aecp.oss-cn-hangzhou.aliyuncs.com/ack-distro/sealer/sealer-0.9.1-beta1-linux-amd64.tar.gz -O sealer.tar.gz && tar -xvf sealer.tar.gz -C /usr/bin

# 获取配置文件
wget http://ack-a-aecp.oss-cn-hangzhou.aliyuncs.com/cnstack-ce/clusterfile/cnstack-ce-v2-0-1-ce-4-clusterfile.yaml -O ClusterFile.yaml

sealer run -f ClusterFile.yaml -m `hostname -i` -p $passwd
```

#### 多节点，master节点多IP环境，一键部署：

```bash
# 获取sealer工具
wget http://ack-a-aecp.oss-cn-hangzhou.aliyuncs.com/ack-distro/sealer/sealer-0.9.1-beta1-linux-amd64.tar.gz -O sealer.tar.gz && tar -xvf sealer.tar.gz -C /usr/bin

# 获取配置文件
wget http://ack-a-aecp.oss-cn-hangzhou.aliyuncs.com/cnstack-ce/clusterfile/cnstack-ce-v2-0-1-ce-4-clusterfile.yaml -O ClusterFile.yaml

# 示例IP：192.168.0.1（master内部IP）136.67.0.1（master外部IP）192.168.0.2（worker1）192.168.0.3（worker2）192.168.0.4（worker3）
sealer run -f ClusterFile.yaml -m 192.168.0.1 -n 192.168.0.2,192.168.0.3,192.168.0.4 -p $passwd -e gatewayExternalIP=136.67.0.1 -e ingressExternalIP=136.67.0.1

# 查看App状态
kubectl get app -A

# 等待所有App的状态为Running
```

部署完成后，通过浏览器访问 `$Master_IP_1:30383` 即可以访问CNStack社区版的控制台（默认用户名：`admin` 默认密码：`Ab123456`）。

注意，上述命令安装了CNStack和多集群管理云服务，但是CNStack在各个纳管集群内的日志和监控组件默认没有安装，可以在平台管理的能力中心页面运维CNStack，更改数据面配置安装日志和监控组件。此外，制品管理能力需要对接第三方的[Harbor服务](https://github.com/goharbor)。在CNStack社区版没有包含Harbor服务。

### 通过Clusterfile，实现高级配置

```bash
# 获取sealer工具
wget http://ack-a-aecp.oss-cn-hangzhou.aliyuncs.com/ack-distro/sealer/sealer-0.9.1-beta1-linux-amd64.tar.gz -O sealer.tar.gz && tar -xvf sealer.tar.gz -C /usr/bin
# 获取配置文件
wget http://ack-a-aecp.oss-cn-hangzhou.aliyuncs.com/cnstack-ce/clusterfile/cnstack-ce-v2-0-1-ce-4-clusterfile.yaml -O ClusterFile.yaml
```

默认情况下，CNStack使用csi-hostpath作为其默认存储类，如果想让CNStack更好地管理它使用的磁盘，请按需准备好裸的数据盘（无需分区及挂载）：

* EtcdDevice: 分配给etcd的磁盘，容量必须大于20GiB，IOPS>3300，仅Master节点需要
* StorageDevice: 分配给docker和kubelet的磁盘，容量建议大于200GiB
* DockerRunDiskSize, KubeletRunDiskSize: 详见yaml说明

准备好磁盘后，配置您的ClusterFile.yaml文件

```bash
apiVersion: sealer.cloud/v2
kind: Cluster
metadata:
  name: my-cluster # 固定为my-cluster
spec:
  ...
  env: # all env are NOT necessary
    - Addons=ack-node-problem-detector,kube-prometheus-crds
    - PodCIDR=172.45.0.0/16,5408:4003:10bb:6a01:83b9:6360:c66d:0000/112 # pod subnet, support ipv6 cidr, must be dual stack cidr
    - SvcCIDR=10.96.0.0/16,6408:4003:10bb:6a01:83b9:6360:c66d:0000/112 # service subnet, support ipv6 cidr, must be dual stack cidr
    - EtcdDevice=/dev/vdb # EtcdDevice is device for etcd, default is "", which will use system disk
    - StorageDevice=/dev/vdc # StorageDevice is device for kubelet and container daemon, default is "", which will use system disk
    - YodaDevice=/dev/vdd # YodaDevice is device for open-local, if not specified, open local can't provision pv
    - DockerRunDiskSize=100 # unit is GiB, capacity for /var/lib/docker, default is 100
    - KubeletRunDiskSize=100 # unit is GiB, capacity for /var/lib/kubelet, default is 100
    - ComponentToInstall=logging,monitor # 需要默认安装的组件
    - gatewayExternalIP=${master_eip} # 用于对外暴露CNStack管控服务入口，该地址要能够被您的浏览器访问到
    - ingressExternalIP=${master_eip} # 用于对外暴露CNStack数据服务入口，该地址要能够被您的浏览器访问到
    - gatewayPort=30383 # 对外暴露的CNStack平台管控的端口
    - gatewayAPIServerPort=30384 # 对外暴露的K8s API的端口
    - ingressHttpPort=80 # 对外暴露的CNStack平台数据的端口（HTTP）
    - ingressHttpsPort=443 # 对外暴露的CNStack平台数据的端口（HTTPS）
  ssh:
    passwd: "password"
    #user: root # default is root
    #port: "22" # default is 22
    #pk: /root/.ssh/id_rsa
    #pkPasswd: xxx
  hosts:
    - ips: # support ipv6
        - 1.1.1.1
      roles: [ master ] # add role field to specify the node role
      env: # all env are NOT necessary, rewrite some nodes has different env config
        - EtcdDevice=/dev/vdb
        - StorageDevice=/dev/vde
      # rewrite ssh config if some node has different passwd...
      # ssh:
      #  user: root
      #  passwd: passwd
      #  port: "22"
    - ips: # support ipv6
        - 2.2.2.2
        - 3.3.3.3
        - 4.4.4.4
      roles: [ node ]
```

配置完成ClusterFile.yaml后，执行`sealer run`命令安装CNStack社区版

```bash
# 运行sealer run命令
sealer run -f ClusterFile.yaml 

# 查看App状态
kubectl get app -A

# 等待所有App的状态为Running
```

## 产品卸载

```bash
# 执行sealer delete删除安装的管理集群
sealer delete -a --force
# 如果使用定制安装，启用了卷管理服务，删除平台创建的数据卷
vgremove open-local-pool-0 --force 
```

## 快速开始

* 访问[创建第一个Application](./doc/first-app.md)，了解如何配置平台，配额，完成第一个应用的创建

## 已知问题

* 在资源受限的基础设施，例如笔记本环境，安装平台，服务启动后，用户登陆UI有可能会发现主机等资源并未显示，也没有错误信息显示。这是因为相关服务需要等待若干分钟获取基础设施资源，稍等几分钟资源等信息就会出现。

## 使用手册

* 访问[阿里云-云原生CNStack](https://www.aliyun.com/activity/middleware/cnstack)，获取CNStack更多产品信息。
* 访问CNStack平台内置的在线用户文档

## 依赖组件介绍

* K8s发行版：[ACK Distro](https://github.com/AliyunContainerService/ackdistro)
* 集群部署工具：[sealer](https://github.com/alibaba/sealer)
* 网络插件：[hybridnet](https://github.com/alibaba/hybridnet)
* 本地存储插件：[open-local](https://github.com/alibaba/open-local)

## 纯离线环境的部署

如果部署环境没有互联网连接，请执行以下命令下载安装包

```bash
# 在有互联网连接的主机，使用sealer pull拉取集群镜像和配置文件
sealer pull ack-agility-registry.cn-shanghai.cr.aliyuncs.com/ecp_builder/cnstack-ce:v2-0-1-ce-4
wget http://ack-a-aecp.oss-cn-hangzhou.aliyuncs.com/cnstack-ce/clusterfile/cnstack-ce-v2-0-1-ce-4-clusterfile.yaml -O ClusterFile.yaml

# 保存集群镜像为tar文件
sealer save ack-agility-registry.cn-shanghai.cr.aliyuncs.com/ecp_builder/cnstack-ce:v2-0-1-ce-4 -o cnstack.tar

# 将集群镜像cnstack.tar和配置文件 ClusterFile.yaml传输到没有互联网连接的部署主机，在部署主机执行以下命令
sealer load -i cnstack.tar
```

然后，可以按照产品部署指南部署产品

## 参考

* [ACK Distro](https://github.com/AliyunContainerService/ackdistro)
* [阿里云云原生Stack（CNStack）](https://cn.aliyun.com/activity/middleware/cnstack)
* [云原生交付平台ADP](https://www.aliyun.com/product/aliware/adp)
