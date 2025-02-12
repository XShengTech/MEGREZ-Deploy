# 常见问题

## 1. 在 Ubuntu 24.04 系统部署的被控端, 监控显示的核心数有问题

### 解决办法

> [!NOTE]
> 重启 lxcfs 前, 请先关闭该节点上的所有容器, 否则可能造成未知错误

Ubuntu 24.04 的 lxcfs 服务添加 `--enable-cfs` 特性

```bash
vim /usr/lib/systemd/system/lxcfs.service
```

这一行
```bash
ExecStart=/usr/bin/lxcfs /var/lib/lxcfs
```

修改为
```bash
ExecStart=/usr/bin/lxcfs /var/lib/lxcfs --enable-cfs
```

重启 lxcfs 服务

```bash
systemctl daemon-reload
service lxcfs restart
```