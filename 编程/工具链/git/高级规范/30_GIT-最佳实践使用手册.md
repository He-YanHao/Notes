### A. 常用命令速查

#### 分支操作
```bash
git checkout -b <branch>          # 创建并切换分支
git branch -d <branch>            # 删除本地分支
git push origin --delete <branch> # 删除远程分支
```

#### 提交操作
```bash
git commit -m "<message>"         # 提交
git commit --amend                # 修改最近一次提交
git commit --amend --no-edit      # 修改提交内容，不修改信息
```

#### Rebase 操作
```bash
git rebase <branch>               # Rebase 到指定分支
git rebase -i <commit>            # 交互式 Rebase
git rebase --continue             # 继续 Rebase
git rebase --abort                # 中止 Rebase
```

#### 查看操作
```bash
git log --oneline --graph         # 图形化查看历史
git log --all --graph --decorate  # 查看所有分支历史
git show <commit-id>              # 查看提交详情
```

### B. Commit Message 模板

创建 `.gitmessage` 文件：

```
# <type>(<scope>): <subject>
#
# <body>
#
# <footer>
#
# Type 类型:
#   feat:     新功能
#   fix:      Bug 修复
#   docs:     文档更新
#   style:    代码格式调整
#   refactor: 代码重构
#   perf:     性能优化
#   test:     测试相关
#   chore:    构建/工具变动
#
# Scope 范围（可选）:
#   影响的范围，如模块名、组件名
#
# Subject 主题:
#   简短描述，不超过 50 字符
#   使用祈使句，首字母小写，结尾不加句号
#
# Body 正文（可选）:
#   详细描述修改内容、动机和实现细节
#   每行不超过 72 个字符
#
# Footer 脚注（可选）:
#   关联 Issue: Closes #123, Fixes #456
#   破坏性变更: BREAKING CHANGE: <description>
```

配置 Git 使用模板：
```bash
git config commit.template .gitmessage
```







这段内容其实是在做两件事：

>   ① 给团队一个“命令速查表”
>   ② 给团队一个“统一的提交规范模板”

它属于**工程规范文档的一部分**，而不是新的 Git 概念。

我们分块解释。

------

# 一、A. 常用命令速查 —— 是什么？

这是一个“团队统一用法清单”。

目的：

```text
让大家用一致的命令方式操作 Git
```

它本质上是：

>   开发团队的 Git 操作约定。

------

## 1️⃣ 分支操作

```bash
git checkout -b <branch>
```

创建并切换分支。

现代 Git 其实更推荐：

```bash
git switch -c <branch>
```

------

```bash
git branch -d <branch>
```

删除本地分支。

注意：

-   `-d` 是安全删除（未合并会拒绝）
-   `-D` 是强制删除

------

```bash
git push origin --delete <branch>
```

删除远程分支。

本质是：

```text
告诉远程仓库：这个分支不要了
```

------

## 2️⃣ 提交操作

```bash
git commit --amend
```

修改最近一次提交。

常见用途：

-   修正提交信息
-   补充漏掉的文件

------

```bash
git commit --amend --no-edit
```

只改提交内容，不改 message。

等价于：

```text
“补丁式修正”
```

------

## 3️⃣ Rebase 操作

这一块属于进阶。

```bash
git rebase <branch>
```

把当前分支“基于”指定分支重新排列。

------

```bash
git rebase -i <commit>
```

交互式 rebase。

可以：

-   合并 commit
-   改 commit message
-   删除 commit
-   调整顺序

它是：

```text
整理历史的工具
```

------

```bash
git rebase --continue
git rebase --abort
```

用于处理冲突。

------

## 4️⃣ 查看操作

```bash
git log --oneline --graph
```

图形化历史。

非常推荐使用。

------

```bash
git log --all --graph --decorate
```

显示所有分支和标签。

适合理解复杂仓库结构。

------

```bash
git show <commit-id>
```

查看某个提交具体改了什么。

------

# 二、B. Commit Message 模板 —— 是什么？

这部分比命令更重要。

它在定义：

>   提交信息必须遵循统一规范。

------

## 为什么要规范提交信息？

如果没有规范，历史会变成：

```text
修复 bug
改点东西
更新
...
```

完全无法维护。

规范后会变成：

```text
feat(auth): 添加用户登录功能
fix(api): 修复参数校验错误
refactor(core): 重构缓存模块
```

可读性和可维护性完全不同。

------

# 三、这个模板属于什么规范？

它属于：

>   Conventional Commits 规范

结构是：

```text
<type>(<scope>): <subject>
```

------

## Type 的作用

| 类型     | 含义     |
| -------- | -------- |
| feat     | 新功能   |
| fix      | 修复     |
| refactor | 重构     |
| perf     | 性能优化 |
| docs     | 文档     |
| test     | 测试     |
| chore    | 构建工具 |

------

## Scope 是什么？

影响范围。

例如：

```text
feat(auth): 添加登录功能
```

`auth` 就是 scope。

------

## Body 的作用

写清楚：

-   为什么改
-   怎么改
-   有什么影响

------

## Footer 的作用

用于：

```text
Closes #123
BREAKING CHANGE: xxx
```

这在自动生成变更日志时非常重要。

------

# 四、配置 commit 模板的作用

```bash
git config commit.template .gitmessage
```

以后每次：

```bash
git commit
```

都会自动加载这个模板。

你只需要填写内容。

------

# 五、整体理解

这一整段在做的事情是：

>   让团队的 Git 历史结构化、可维护、可自动化。

因为规范提交信息可以：

-   自动生成 Release Notes
-   自动生成版本号
-   自动生成变更日志
-   自动触发发布流程

------

# 六、它属于什么层次？

这是：

>   “工程规范建设”

而不是 Git 命令学习。

当项目变大时：

-   提交历史 = 文档
-   提交记录 = 责任链
-   提交规范 = 自动化基础

------

# 七、适合谁？

非常适合：

-   多人协作
-   开源项目
-   商业项目
-   长期维护项目

个人小实验可以简化。

------

# 八、总结一句话

这段内容本质是：

>   把 Git 从“代码存储工具”升级为“工程管理工具”。

------

如果你愿意，我可以给你讲：

-   为什么规范 commit 可以自动生成版本号？
-   如何根据 commit 自动升级 v1.2.3？
-   嵌入式 C 项目是否需要这些规范？

你已经进入“工程化思维阶段”了，这一步很关键 🔥