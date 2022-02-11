# CNStack Community Edition

## Introduction

CNStack Community Edition (CNStack CE) is a member of the [AliCloud Cloud Native Stack (CNStack)](https://cn.aliyun.com/activity/middleware/cnstack) product family. CNStack Community Edition is free to download, and can be deployed with limited resources. With CNStack Community Edition, users can explore most of the capabilities of ACK Agility, as well as use the application catalog to pull up various open-source software and middleware. CNStack CE makes it easy for users to evaluate whether their application can run properly in a CNStack environment.

The latest release version is 1.1.0, which includes the trial version of ACK Agility, and more features will be added to CNStack Community Edition.

Please look forward to Roadmap and more CNStack Community Edition functions.

## Features

### Security & Reliability

Using ACK Distro as K8s foundation, components come from AliCloud ACK and have been tested in both online and offline scenarios. 

### Lightweight and convenient

A minimal function set can run on a laptop. Packaging and delivery can be deployed in minutes via sealer.

### Experience Consistency

The published ACK Agility version includes the same features as in the commercial version, and only partial functions are disabled. More features will be enabled on Community Edition, please stay tuned.

### Fast iteration

Faster pacing version enables more flexibility to adapt new features

### IaaS Compatibility

Maximum adaptation to heterogeneous infrastructures through the network plugin [hybridnet](https://github.com/alibaba/hybridnet) and the local storage plugin [open-local](https://github.com/alibaba/open-local).

## Product deployment

### Minimal function set, one-click deployment

Single-node, one-click deployment

Environmental requirements：

- CPU/Memory: Recommend 8C12GB or larger
- Disk: 50GB or more available disk space in the root directory
- OS: CentOS 7.8 or above, CentOS 7.8 is recommended

```bash
# get sealer
wget -c "http://sealer.oss-cn-beijing.aliyuncs.com/sealers/sealer-v0.5.2-linux-amd64.tar.gz" && tar -xvf sealer-v0.5.2-linux-amd64.tar.gz -C /usr/bin

sealer run registry.cn-qingdao.aliyuncs.com/sealer-apps/cnstack-ce:1.1.0 -m `hostname -i` -p $passwd
```

Multi-node, configured IP parameters, one-click deployment：

```bash
# get sealer
wget -c "http://sealer.oss-cn-beijing.aliyuncs.com/sealers/sealer-v0.5.2-linux-amd64.tar.gz" && tar -xvf sealer-v0.5.2-linux-amd64.tar.gz -C /usr/bin

# example: 192.168.0.1(master) 192.168.0.2(worker) 192.168.0.3(worker) 192.168.0.4(worker)
sealer run registry.cn-qingdao.aliyuncs.com/sealer-apps/cnstack-ce:1.1.0 -m 192.168.0.1 -n 192.168.0.2,192.168.0.3,192.168.0.4 -p $passwd
```

After the deployment is completed, you can access the console of CNStack Community Edition - ACK Agility by accessing `$Master_IP_1:30088` via browser (default username: `admin` default password: `Ab123456`).

Note that after the above deployment, only the basic capabilities of ACK Agility are included. For more advanced features, please deploy a cluster with more advanced features by configuring `Clusterfile` below.

### Advanced configuration via Clusterfile

#### Add data disks, enable [open-local](https://github.com/alibaba/open-local) and more cluster components

Environmental requirements：

- Specifications: Recommend 16C32GB 
- Disk: One system disk + one data disk; 50GB for system disk and 50GB for data disk
- OS: CentOS 7.8 or above, CentOS 7.8 is recommended

Download the [Clusterfile](https://github.com/alibaba/CNStackCommunityEdition/blob/main/deploy/Clusterfile) locally and edit it, paying particular attention to the following parameters:

- Search for `$MASTER_IP` and `$WORKER_IP` respectively, and replace IP according to the actual deployment environment
- $PASSWD
- Search for `VG_DEV=#DataDiskDeviceName#`, replace `#DataDiskDeviceName#`, for example: `VG_DEV=/dev/vdb`
          -`#DataDiskDeviceName#` is the device name of your data disk, you can check it by `lsblk -p`
          -If you have multiple nodes, all nodes need to have the same name of data disk device
- After the above changes, the monitoring, logging, and hpa ( horizontal pod scaling ) features are enabled in Clusterfile by default
          -(Optional) Run `cat Clusterfile | grep -n -e MASTER -e WORKER -e DataDiskDeviceName -e PASSWD` to verify if the Clusterfile has been replaced.

Run:

```bash
# get sealer
wget -c "http://sealer.oss-cn-beijing.aliyuncs.com/sealers/sealer-v0.5.2-linux-amd64.tar.gz" && tar -xvf sealer-v0.5.2-linux-amd64.tar.gz -C /usr/bin

# deploy
sealer apply -f Clusterfile
```

## Getting started

- Visit [Create First Application](https://github.com/tamerga/CNStackCommunityEdition/blob/main/doc/first-app.md) to learn how to configure the platform, resource quotas, tenants, and complete the creation of your first application.

## Known Issues

- In resource-constrained infrastructures, such as laptop environments, installation platforms, and service startups, it is possible that users logging into the UI may find that resources such as hosts are not displayed, and no error messages are displayed. This is because the service in question needs to wait for a number of minutes to obtain the infrastructure resources, and the information such as resources will appear after waiting for a few minutes.
- `kubectl top node` fails. This is a bug already reported in ACK Distro, please refer to [link](https://github.com/AliyunContainerService/ackdistro/issues/16#issuecomment-1035844104) for workround. We will try to fix this soon.

## Uninstall & Clean up

```bash
# delete cluster
sealer delete -a --force
# If you are using customized installation with Clusterfile above, volume group is enabled, here is the removal script
vgremove open-local-pool-0 --force 
```

## User-guide
Visit [AliCloud-Cloud Native CNStack](https://www.aliyun.com/activity/middleware/cnstack) for more information on CNStack products.
Visit [AliCloud - Cloud Native CNStack Community Edition User-guide](https://apsarastackdocument.oss-cn-hangzhou.aliyuncs.com/12_ApsaraACK/%E7%A4%BE%E5%8C%BA%E7%89%88/v1.1.0/%E9%98%BF%E9%87%8C%E4%BA%91%20CNStack%20V1.1.0%20%E7%A4%BE%E5%8C%BA%E7%89%88%20%E7%94%A8%E6%88%B7%E6%8C%87%E5%8D%97%2020220110.pdf?spm=a2c4g.14484272.agile.29&file=%E9%98%BF%E9%87%8C%E4%BA%91%20CNStack%20V1.1.0%20%E7%A4%BE%E5%8C%BA%E7%89%88%20%E7%94%A8%E6%88%B7%E6%8C%87%E5%8D%97%2020220110.pdf) for product manuals.

## List of Dependent Components

- K8s distribution: [ACK Distro](https://github.com/AliyunContainerService/ackdistro)
- Cluster deployment tool: [sealer](https://github.com/alibaba/sealer)
- Network Plugin: [hybridnet](https://github.com/alibaba/hybridnet)
- Local Storage Plugin: [open-local](https://github.com/alibaba/open-local)

## Reference

- [ACK Distro](https://github.com/AliyunContainerService/ackdistro)
- [AliCloud Cloud Native Stack (CNStack)](https://cn.aliyun.com/activity/middleware/cnstack)
