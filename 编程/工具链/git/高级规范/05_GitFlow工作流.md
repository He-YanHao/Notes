### 5.1 Git Flow 工作流

#### 工作流图示

```
main (生产环境)
  ↑
  ├── hotfix/* (紧急修复)
  │     ↓
  │   main + develop
  │
develop (开发环境)
  ↑
  ├── feature/* (功能开发)
  │     ↓
  │   develop
  │
  └── release/* (版本发布)
        ↓
      main + develop
```

#### 详细流程

1. **功能开发**

    ```
    develop → feature/xxx → develop
    ```

2. **Bug 修复**

    ```
    develop → fix/xxx → develop
    ```

3. **热修复**

    ```
    main → hotfix/xxx → main + develop
    ```

4. **版本发布**

    ```
    develop → release/v1.0.0 → main + develop
    ```

### 5.2 功能开发流程

#### 标准流程

1. **创建功能分支**

    ```bash
    git checkout develop
    git pull origin develop
    git checkout -b feature/user-authentication
    ```

2. **开发与提交**

    ```bash
    # 进行开发工作
    git add .
    git commit -m "feat(auth): 添加用户登录功能"
    # ... 多次提交
    ```

3. **同步主分支**

    ```bash
    git fetch origin
    git rebase origin/develop  # 或 git merge origin/develop
    ```

4. **整理提交历史**（可选）

    ```bash
    git rebase -i HEAD~n  # 合并或整理提交
    ```

5. **推送分支**

    ```bash
    git push origin feature/user-authentication
    # 如果使用了 rebase，可能需要强制推送
    git push --force-with-lease origin feature/user-authentication
    ```

6. **创建 Pull Request**

    - 在 Git 平台（GitHub/GitLab 等）创建 PR
    - 填写 PR 描述
    - 请求代码审查

7. **代码审查与修改**

    - 根据审查意见修改代码
    - 继续提交并推送（PR 会自动更新）

8. **合并与清理**

    - 审查通过后，合并到目标分支
    - 删除功能分支（本地和远程）

### 5.3 代码审查规范

#### Pull Request 描述模板

```markdown
## 变更说明
简要描述本次 PR 的目的和主要变更。

## 变更类型
- [ ] 新功能
- [ ] Bug 修复
- [ ] 代码重构
- [ ] 文档更新
- [ ] 性能优化
- [ ] 其他

## 相关 Issue
Closes #123

## 测试说明
- 测试环境：xxx
- 测试步骤：xxx
- 测试结果：xxx

## 截图（如适用）
[截图]

## 检查清单
- [ ] 代码已通过所有测试
- [ ] 代码符合项目规范
- [ ] 已更新相关文档
- [ ] Commit Message 符合规范
```

#### 审查要点

**审查者关注**：

- ✅ 代码质量和规范
- ✅ 功能完整性和正确性
- ✅ 测试覆盖率
- ✅ 性能和安全性
- ✅ 文档更新

**提交者注意**：

- ✅ 及时响应审查意见
- ✅ 认真处理反馈
- ✅ 保持 PR 的可审查性（不要过大）

### 5.4 冲突处理原则

#### 预防冲突

1. **频繁同步**：定期从主分支拉取最新代码
2. **沟通协调**：避免多人同时修改同一文件
3. **小粒度提交**：频繁提交，减少冲突范围

#### 解决冲突

1. **拉取最新代码**

    ```bash
    git fetch origin
    git rebase origin/develop  # 或 merge
    ```

2. **定位冲突文件**

    ```bash
    git status  # 查看冲突文件
    ```

3. **手动解决冲突**

    - 打开冲突文件
    - 查找冲突标记：`<<<<<<<`、`=======`、`>>>>>>>`
    - 手动合并代码，删除标记

4. **标记已解决**

    ```bash
    git add <resolved-files>
    git rebase --continue  # 如果是 rebase
    # 或
    git commit  # 如果是 merge
    ```

#### 冲突解决策略

- **优先保留主分支代码**：当不确定时，优先保留主分支的代码逻辑
- **沟通确认**：与冲突代码的作者沟通，确认正确的解决方案
- **测试验证**：解决冲突后，充分测试确保功能正常







这段内容是在描述一种**规范化团队协作开发流程**，核心基于一个经典模型：

## 🌿 Git Flow 工作流

Git Flow 是一种“分支分层管理策略”，用于中大型团队开发。

它解决的问题是：

>   如何在多人协作时，既能快速开发，又能保证线上稳定？

------

# 一、5.1 Git Flow 工作流结构解释

你给出的图结构本质是一个“分层分支体系”。

我们按层理解。

------

## 🟢 main（生产环境）

-   永远保持“可发布”
-   对应线上版本
-   禁止随便改

它只接受：

-   release 分支合并
-   hotfix 分支合并

------

## 🟡 develop（开发环境）

-   所有功能最终都会汇聚到这里
-   是“下一版本”的集成环境
-   比 main 新，但不一定稳定

------

## 🔵 feature/*（功能分支）

用于：

```text
开发新功能
```

流程：

```text
develop → feature/xxx → develop
```

意思是：

-   从 develop 拉分支
-   开发
-   合并回 develop

------

## 🔴 hotfix/*（紧急修复）

用于：

```text
线上紧急 bug
```

流程：

```text
main → hotfix/xxx → main + develop
```

为什么要同时合并到 develop？

因为：

>   否则下个版本会丢失修复内容。

------

## 🟣 release/*（版本发布）

用于：

-   准备发版
-   做最后测试
-   修小 bug

流程：

```text
develop → release/v1.0.0 → main + develop
```

它相当于：

>   “冻结版本，准备上线”

------

# 二、5.2 功能开发流程解析

这是标准企业流程。

------

## ① 创建功能分支

```bash
git checkout develop
git pull origin develop
git checkout -b feature/user-authentication
```

目的：

-   确保基于最新代码开发
-   不污染主分支

------

## ② 开发与提交

```bash
git commit -m "feat(auth): 添加用户登录功能"
```

这里用的是：

```
Conventional Commits 规范
```

例如：

-   feat = 新功能
-   fix = 修复
-   refactor = 重构

------

## ③ 同步主分支

```bash
git rebase origin/develop
```

目的是：

>   避免未来大冲突

你会发现现代团队更推荐 rebase 而不是 merge。

------

## ④ 整理提交历史

```bash
git rebase -i
```

这是进阶操作：

-   合并多个小提交
-   清理无意义 commit
-   保持历史干净

------

## ⑤ 强制推送（安全方式）

```bash
git push --force-with-lease
```

注意：

>   只有在 rebase 后才需要

------

## ⑥ 创建 PR

在平台上创建 PR：

-   描述功能
-   关联 Issue
-   请求审查

在：

-   GitHub
-   GitLab

这是标准流程。

------

# 三、5.3 代码审查规范

这是“质量控制机制”。

------

## PR 模板的作用

它强制开发者回答：

-   改了什么？
-   为什么改？
-   怎么测试？
-   是否影响其他模块？

防止出现：

```text
“修复了一些问题”
```

这种毫无意义的 PR 描述。

------

## 审查者关注

-   逻辑是否正确
-   是否有边界问题
-   是否有性能风险
-   是否符合架构

------

## 提交者注意

-   PR 不要太大
-   及时回复评论
-   不要情绪化

------

# 四、5.4 冲突处理原则

冲突不可避免。

关键是：

>   预防优于解决

------

## 预防方式

1.  经常同步
2.  小提交
3.  沟通

------

## 冲突本质

当 Git 发现：

```text
同一行代码被两个人修改
```

就会出现：

```
<<<<<<<
=======
>>>>>>>
```

你必须手动决定保留哪部分。

------

## 冲突解决策略重点

非常关键的一句话：

>   不确定时优先保留主分支逻辑

因为主分支代表：

```
已验证的稳定代码
```

------

# 五、整体理解

这整段内容描述的是一个：

>   企业级标准协作流程体系

包括：

-   分支管理
-   代码审查
-   提交规范
-   冲突处理
-   发布策略

------

# 六、适合谁？

Git Flow 适合：

-   多人团队
-   有明确版本发布周期
-   有测试阶段
-   有线上环境

不太适合：

-   个人项目
-   小工具
-   高频部署项目

------

# 七、现代趋势

很多公司现在开始转向：

-   Trunk-Based Development（主干开发）

因为 Git Flow 分支较多，管理成本高。

------

# 八、用一句话总结

这段内容本质是：

>   一套防止“多人协作失控”的工程纪律。

它的核心目标只有一个：

```
稳定 + 可追踪 + 可回滚
```

------

如果你愿意，我可以下一步讲：

-   Git Flow 和 Trunk-Based 的优缺点对比
-   嵌入式项目适不适合 Git Flow？
-   为什么很多开源项目只有 main + feature？

你现在已经在接触“工程管理层面”的知识了，这是开发者进阶的关键阶段 🔥