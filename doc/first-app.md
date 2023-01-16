# 创建第一个应用

下图是在CNStack创建应用的简要流程

![image](https://user-images.githubusercontent.com/8002217/211233038-c88a1a67-5653-422d-a481-8f620096b7c4.png)

## 创建业务集群

CNStack部署后会创建名为“cluster-local”的管理集群，该集群如果有足够的工作节点可以部署应用，使用“cluster-local”部署应用的场景可以跳过本节。使用者也可以选择创建业务集群部署应用。

### 准备业务集群资源

当前平台所在集群的子网内，分配至少一个节点作为目标集群的运行环境。节点需要满足的条件和CNStack平台部署时的条件一样，master节点8C16G、50G系统盘，worker节点2C4G，50G系统盘。

#### 创建集群

进入“集群管理菜单”，可以看到“集群创建按钮”

![image](https://user-images.githubusercontent.com/8002217/211234935-17f04b23-a378-4c85-b752-596eefefe82d.png)

#### 配置集群创建参数

在“Master节点配置”中，主要有以下四个配置

* 是否允许调度业务负载。作为入门验证，建议设置为允许，以减少测试资源需求。
* etcd数据盘。作为入门验证，建议设置为“与系统盘共享”，以减少磁盘需求。
* 管控数据存储。作为入门验证，建议设置为“与系统盘共享”，以减少磁盘需求。
* 本地存储池。如果第三步选择了“从本地盘调度池中划分”，需要配置用于作为本地存储池的磁盘分区。可以在目标节点使用lsblk，获取节点的磁盘分区情况。找一个合适的未使用分区填写在这里，例如，“/dev/vdb”。

![image](https://user-images.githubusercontent.com/8002217/211235242-5fcae504-0c5a-4365-98a1-f27fb36bedd0.png)

#### 等待集群创建并纳管

这一步系统会自动执行，不需要用户介入

![image](https://user-images.githubusercontent.com/8002217/211235363-308af36e-96cf-4f92-b3dd-85d04c2e9668.png)

## 平台管理员创建租户，并授权资源

### 创建租户

可以直接使用“内置管理账号”（admin）作为租户管理员

![image](https://user-images.githubusercontent.com/8002217/211235677-7ea3902b-f945-41c5-a9c9-346474565789.png)

### 授权资源给租户

可以将集群的默认节点组授权给租户

![image](https://user-images.githubusercontent.com/8002217/211235756-91f403ea-5d4d-4081-8d11-3416c768219b.png)

## 租户管理员创建项目，并授权资源

### 创建项目

可以直接使用“内置管理账号”（admin）作为项目管理员

![image](https://user-images.githubusercontent.com/8002217/211235871-7748542b-0457-410c-9118-ab7bf2930c48.png)

### 授权资源给项目

租户可以授权全部或者部分自己拥有权限的资源给项目。与此同时，命名空间也被同步创建

![image](https://user-images.githubusercontent.com/8002217/211236022-edbfd367-2661-461d-8282-b2c641da8405.png)

## 项目管理员或其他成员发布工作负载、模板应用等

项目管理员或其他成员在“工作空间”的“我的项目”中，可以看到其拥有权限的所有项目清单

![image](https://user-images.githubusercontent.com/8002217/211236170-0ddb6260-53ec-446b-966e-809af2994613.png)

### 创建工作负载

可以创建有状态，或无状态的工作负载

![image](https://user-images.githubusercontent.com/8002217/211236230-7d1efd8f-4dec-4f0b-a3c9-1a10bf5d8d5b.png)

为了简化操作，可以直接使用Demo镜像

![image](https://user-images.githubusercontent.com/8002217/211236265-bc7a900b-94ca-49fd-ab0a-c8422552c6d1.png)

创建完成之后，进入详情页，查看Pod运行情况

![image](https://user-images.githubusercontent.com/8002217/211236291-9862f612-6257-4973-8cbb-3ff61f3d25d5.png)

### 创建模板应用，发布一个“云组件”实例

平台支持基于helm charts规范的模板化的应用实例管理。用户可以基于事先准备好的应用模板，在项目中按需实例化，并持续运维管理。模板应用分两种形态：云组件和HelmChart应用。云组件的包，由平台管理员在能力中心进行管理。项目成员在项目中按需实例化。而HelmChart应用的包，则由项目管理员在项目的制品仓库中上传，并配置共享范围，缺省仅能用于当前项目。

#### 云组件的包管理

平台管理员，进入“能力中心”的“云组件”菜单，进行云组件包的上传。云组件包，可以来自于第三方供应商，也可以用户自行封装。符合helm charts规范的包都可以作为云组件管理。

![image](https://user-images.githubusercontent.com/8002217/211236995-b4226f50-d7c2-4106-b7c3-8eea0577a621.png)

上传实现准备好的包文件

![image](https://user-images.githubusercontent.com/8002217/211237046-f6651578-f1da-49a1-ae8c-a8130eb3fb38.png)

在云组件列表中查看上传的“云组件”

![image](https://user-images.githubusercontent.com/8002217/211237268-bc65298b-ad0b-41bc-b7f9-3b807047a3c3.png)

#### 云组件的实例化

项目管理员进入工作空间的“项目管理”页面，并进入“模板应用”菜单

![image](https://user-images.githubusercontent.com/8002217/211237416-5f7561af-0b07-4f64-9f96-70d9d879aa3f.png)

项目管理员开始“创建实例”，选择云组件 “apache”，以及合适的版本，并配置必要的参数

![image](https://user-images.githubusercontent.com/8002217/211237492-16da9dd8-91df-4694-8f3a-7ab2f780ad13.png)

部署完成后，项目成员可以持续对该实例进行运维

![image](https://user-images.githubusercontent.com/8002217/211237549-12f91b0b-fd6b-4a78-9050-5e6a35cecc58.png)



