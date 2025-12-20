```
he@he-books:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:40:db:98 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 192.168.188.232/24 brd 192.168.188.255 scope global dynamic noprefixroute ens33
       valid_lft 3362sec preferred_lft 3362sec
    inet6 240e:47e:3262:1921:d9d1:b4b2:637e:c2a6/64 scope global temporary dynamic 
       valid_lft 7106sec preferred_lft 7106sec
    inet6 240e:47e:3262:1921:7e98:b00d:32a7:f07/64 scope global dynamic mngtmpaddr noprefixroute 
       valid_lft 7106sec preferred_lft 7106sec
    inet6 fe80::fef0:6919:8f06:e1dc/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
4: dae0@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 82:97:4f:8d:cf:de brd ff:ff:ff:ff:ff:ff link-netns daens
    inet6 fe80::ecee:eeff:feee:eeee/128 scope link 
       valid_lft forever preferred_lft forever
    inet6 fe80::fc3a:27ff:fe74:3bee/64 scope link 
       valid_lft forever preferred_lft forever
he@he-books:~$ ip route
default via 192.168.188.47 dev ens33 proto dhcp src 192.168.188.232 metric 100 
127.0.0.0/8 dev lo proto kernel scope link src 127.0.0.1 metric 30 
192.168.188.0/24 dev ens33 proto kernel scope link src 192.168.188.232 metric 100 
he@he-books:~$ nmcli con show
NAME        UUID                                  TYPE      DEVICE 
有线连接 1  d5edc2ee-b732-31fd-b9a0-4d4046d64b27  ethernet  ens33  
lo          3493b2c5-fba3-45e6-a04c-d927a7992f32  loopback  lo     
```







太好了，这三条信息**已经把所有关键参数都给齐了** 👍
我直接给你一个 **“一次执行、100% 生效、不掉线”** 的固定 IP 方案，**专门适配你现在这台 Ubuntu + VMware + NetworkManager** 的环境。

------

# ✅ 最稳妥方案：用 **nmcli** 给 ens33 固定 IPv4

你当前情况总结一下（我已经帮你分析好了）：

-   网卡：`ens33`
-   当前 IP（DHCP）：`192.168.188.232/24`
-   网关：`192.168.188.47`
-   网段：`192.168.188.0/24`
-   NetworkManager 连接名：`有线连接 1`
-   桥接模式（正确）
-   Xshell 只关心 IPv4（完全 OK）

------

## 一、选一个“安全”的固定 IP

当前 DHCP 给你的是 `.232`
我们选一个 **不太可能冲突** 的，比如：

>   **192.168.188.50**

（只要不在你路由器 DHCP 池里就行，一般没问题）

------

## 二、直接执行这条命令（核心）

👉 **整条复制粘贴执行**

```bash
sudo nmcli con mod "有线连接 1" \
ipv4.method manual \
ipv4.addresses 192.168.188.50/24 \
ipv4.gateway 192.168.188.47 \
ipv4.dns "8.8.8.8 114.114.114.114"
```

这一步只是**改配置，不会断网**。

------

## 三、让配置生效（会短暂断一下网，正常）

```bash
sudo nmcli con down "有线连接 1"
sudo nmcli con up "有线连接 1"
```

如果你是 **通过 Xshell 连这台虚拟机**：

-   会断 1～2 秒
-   然后用 **新 IP** 连回来即可

------

## 四、验证是否真的固定住

```bash
ip a
ip route
```

你应该看到：

-   `inet 192.168.188.50/24`（不再是 dynamic）
-   `default via 192.168.188.47 dev ens33`

然后在 Xshell 里：

```
主机：192.168.188.50
```

以后 **永远不变** ✅

------

## 五、确认“开机也不会变”

重启一次虚拟机：

```bash
sudo reboot
```

重启后再看：

```bash
ip a | grep inet
```

✔ 还是 `192.168.188.50`
✔ Xshell 不用再改 IP

------

## 六、两个你这个环境里“非常重要”的补充建议

### ✅ 1️⃣ 固定 VMware 网卡 MAC（防止极端情况）

VMware → 虚拟机设置 → 网络适配器
✔ 使用 **手动 MAC 地址**

你现在的 MAC 是：

```
00:0c:29:40:db:98
```

固定它，避免 DHCP/NM 重新识别为新设备。

------

### 🚫 2️⃣ 不要手动改 `/etc/netplan/90-NM-*.yaml`

你现在这套：

```
Netplan + NetworkManager + 桥接
```

**nmcli 才是正道**，比手写 yaml 稳定很多。

------

## 七、如果哪天你想“切回 DHCP”

一条命令就能回去：

```bash
sudo nmcli con mod "有线连接 1" ipv4.method auto
sudo nmcli con down "有线连接 1"
sudo nmcli con up "有线连接 1"
```

------

## 🏁 总结一句话

>   **你现在这套环境，用 nmcli 固定 IP 是最稳、最不折腾、最适合 Xshell 的方案。**

如果你愿意，我也可以：

-   帮你算一个 **更“安全”的 IP 范围**
-   或教你 **让 Xshell 自动识别虚拟机（脚本 + hosts）**

你现在用 Xshell 连这台虚拟机是 **开发环境常驻**，还是 **偶尔连一下**？

