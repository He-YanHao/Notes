# 版本管理

## 查看分支 git branch

```bash
# 查看本地分支
git branch

# 查看远程分支
git branch -r

# 查看所有本地和远程分支
git branch -a
```





## 创建分支 git branch

查看分支的指令也可以用来创建分支。

创建一个名叫 `new` 的分区。

```bash
git branch new
```

## 删除本地分支

```bash
git branch -d <branch-name>
```

-   `-d` 选项表示**删除本地分支**，但是会确保该分支已经合并到当前分支或者其他分支中。如果该分支尚未被合并，Git 会给出警告，防止丢失未合并的更改。

### 强制删除本地分支（如果未合并）

如果确定要删除一个**尚未合并的**本地分支（即使它包含未合并的更改），可以使用 `-D`（大写 `D`）选项：

**命令：**

```bash
git branch -D <branch-name>
```

这个命令会强制删除本地分支，即使该分支有未合并的更改。



## 切换分支 git checkout

切换到 `new` 分区。

```bash
git checkout new
```

可以使用 `-b` 参数，若要切换的分支不存在，则直接创建。

```bash
git checkout -b new
```

### 临时保存 git stash

在切换分支时有时需要临时保存文件，使用：

```bash
git stash
```

保存，之后使用

```bash
git stash pop
```

回复。



## 合并版本 git merge （危险）

当存在多个分支，可以使用 `git merge` 合并这些分支。

这个指令存在不安全性，会使提交历史交叉，后续不易排查，因此大企业**禁用**。

```bash
git merge 分支名
```

会将参数中的分支合并到当前分支里。



## 移动提交 git rebase

会把当前分支合并到参数分支后面，所以一般是把分支开发合并到 `main` 分支。

```bash
git rebase main
```

假设原来是：

```
A4   B2
|    |
A3   B1
|    |
A2---/ --- new分支
|
A1
main分支
```

在  `new` 分支使用

```bash
git rebase main
```

会变成：

```
     B2*
     |
     B1*
     |
A4---/ --- new分支
|
A3
|
A2
|
A1
main分支
```

需要注意，这个操作不是移动提交，而是重新提交。

新的 `B1*` `B2*` 和 `B1` `B2` 的提交哈希不同。

接下来提交到远程仓库的时候需要强制提交

```bash
git push --force origin power_device
```







## 整理提交 git rebase

除了移动提交外，`git rebase` 还要整理提交的作用。

使用：

```bash
git rebase -i HEAD~5
```

即可交互式重写最新的5个提交，删掉对应的提交短哈希和commit说明就行。

注意：在编辑文档过程中不要保存！！！

```bash
git rebase -i <提交>
git rebase -i --root        # 从第一个提交开始rebase
```





## 合并多个版本 git cherry-pick

可以将 `commit1` `commit3` `commit5` 都合并到当前分支下。

```bash
git cherry-pick commit1 commit3 commit5
```



## 从其他分支获得文件git restore

可以把多个分支的文件合并到自己的项目里

```bash
git restore --source=<branch-name> <path-to-file>
```



