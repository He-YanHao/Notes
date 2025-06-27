# git

## 基础概念

### 区域

分为工作区，暂存区，本地仓库。

工作区位于`.git`目录，用于实际操作。

暂存区`.git/index`目录，用于存放即将提交的代码。

本地仓库`.git/objects`目录，用于存放代码和版本控制的地方。

## 配置信息

配置信息使用`git config`指令，其拥有三个优先级

- --loca：表示使用本地配置文件，仅对当前的git仓库有效。

- --global：表示使用全局配置文件，对所有的git仓库有效。

- --system：表示使用系统级配置文件，对所有的用户有效。

	> 三者不能同时出现。

### 配置信息

#### 配置基本信息

使用

```
git config --global user.name "名字"
git config --global user.email 邮箱
```

配置用户基本信息。

> 对于有空格的参数加双引号。

#### 配置编辑器信息


```markdown
# 设置 VS Code 为默认编辑器
git config --global core.editor "code --wait"

# 设置 Vim 为默认编辑器
git config --global core.editor "vim"

# 设置 nano 为默认编辑器
git config --global core.editor "nano"
```

#### 配置换行信息

```markdown
# Windows 推荐设置（检出时转 CRLF，提交时转 LF）
git config --global core.autocrlf true

# Linux/macOS 推荐设置（提交时转 LF）
git config --global core.autocrlf input

# 完全禁用转换
git config --global core.autocrlf false
```

### 查看信息

```markdown
# 列出所有配置（包括所有级别）
git config --list
# 列出全局配置
git config --global --list
# 查看特定配置项的值
git config user.name
```

### 保存信息

```
git config --global credential.helper store
```

将本地永久保存Git凭证。

```
git config --global credential.helper cache --timeout=3600
```

将本地缓存区保存1小时Git凭证。

查看已配置的信息

```
git config --global --list
```

会被保存到

```
~/.gitconfig
```

目录下。

### 编辑信息

```markdown
# 编辑全局配置文件
git config --global --edit
# 编辑当前仓库的配置文件
git config --edit
```

## 基础指令

### 初始化仓库 git init 

```c
git init 路径
```

不包含路径则为当前目录。

```
git init
```

### 克隆仓库 git clone

```
git clone ULR地址
```

### 获取状态 git status

```c
git status
```

### 回退版本 git reset

```c
git reset
```

### 查看差异 git diff

```c
git diff
```

### 删除 git rm

```c
git rm
```

### .gitignore

忽略版本控制的文件。



```c

```



```c

```



```c

```



```c

```



```c

```



```c

```



```c

```



```c

```



```c

```



```c

```



