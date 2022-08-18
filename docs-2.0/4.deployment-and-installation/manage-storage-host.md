# 管理 Storage 主机

从 3.0.0 版本开始，在配置文件中添加的 Storage 主机无法直接读写，配置文件的作用仅仅是将 Storage 主机注册至 Meta 服务中。必须使用`ADD HOSTS`命令后，才能正常读写 Storage 主机。

<!--
!!! note

    NebulaGraph Cloud 上建立的集群会自动添加 Storage 主机，用户无需手动执行`ADD HOSTS`。
-->

## 增加 Storage 主机

向集群中增加 Storage 主机。

```ngql
ADD HOSTS <ip>:<port> [,<ip>:<port> ...];
ADD HOSTS "<hostname>":<port> [,"<hostname>":<port> ...];
```

!!! note

    - 增加 Storage 主机在**下一个**心跳周期之后才能生效，为确保数据同步，请等待 2 个心跳周期（20 秒），然后执行`SHOW HOSTS`查看是否在线。
    
    - IP地址和端口请和配置文件中的设置保持一致，例如单机部署的默认为`127.0.0.1:9779`。
    
    - 使用域名时，需要用引号包裹，例如 `ADD HOSTS "foo-bar":9779`。

    - 确保新增的 Storage 主机没有被其他集群使用过，否则会导致添加 Storage 节点失败。

## 删除 Storage 主机

从集群中删除 Storage 主机。

!!! note

    无法直接删除正在使用的 Storage 主机，需要先删除关联的图空间，才能删除 Storage 主机。

```ngql
DROP HOSTS <ip>:<port> [,<ip>:<port> ...];
DROP HOSTS "<hostname>":<port> [,"<hostname>":<port> ...];
```
