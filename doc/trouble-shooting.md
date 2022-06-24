# Trouble shooting

> 记录一些社区用户在部署时候遇到的问题，和解决办法

## Error from server: no preferred addresses found; konwn address: []

**问题表现**

执行 `kubectl logs $pod` 或者 `kubectl exec $pod` 时，报错

```
Error from server: no preferred addresses found; konwn address: []
```

**问题确认**

此类问题本质是 apiserver 尝试连接到对应 Pod 时，无法获取 Pod 所在 Node 的地址。可以通过以下两个方式确认问题：

1. `kubectl describe $node` 这里 `$node` 是 Pod 所在节点的节点名称，如果在 Status 中找不到 address 部分的内容，则说明 apiserver 的确没有 node 的 IP 信息
2. 在 Pod 所在节点执行 `ps -ef | grep kubelet | grep node-ip`，目的是查看 kubelet 是否正确获取了 node IP。如果没有输出，或者输出不对，则说明 kubelet 获取 node IP 异常

**如何解决**

通过 `ip -a` 查看网卡状态，一般情况是 node IP 对应网卡有问题，例如 DOWN 状态，此时请联系 IaaS 负责人开启网卡，或者查找对应版本的 Linux 网卡操作方法解决。

PS：kubelet 获取 node IP 一般不会有错，如果是双网卡情况更多是 IP 有错误，而不是找不到 IP。