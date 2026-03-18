# Ubuntu环境快速布置本地模型

## 下载ollama并拉取模型

```bash
# 下载官方下载脚本并直接运行
curl -fsSL https://ollama.ai/install.sh | sh
# 启动 Ollama 服务（后台运行）
ollama serve &
# 拉取一个模型
ollama pull deepseek-r1:7b
ollama pull deepseek-r1:14b
ollama pull qwen2.5-coder:14b
ollama pull mistral-small:24b
ollama list
# 运行
ollama run deepseek-r1:7b-q4_k
```

| 模型名称                 | 大小   | 类别     | 一句话定位                     | 核心优势               |
| :----------------------- | :----- | :------- | :----------------------------- | :--------------------- |
| **deepseek-r1:7b**       | 4.7 GB | 通用推理 | 高效平衡的通用助手             | 响应快、日常任务效率高 |
| **deepseek-r1:14b**      | 9.0 GB | 通用推理 | 进阶版推理能手                 | 复杂逻辑推理、代码辅助 |
| **qwen2.5-coder:14b**    | 9.0 GB | 代码专家 | 专业的代码生成与修复工具       | 代码生成、理解、调试   |
| **mistral-small3.1:24b** | 15 GB  | 全能新秀 | 支持视觉理解的轻量级多模态模型 | 视觉理解、超长上下文   |

## 安装配置docker

```bash
sudo snap install docker
# 创建docker组
sudo groupadd docker
# 把当前用户加入docker
sudo usermod -aG docker $USER
```

```bash
docker run -d \                                 # 在后台运行容器
  --network=host \                              # 让容器使用宿主机的网络
  -v ~/open-webui-data:/app/backend/data \      # 挂载数据卷：将家目录的文件夹映射到容器内，用于持久化存储聊天记录、知识库等
  -e OLLAMA_BASE_URL=http://127.0.0.1:11434 \   # 设置环境变量：告诉Open WebUI连接到本机（127.0.0.1）11434端口的Ollama服务
  --name open-webui \                           # 为容器指定一个固定的名称，方便后续管理（如启动、停止）
  --restart always \                            # 设置重启策略：容器退出时（或Docker重启后）自动重新启动
  ghcr.io/open-webui/open-webui:main            # 指定使用的镜像：从GitHub Container Registry拉取Open WebUI的主版本镜像
```

```bash
docker run -d \
  --network=host \
  -v ~/open-webui-data:/app/backend/data \
  -e OLLAMA_BASE_URL=http://127.0.0.1:11434 \
  --name open-webui \
  --restart always \
  ghcr.io/open-webui/open-webui:main
```



Open WebUI 默认在**容器内部监听 8080 端口**，访问：

```
http://localhost:8080
```

