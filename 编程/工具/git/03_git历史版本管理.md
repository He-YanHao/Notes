# git历史版本管理

## 初始化新仓库 git init 

```bash
git init 路径
```

不包含路径则为当前目录。

```bash
git init
```



## 文件管理

### 添加文件 git add

```bash
git add 路径
#添加指定文件
git add .
#添加全部文件
```



### 删除 git rm

```bash
git rm
```



### 获取状态 git status

```bash
git status
```



### 提交到本地仓库 git commit

```bash
git commit -m "提交说明"
```



### 查看差异 git diff

```bash
git diff
```

| 比较对象                   | 命令示例                            | 使用场景                                                     |
| :------------------------- | :---------------------------------- | :----------------------------------------------------------- |
| **工作区 vs 暂存区**       | `git diff`                          | 查看**尚未暂存**的修改（你改了但还没 `git add` 的文件）      |
| **暂存区 vs 最后一次提交** | `git diff --staged` (或 `--cached`) | 查看**已暂存但未提交**的修改（`git add` 后，`git commit` 前的状态） |
| **工作区 vs 最后一次提交** | `git diff HEAD`                     | 查看**所有未提交**的修改（包括已暂存和未暂存的）             |
| **两次提交之间**           | `git diff commitA commitB`          | 比较两个历史提交的差异                                       |
| **两个分支之间**           | `git diff branchA..branchB`         | 比较两个分支最新提交的差异                                   |
| **特定文件**               | `git diff -- 文件路径`              | 只比较特定文件的差异                                         |



## 查看提交历史

```bash
git log
```

查看提交历史。

## 回滚

### 硬回滚 git reset

如果你想完全回滚到某个提交，包括将工作区和暂存区恢复为该提交的状态（即丢弃当前提交之后的所有更改），你可以使用 `git reset --hard`。

**命令：**

```
git reset --hard <commit-hash>
```

其中 `<commit-hash>` 是你想回滚到的提交哈希（例如 `923bfedafc14b32e5934514e9100bd178beefb7a`）。

**注意：**

-   `--hard` 会丢弃工作目录中的所有未提交的更改。如果你有任何未保存的更改，务必先提交或暂存它们，否则会丢失。
-   `git reset --hard` 会改变本地历史，这意味着你的历史记录会被修改，所以如果你已经将提交推送到远程仓库，使用 `git reset` 后，你需要强制推送。

### 只回滚工作区内容 git checkout

如果你不想更改提交历史，只想回滚工作区的内容到某个提交的状态，你可以使用 `git checkout` 来恢复文件：

**命令：**

```
git checkout <commit-hash> -- <file-paths>
```

这样，你可以恢复特定文件或整个工作区到某个提交的状态。例如，恢复整个工作区：

```
git checkout <commit-hash> .
```

这将仅恢复工作区的文件内容，但不会更改历史提交。

### 创建新的反向提交 git revert

如果你想保留所有提交历史，并且将当前的提交回滚到某个特定的提交，避免修改提交历史，可以使用 `git revert`。

**命令：**

```
git revert <commit-hash>
```

**解释：**

-   `git revert` 会创建一个新的提交，用于撤销指定提交的所有更改。原始的提交历史会被保留，不会像 `git reset` 那样更改历史。
-   `git revert` 是一个**非破坏性**的操作，适合多人协作时使用，因为它不会改变任何现有提交历史。

## HEAD

### 移动 HEAD 引用

**HEAD** 是 Git 中一个特殊的指针，它指向**当前所在的提交记录**，表示工作目录的当前状态。

```bash
git checkout HEAD^     # 移动到父提交
git checkout HEAD^^    # 移动到祖父提交
git checkout HEAD~1    # 等同于 HEAD^
```

