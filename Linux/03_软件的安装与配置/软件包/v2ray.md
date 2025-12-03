# v2ray

## 安装

```bash
sudo apt update
sudo apt upgrade
sudo apt install curl
curl -Ls https://mirrors.v2raya.org/go.sh | sudo bash
wget https://github.com/v2rayA/v2rayA/releases/download/v2.0.0/installer_debian_amd64_2.0.0.deb
sudo apt install ./installer_debian_amd64_2.0.0.deb

```

```
https://github.com/v2rayA/v2rayA/releases
```



```bash
# 通知系统管理程序重新加载所有服务配置文件
sudo systemctl daemon-reload

# 启动 v2raya 服务
sudo systemctl start v2raya

# 检查服务状态
sudo systemctl status v2raya

# 确认服务已设置为开机自启 (应该显示为 enabled)
sudo systemctl is-enabled v2raya

# 停止 v2raya 服务
sudo systemctl stop v2raya

# 禁止开机自启
sudo systemctl disable v2raya

# 再次检查状态确保已停止
sudo systemctl status v2raya
```

浏览器访问：

```
http://127.0.0.1:2017
```

开始管理。

如不慎忘记密码，使用 v2raya --reset-password 重置。



