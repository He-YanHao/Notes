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



## HEAD

### 移动 HEAD 引用

**HEAD** 是 Git 中一个特殊的指针，它指向**当前所在的提交记录**，表示工作目录的当前状态。

```bash
git checkout HEAD^     # 移动到父提交
git checkout HEAD^^    # 移动到祖父提交
git checkout HEAD~1    # 等同于 HEAD^
```

