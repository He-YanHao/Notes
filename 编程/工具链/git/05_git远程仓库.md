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

### 首次推送设置推送目标

#### 推送到远程 main 分支

```bash
git push -u origin main
```

或

```bash
git push --set-upstream origin main
```

将本地 `main` 分支的更改推送到远程仓库的 `origin` 仓库，并将本地 `main` 分支与远程 `origin/main` 分支建立追踪关系。

-   `origin`：远程仓库的默认名字。

-   `-u` 或 `--set-upstream` 选项会将本地分支 `main` 与远程的 `origin/main` 分支关联起来。这样，Git 会记住这个远程分支作为默认推送目标，以后可以直接运行 `git push` 或 `git pull`，而不需要每次指定远程分支。
  * 初次推送时，git 会要求你指定远程和分支名称，`-u` 会帮助你简化后续的推送操作。



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

拉取本地分支对应分支最新代码

```bash
git pull
```

`git pull` 实际上是 `git fetch` 和 `git merge`（或 `git rebase`）的组合。

它会首先从远程仓库获取最新的更改（和 `git fetch` 一样），然后 **自动将这些更改合并** 到当前分支。



## 同步远程仓库最新状态

同步当前分支

```bsh
git fetch
```

同步所有分支

```bash
git fetch --all
```



## 克隆仓库 git clone

```bash
git clone ULR地址
```

可以从网络上的代码托管平台复制工程。

