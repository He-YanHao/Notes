# Distrobox

## 下载

```shell
# 宿主机 Ubuntu 26.04 上执行
sudo apt update
sudo apt install -y distrobox podman

# 创建一个 Ubuntu 24.04 容器，名字叫 guider
distrobox create --name guider --image ubuntu:24.04

# 进入容器
distrobox enter guider
```

```bash
sudo apt update
sudo apt install -y libxcb-xinerama0 libxcb-cursor0 libxkbcommon-x11-0 \
  libgl1 libegl1 libgtk-3-0 libwebkit2gtk-4.1-0 libfuse2

# 然后运行你的 GUI Guider 安装包
# 如果是 .deb：
sudo apt install ./GUI-Guider-xxx.deb
# 如果是 .run：
chmod +x GUI-Guider-xxx.run
./GUI-Guider-xxx.run
```

