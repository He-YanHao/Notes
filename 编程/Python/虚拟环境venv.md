Python 从 3.3 版本开始内置了 `venv` 模块，用于创建虚拟环境。以下是详细的使用方法：

## 1. 创建虚拟环境

```bash
# 在当前目录创建名为 myenv 的虚拟环境
python -m venv myenv

# 或者指定 Python 版本（如果有多个版本）
python3 -m venv myenv
python3.9 -m venv myenv
```

## 2. 激活虚拟环境

### Windows (命令提示符)
```bash
myenv\Scripts\activate.bat
```

### Windows (PowerShell)
```bash
myenv\Scripts\Activate.ps1
```

### Linux/macOS
```bash
source myenv/bin/activate
```

激活后，命令行提示符会显示虚拟环境名称，如：`(myenv) C:\Users\username>`

## 3. 使用虚拟环境

激活后，所有 Python 和 pip 命令都会在虚拟环境中运行：

```bash
# 安装包（只在虚拟环境中）
pip install pandas matplotlib

# 查看已安装的包
pip list

# 运行 Python
python
```

## 4. 退出虚拟环境

```bash
deactivate
```

## 5. 常用命令和选项

### 创建包含系统站点包的虚拟环境
```bash
python -m venv --system-site-packages myenv
```

### 创建时安装 pip（某些系统可能默认不安装）
```bash
python -m venv --without-pip myenv
```

### 升级虚拟环境中的 pip
```bash
# 激活虚拟环境后
python -m pip install --upgrade pip
```

## 6. 项目目录结构示例

```
my_project/
├── venv/                 # 虚拟环境目录（通常添加到 .gitignore）
├── src/
│   └── main.py
├── requirements.txt      # 项目依赖
└── README.md
```

## 7. 管理项目依赖

```bash
# 生成 requirements.txt
pip freeze > requirements.txt

# 从 requirements.txt 安装依赖
pip install -r requirements.txt
```

## 8. 删除虚拟环境

直接删除虚拟环境目录即可：
```bash
# 先退出虚拟环境
deactivate

# 然后删除目录
rm -rf myenv  # Linux/macOS
rmdir /s myenv  # Windows
```

## 最佳实践建议

1. **每个项目使用独立的虚拟环境**
2. **将 `venv/` 添加到 `.gitignore`**
3. **使用 `requirements.txt` 记录依赖**
4. **定期更新虚拟环境中的包**

这样就能有效隔离不同项目的依赖，避免版本冲突问题！