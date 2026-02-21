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
git push --set-upstream origin main
git push --force-with-lease origin main
```

```
这三条 `git push` 命令都用于将本地 `main` 分支的更改推送到远程仓库，但它们之间有一些细微的区别，尤其是在设置远程追踪分支和强制推送方面。下面是对每个命令的详细分析：

### 1. `git push -u origin main`

* **作用**：将本地 `main` 分支的更改推送到远程仓库的 `origin` 仓库，并将本地 `main` 分支与远程 `origin/main` 分支建立追踪关系。
* **解释**：

  * `-u` 或 `--set-upstream` 选项会将本地分支 `main` 与远程的 `origin/main` 分支关联起来。这样，Git 会记住这个远程分支作为默认推送目标，以后你可以直接运行 `git push` 或 `git pull`，而不需要每次指定远程分支。
  * 初次推送时，Git 会要求你指定远程和分支名称，`-u` 会帮助你简化后续的推送操作。

  **适用场景**：初次推送本地分支并希望设置远程追踪分支时。

### 2. `git push --set-upstream origin main`

* **作用**：与 `git push -u origin main` 完全相同。`--set-upstream` 用于将本地分支与远程分支建立追踪关系。
* **解释**：

  * 该命令与 `-u` 选项等效，都是用于设置本地分支与远程分支的关系，使得将来可以简化推送和拉取操作。

  **适用场景**：当你需要设置本地分支和远程分支的追踪关系时，可以使用该命令。

### 3. `git push --force-with-lease origin main`

* **作用**：将本地 `main` 分支强制推送到远程仓库的 `origin/main` 分支，允许覆盖远程分支的历史记录，但会进行一些检查以确保你的推送不会无意中覆盖其他人推送的内容。
* **解释**：

  * `--force-with-lease` 是一种比 `--force` 更安全的强制推送方式。它的作用是：只有在远程分支的当前状态没有变化的情况下，才允许你强制推送。具体来说，Git 会检查远程分支的最新提交与当前本地分支的提交是否匹配，如果远程分支自上次拉取以来被其他人更新过，推送会失败，防止你覆盖其他人的更改。
  * 这对于重写历史、解决合并问题或清理提交历史时非常有用，但要小心使用，确保你了解推送的内容。

  **适用场景**：当你需要推送本地的更改并覆盖远程分支时（例如，通过重写历史或处理错误的提交），且希望避免无意中覆盖他人提交。

### 总结：

* **`git push -u origin main`** 和 **`git push --set-upstream origin main`**：这两条命令是等效的，作用是将本地 `main` 分支推送到远程仓库，并将本地分支与远程分支建立追踪关系。适用于初次推送时设置远程分支。
* **`git push --force-with-lease origin main`**：用于强制推送并覆盖远程分支，但在推送前会进行安全检查，确保你不会覆盖其他人的更改。适用于需要覆盖历史的场景。

如果你没有修改远程分支的历史（例如，不涉及 `rebase` 或 `reset`），一般情况下推荐使用前两条命令。
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

拉取本地分支对应分支最新代码

```bash
git pull
```

`git pull` 实际上是 `git fetch` 和 `git merge`（或 `git rebase`）的组合。它会首先从远程仓库获取最新的更改（和 `git fetch` 一样），然后 **自动将这些更改合并** 到当前分支。

```bash
git fetch origin master
```

仅下载远程更新，保持本地分支不变。

```bash
git reset --hard origin/master
```

将当前分支重置为远程 `master` 分支的最新状态，并丢弃所有本地未提交的更改。

拉取远程仓库的新分支

```bash
git checkout -b he_power_mng_test origin/he_power_mng_test
```



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

