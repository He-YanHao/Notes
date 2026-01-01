# ssh连接虚拟机

配置桥接模式网卡上网和仅主机模式网卡进行ssh连接

## ssh连接

### ssh远程连接必备条件

需要 `openssh-server` 组件，检查是否安装。

```shell
sudo apt install openssh-server
```

检查是否启动

```shell
sudo systemctl status ssh
```

如果没有启动，使用：

```shell
sudo systemctl enable --now ssh
```

启动，并设置为开机自启动。

```bash
he@he-books:~$ cd /etc/netplan/
he@he-books:~$ sudo vim ./01-network-manager-all.yaml 
```

```bash
sudo netplan apply
```



```bash
# Let NetworkManager manage all devices on this system
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    ens33:
      dhcp4: yes
      optional: true
    ens37: # 确保对应的是仅主机模式的网卡
      dhcp4: no
      addresses:
        - 192.168.246.125/24 # 确保和主机IP在同一个网络组
      gateway4: 192.168.246.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```



