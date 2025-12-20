# git远程仓库

## 添加远程仓库 git remote

```bash
git remote add origin https://github.com/yourname/repo.git
```

或使用 SSH：

```bash
git remote add origin git@github.com:yourname/repo.git
```

验证远程仓库

```bash
git remote -v
```

## 推送到远程仓库

### 首次推送

#### 推送到远程 main 分支

```bash
git push -u origin main
```

#### 如果本地是 master 分支

```bash
git push -u origin master:main
```

| `git push`    | 将本地提交推送到远程仓库                                     |
| ------------- | ------------------------------------------------------------ |
| `-u`          | 设置上游跟踪分支（后续可直接用 `git push` 代替完整命令）     |
| `origin`      | 远程仓库的别名（默认名称）                                   |
| `master:main` | 本地分支 `master` 推送到远程分支 `main`（用冒号 `:` 分隔本地和远程分支） |

### 后续推送

仅需运行

```bash
git push
```

## 拉取远程仓库

```bash
git pull
```

```bash
git fetch origin master
git reset --hard origin/master
```



## 克隆仓库 git clone

```bash
git clone ULR地址
```

可以从网络上的代码托管平台复制工程。

