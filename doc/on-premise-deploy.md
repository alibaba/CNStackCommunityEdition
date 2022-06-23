# 在离线环境部署 CNStack 社区版

唯一要解决的就是 sealer 二进制和 CNStack 社区版 的镜像获取，解决办法如下：

准备：

* 机器A，可以正常联网的 Linux 机器（建议 CentOS 7.8 以上），如ECS
* 机器B，不能联网的离线环境

第一步。在能够联网的机器A上，执行 

```bash
# 获取sealer工具
wget -c "http://sealer.oss-cn-beijing.aliyuncs.com/sealers/sealer-v0.5.2-linux-amd64.tar.gz" && tar -xvf sealer-v0.5.2-linux-amd64.tar.gz -C /usr/bin
```

得到 sealer-v0.5.2-linux-amd64.tar.gz 并将其二进制安装到了机器A上

第二步。在机器A上执行

```bash
# pull image
sealer pull registry.cn-qingdao.aliyuncs.com/sealer-apps/cnstack-ce:1.1.0

# save image to tar.gz
sealer save registry.cn-qingdao.aliyuncs.com/sealer-apps/cnstack-ce:1.1.0 -o image.tar.gz
```

得到 image.tar.gz

第三步。将 image.tar.gz 和  sealer-v0.5.2-linux-amd64.tar.gz  拷贝到不能联网的机器B上

第四步。在机器B执行

```bash
# install sealer
tar -xvf sealer-v0.5.2-linux-amd64.tar.gz -C /usr/bin

# load image
sealer load -i  image.tar.gz

# 验证：下面两个命令都可以看到正常的输出
sealer version

sealer images
```

之后，继续按照 官方的手册，选择（1）直接 sealer run 部署最小功能集版本，或者（2）sealer apply 安装有高级配置的版本

## 已知问题

### 缺少镜像

目前发现离线包在安装最小功能集版本时，缺少镜像：`ack-agility-registry.cn-shanghai.cr.aliyuncs.com/ecp_builder/registration-operator:v1.0.0`

如何解决：首先正常执行上面的步骤，在机器B（离线机器）执行过 sealer run 部署最小功能集后，待部署进程结束，手动加载镜像，具体方案。

```bash
# 在机器A（有网络的机器）执行
# pull 镜像
docker pull ack-agility-registry.cn-shanghai.cr.aliyuncs.com/ecp_builder/registration-operator:v1.0.0
# save 镜像到 tar.gz
docker save ack-agility-registry.cn-shanghai.cr.aliyuncs.com/ecp_builder/registration-operator:v1.0.0 -o registration-operator_v1.0.0.tar.gz

# 将上述 registration-operator_v1.0.0.tar.gz 拷贝到机器B
# 在机器B（离线的机器）执行
docker load -i registration-operator_v1.0.0.tar.gz

# 稍等一段时间，之前有 ImageError 的应用就可以恢复
```

注：`registration-operator_v1.0.0.tar.gz` 也可以提前准备好
