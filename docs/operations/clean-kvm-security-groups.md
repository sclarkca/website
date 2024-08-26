---
edition: ce
sidebar_position: 23
---

# 清理未使用的安全组

3.11及之后版本安全组不再使用一对多设计，因此升级到3.11版本后，系统内会残留一些无用的kvm安全组，此时需要清理这部分未被使用的安全组

```bash
# 此命令仅删除本地数据库中未被其他实例引用的kvm安全组记录
$ climc secgroup-clean
```