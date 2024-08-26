---
sidebar_position: 4
---

# 宿主机宕机自动迁移

## 简介

当检测到宿主机宕机时，宿主机上运行中的虚机自动迁移到其他宿主机上。

### 原理介绍

宕机检测依赖 etcd watch 机制。host-agent 启动时向 etcd 注册一个 key。 region watch 到 host key 消失后一段时间时(60s)发起宕机迁移，将宿主机上共享存储的虚机迁移到其他宿主机上。

所以宕机自动迁移依赖 onecloud operator 部署的 etcd。
```bash
# 查看 onecloud operator 部署的 etcd 状态
kubectl get pods -n onecloud | grep default-etcd
```

host-agent 启动时会向 etcd 注册一个 key. 当开启宕机自动迁移，宿主机断网时一段时间后 host-agent 会将宿主机上的虚机关机，防止网络恢复后磁盘双写。

## 使用介绍

```bash
# climc 命令

# 修改region服务的配置
$ climc service-config --config '{"default":{"enable_host_health_check":true}}' region2

$ climc host-auto-migrate-on-host-down --help
Usage: climc host-auto-migrate-on-host-down [--auto-migrate-on-host-shutdown {enable,disable}] [--help] [--auto-migrate-on-host-down {enable,disable}] <ID> ...

# 开启宕机自动迁移
$ climc host-auto-migrate-on-host-down --auto-migrate-on-host-down enable <ID>

# 开启宕机自动迁移同时开启关机自动迁移
$ climc host-auto-migrate-on-host-down --auto-migrate-on-host-down enable --auto-migrate-on-host-shutdown enable <ID>

# 取消宕机自动迁移
$ climc host-auto-migrate-on-host-down <ID>

# 重启region服务
kubectl -n onecloud rollout restart deployment default-region
```
