# Git 最佳实践使用手册

> 本文档基于 Google Git 规范、Conventional Commits 标准及业界最佳实践，旨在规范团队协作，提升代码质量和开发效率。

---

## 目录

- [一、分支管理规范](#一分支管理规范)
  - [1.1 分支类型](#11-分支类型)
  - [1.2 分支命名规范](#12-分支命名规范)
  - [1.3 分支生命周期管理](#13-分支生命周期管理)
- [二、提交信息规范](#二提交信息规范)
  - [2.1 Commit Message 格式](#21-commit-message-格式)
  - [2.2 提交类型（Type）](#22-提交类型type)
  - [2.3 作用域（Scope）](#23-作用域scope)
  - [2.4 主题（Subject）](#24-主题subject)
  - [2.5 正文（Body）](#25-正文body)
  - [2.6 脚注（Footer）](#26-脚注footer)
  - [2.7 提交示例](#27-提交示例)
- [三、Rebase 使用规范](#三rebase-使用规范)
  - [3.1 Rebase 的基本原则](#31-rebase-的基本原则)
  - [3.2 何时使用 Rebase](#32-何时使用-rebase)
  - [3.3 交互式 Rebase 操作](#33-交互式-rebase-操作)
  - [3.4 Rebase 注意事项](#34-rebase-注意事项)
- [四、提交约束与最佳实践](#四提交约束与最佳实践)
  - [4.1 提交原子性](#41-提交原子性)
  - [4.2 代码质量保证](#42-代码质量保证)
  - [4.3 提交频率](#43-提交频率)
  - [4.4 提交前检查清单](#44-提交前检查清单)
- [五、团队协作工作流](#五团队协作工作流)
  - [5.1 Git Flow 工作流](#51-git-flow-工作流)
  - [5.2 功能开发流程](#52-功能开发流程)
  - [5.3 代码审查规范](#53-代码审查规范)
  - [5.4 冲突处理原则](#54-冲突处理原则)
- [六、高级实践与工具](#六高级实践与工具)
  - [6.1 Git Hooks 使用](#61-git-hooks-使用)
  - [6.2 标签管理](#62-标签管理)
  - [6.3 自动化检查](#63-自动化检查)
- [七、常见问题与解决方案](#七常见问题与解决方案)

---

## 一、分支管理规范

### 1.1 分支类型

#### 主分支（Main/Master）
- **用途**：存放稳定可部署的生产代码
- **保护规则**：
  - 禁止直接推送代码
  - 必须通过 Pull Request/Merge Request 合并
  - 必须通过代码审查
  - 必须通过 CI/CD 检查

#### 开发分支（Develop）
- **用途**：日常开发的主分支，包含最新开发的功能和修复
- **命名**：`develop`
- **更新频率**：频繁更新，作为功能集成分支

#### 功能分支（Feature）
- **用途**：开发新功能
- **命名格式**：`feature/功能描述` 或 `feature/模块名-功能描述`
- **创建来源**：从 `develop` 分支创建
- **合并目标**：合并回 `develop` 分支
- **示例**：
  - `feature/user-authentication`
  - `feature/payment-gateway-integration`
  - `feature/ble-protocol-v2`

#### 修复分支（Fix/Hotfix）
- **修复分支（Fix）**：用于修复开发过程中的 Bug
  - **命名格式**：`fix/问题描述` 或 `fix/模块名-问题描述`
  - **创建来源**：从 `develop` 分支创建
  - **合并目标**：合并回 `develop` 分支
  - **示例**：`fix/login-validation-error`

- **热修复分支（Hotfix）**：用于紧急修复生产环境问题
  - **命名格式**：`hotfix/问题描述`
  - **创建来源**：从 `main` 分支创建
  - **合并目标**：合并回 `main` 和 `develop` 分支
  - **示例**：`hotfix/payment-security-patch`

#### 发布分支（Release）
- **用途**：准备新版本发布，进行版本号更新、文档完善、测试等
- **命名格式**：`release/版本号`
- **创建来源**：从 `develop` 分支创建
- **合并目标**：合并回 `main` 和 `develop` 分支
- **示例**：`release/v1.2.0`

#### 实验分支（Experimental/Spike）
- **用途**：进行技术探索、概念验证
- **命名格式**：`experimental/功能描述` 或 `spike/技术验证`
- **示例**：`experimental/new-architecture`

### 1.2 分支命名规范

#### 命名规则
1. **使用小写字母**：分支名统一使用小写
2. **使用连字符分隔**：多个单词使用 `-` 连接，不使用下划线或驼峰
3. **具有描述性**：分支名应清晰表达其目的
4. **避免特殊字符**：不使用空格、中文等特殊字符
5. **长度限制**：建议不超过 50 个字符

#### 命名示例

✅ **正确示例**：
```
feature/user-login
fix/ble-connection-timeout
hotfix/payment-gateway-crash
release/v2.0.0
experimental/new-protocol
```

❌ **错误示例**：
```
Feature/UserLogin           # 包含大写
fix_ble_timeout            # 使用下划线
feature/user login         # 包含空格
fix/修复登录bug             # 包含中文
feature/very-long-branch-name-that-exceeds-recommended-length  # 过长
```

### 1.3 分支生命周期管理

#### 创建分支
```bash
# 从 develop 创建功能分支
git checkout develop
git pull origin develop
git checkout -b feature/user-authentication

# 从 main 创建热修复分支
git checkout main
git pull origin main
git checkout -b hotfix/security-patch
```

#### 删除分支
- **本地分支**：合并完成后立即删除
  ```bash
  git branch -d feature/user-authentication
  ```

- **远程分支**：合并完成后删除远程分支
  ```bash
  git push origin --delete feature/user-authentication
  ```

#### 分支同步
- 定期从主分支拉取最新代码，保持分支同步
- 使用 `rebase` 而不是 `merge` 来保持历史整洁（见 Rebase 规范章节）

---

## 二、提交信息规范

规范的 Commit Message 能够：
- 📖 快速理解代码变更意图
- 🔍 方便追溯问题和代码审查
- 🤖 自动生成变更日志
- 🚀 触发自动化流程（如发布）

### 2.1 Commit Message 格式

遵循 **Conventional Commits** 规范，标准格式如下：

```
<type>(<scope>): <subject>

<body>

<footer>
```

#### 格式说明

- **Header（必需）**：`<type>(<scope>): <subject>`
  - `type`：提交类型（必填）
  - `scope`：影响范围（可选）
  - `subject`：简短描述（必填）

- **Body（可选）**：详细描述修改内容、动机和实现细节

- **Footer（可选）**：关联 Issue、Breaking Changes 等

### 2.2 提交类型（Type）

| Type | 说明 | 示例 |
|------|------|------|
| `feat` | 新增功能 | `feat(auth): 添加用户登录功能` |
| `fix` | 修复 Bug | `fix(ble): 修复连接超时问题` |
| `docs` | 文档更新 | `docs: 更新 API 使用文档` |
| `style` | 代码格式调整（不影响功能） | `style: 格式化代码` |
| `refactor` | 代码重构（不新增功能或修复 Bug） | `refactor(charger): 重构充电控制逻辑` |
| `perf` | 性能优化 | `perf(ble): 优化数据传输性能` |
| `test` | 添加或修改测试用例 | `test(power): 添加电源管理单元测试` |
| `chore` | 构建过程或辅助工具的变动 | `chore: 更新依赖包版本` |
| `build` | 构建系统或外部依赖的变更 | `build: 更新 CMakeLists.txt` |
| `ci` | CI/CD 配置或脚本的修改 | `ci: 添加自动化测试流程` |
| `revert` | 回滚之前的提交 | `revert: 回滚 feat(auth): 添加登录功能` |

### 2.3 作用域（Scope）

作用域用于说明本次提交影响的模块或功能范围。

#### 命名规范
- 使用小写字母
- 使用名词，如模块名、组件名
- 可选，但推荐使用以提高可读性

#### 常见作用域示例

**本项目示例**：
- `ble`：蓝牙相关
- `charger`：充电器相关
- `power`：电源管理
- `protocol`：协议相关
- `api`：API 接口
- `ui`：用户界面

**示例**：
```
feat(ble): 添加设备配对功能
fix(charger): 修复充电状态异常
refactor(power): 重构电源管理模块
```

### 2.4 主题（Subject）

#### 编写规则

1. **使用祈使句**：以动词开头，如“添加”、“修复”、“更新”
   - ✅ `添加用户认证功能`
   - ❌ `添加了用户认证功能` 或 `修复了 Bug`

2. **首字母小写**：第一个字母小写（除非是专有名词）
   - ✅ `fix(ble): 修复连接超时问题`
   - ❌ `fix(ble): 修复连接超时问题`（首字母大写）

3. **结尾不加句号**：不要在末尾添加句号或其他标点

4. **简洁明了**：建议不超过 50 个字符，清晰描述做了什么

5. **避免冗余信息**：不要重复 type 和 scope 中已表达的信息

#### 示例对比

✅ **好的 Subject**：
```
feat(auth): 实现 JWT 身份验证
fix(ble): 修复连接超时问题
docs: 更新安装说明
```

❌ **差的 Subject**：
```
feat(auth): 新增功能（太模糊）
fix(ble): bug修复（不够具体）
docs: 更新（未说明更新内容）
feat(auth): 实现了 JWT 身份验证功能，包括 token 生成和验证（太长）
```

### 2.5 正文（Body）

#### 何时需要 Body

以下情况建议添加 Body：
- 提交涉及多个文件或复杂变更
- 需要解释“为什么”而不是“做了什么”
- 涉及破坏性变更（Breaking Changes）
- 修复复杂 Bug 时需要说明原因

#### 编写规则

1. **解释动机**：说明为什么要做这个变更
2. **对比差异**：与之前版本的主要差异
3. **每行不超过 72 个字符**：便于阅读和 Git 显示
4. **使用空行分隔段落**：提高可读性

#### 示例

```
feat(ble): 添加设备自动重连功能

添加了当蓝牙连接断开时的自动重连机制。
- 实现指数退避重连策略
- 最大重连次数限制为 5 次
- 重连间隔：1s, 2s, 4s, 8s, 16s

解决了用户需要手动重新连接设备的问题。
```

### 2.6 脚注（Footer）

#### 用途

1. **关联 Issue**：
   ```
   Closes #123
   Fixes #456
   Refs #789
   ```

2. **破坏性变更说明**：
   ```
   BREAKING CHANGE: API 接口变更
   
   原来的 `/api/v1/users` 接口已废弃，请使用 `/api/v2/users`。
   新接口需要额外的认证参数。
   ```

3. **相关人员**：
   ```
   Co-authored-by: Name <email@example.com>
   Reviewed-by: Name <email@example.com>
   ```

### 2.7 提交示例

#### 示例 1：简单功能添加

```
feat(charger): 添加快充模式支持
```

#### 示例 2：修复 Bug（带说明）

```
fix(ble): 修复设备连接超时问题

修复了当设备距离过远时连接超时的 Bug。
增加了连接超时检测机制，超时后自动重试。

Fixes #123
```

#### 示例 3：代码重构（详细说明）

```
refactor(power): 重构电源管理模块

将电源管理逻辑拆分为独立的子模块：
- power_state.c: 状态管理
- power_monitor.c: 监控功能
- power_control.c: 控制逻辑

提高了代码的可维护性和可测试性。
```

#### 示例 4：破坏性变更

```
feat(api): 重构充电器 API 接口

BREAKING CHANGE: 充电器控制 API 接口变更

旧的接口 `POST /api/charger/start` 已废弃。
新的接口为 `POST /api/v2/charger/control`，请求体格式已变更。

迁移指南：
- 将 endpoint 改为 `/api/v2/charger/control`
- 请求体新增 `mode` 字段：`{"mode": "fast", "voltage": 5.0}`

Closes #456
```

#### 示例 5：文档更新

```
docs: 更新 BLE 协议文档

- 添加新命令格式说明
- 更新错误码定义
- 补充示例代码

Refs #789
```

---

## 三、Rebase 使用规范

### 3.1 Rebase 的基本原则

#### ✅ 使用 Rebase 的场景
1. **个人分支同步主分支**：保持提交历史线性
2. **合并多个提交**：整理提交历史
3. **修改提交信息**：修正错误或不规范的提交
4. **重新排序提交**：优化提交顺序

#### ❌ 禁止使用 Rebase 的场景
1. **公共分支**：不要对 `main`、`develop` 等公共分支进行 rebase
2. **他人正在使用的分支**：不要 rebase 他人可能正在基于此分支工作的分支
3. **已合并到主分支的历史**：不要修改已经合并的历史

### 3.2 何时使用 Rebase

#### 场景 1：同步主分支到功能分支

```bash
# 在功能分支上
git checkout feature/user-authentication
git fetch origin
git rebase origin/develop
```

**优势**：保持线性历史，避免不必要的合并提交

#### 场景 2：提交 Pull Request 前整理历史

```bash
# 合并多个小提交为一个大提交
git rebase -i HEAD~5  # 整理最近 5 个提交
```

**优势**：让 PR 更容易审查，历史更清晰

### 3.3 交互式 Rebase 操作

#### 常用操作

```bash
git rebase -i <commit-id>  # 或 HEAD~n（n 为提交数量）
```

#### 交互式 Rebase 命令

| 命令 | 说明 | 使用场景 |
|------|------|----------|
| `pick` | 使用该提交（默认） | 保留提交 |
| `reword` | 使用并修改提交信息 | 修正提交信息 |
| `edit` | 使用但暂停以修改 | 修改提交内容 |
| `squash` | 使用但合并到上一个提交 | 合并多个提交 |
| `fixup` | 类似 squash，但丢弃提交信息 | 合并并丢弃提交信息 |
| `drop` | 删除提交 | 删除不需要的提交 |
| `exec` | 执行 shell 命令 | 在每个提交后执行命令 |

#### 操作示例

**示例：合并最近 3 个提交**

```bash
git rebase -i HEAD~3
```

编辑器内容：
```
pick abc1234 feat: 添加用户登录
squash def5678 fix: 修复登录验证
squash ghi9012 style: 格式化代码
```

结果：3 个提交合并为 1 个提交

**示例：修改提交信息**

```bash
git rebase -i HEAD~3
```

编辑器内容：
```
pick abc1234 feat: 添加用户登录
reword def5678 fix: 修复登录验证
pick ghi9012 style: 格式化代码
```

结果：第二个提交的信息会被打开编辑器供修改

### 3.4 Rebase 注意事项

#### ⚠️ 冲突处理

1. **解决冲突**：
   ```bash
   # 发生冲突后，Git 会暂停
   # 1. 手动解决冲突
   # 2. 标记为已解决
   git add <resolved-files>
   # 3. 继续 rebase
   git rebase --continue
   ```

2. **中止 Rebase**：
   ```bash
   git rebase --abort  # 回到 rebase 前的状态
   ```

3. **跳过当前提交**：
   ```bash
   git rebase --skip  # 跳过当前提交（谨慎使用）
   ```

#### ⚠️ 强制推送

**重要**：Rebase 会改写提交历史，推送到远程仓库需要使用强制推送。

**安全做法**：
```bash
# 使用 --force-with-lease（推荐）
git push --force-with-lease origin feature/user-authentication
```

**区别**：
- `--force`：强制推送，可能覆盖他人的提交
- `--force-with-lease`：仅在远程分支没有新的提交时强制推送，更安全

#### ⚠️ 团队协作建议

1. **沟通**：在 rebase 公共分支前，通知团队成员
2. **时间窗口**：选择团队活动较少的时间进行 rebase
3. **通知机制**：通过团队通信工具通知 rebase 操作

---

## 四、提交约束与最佳实践

### 4.1 提交原子性

#### 原则
每次提交应该是一个**完整的、逻辑独立的变更单元**。

#### 好的原子性提交
✅ **正确**：
- 一次提交只添加一个功能
- 一次提交只修复一个问题
- 功能代码和测试代码分开提交（或同一提交包含相关测试）

#### 差的提交
❌ **错误**：
- 一次提交包含多个不相关的功能
- 一次提交同时包含功能添加和 Bug 修复
- 提交中包含调试代码或临时文件

#### 示例

✅ **好的提交**：
```
feat(ble): 添加设备扫描功能
fix(charger): 修复充电状态显示错误
test(power): 添加电源管理单元测试
```

❌ **差的提交**：
```
feat: 添加多个功能
  - 添加用户登录
  - 添加设备扫描
  - 修复充电 Bug
  - 更新文档
```

### 4.2 代码质量保证

#### 提交前检查

1. **代码格式**：
   ```bash
   # 运行代码格式化工具
   npm run format  # 或相应的格式化命令
   ```

2. **代码检查**：
   
   ```bash
   # 运行 Linter
   npm run lint
   ```
```
   
3. **单元测试**：
   ```bash
   # 运行测试套件
   npm test
```

4. **编译检查**：
   ```bash
   # 确保代码能够编译通过
   npm run build
   ```

#### 禁止提交的内容

- ❌ 调试代码（`console.log`、`printf` 等）
- ❌ 注释掉的代码
- ❌ 临时文件（`.tmp`、`.bak` 等）
- ❌ 敏感信息（密码、密钥等）
- ❌ 大型二进制文件（除非必要）
- ❌ IDE 配置文件（应添加到 `.gitignore`）

### 4.3 提交频率

#### 原则
**频繁提交，但不频繁推送**

#### 建议

1. **完成一个小功能就提交**：
   - 完成一个函数
   - 完成一个模块
   - 修复一个小 Bug

2. **阶段性推送**：
   - 完成一个完整功能后推送到远程
   - 准备创建 Pull Request 前推送
   - 需要与他人协作时推送

#### 好处

- 📝 便于追溯问题
- 🔄 便于回滚
- 🤝 减少合并冲突
- 📊 便于代码审查

### 4.4 提交前检查清单

在提交代码前，请确认：

#### 代码层面
- [ ] 代码能够编译通过
- [ ] 所有测试通过
- [ ] 代码符合项目规范
- [ ] 已删除调试代码和临时文件
- [ ] 代码已格式化

#### 提交信息层面
- [ ] Commit Message 符合规范
- [ ] 提交类型（type）正确
- [ ] 提交主题清晰明了
- [ ] 复杂变更已添加 Body 说明
- [ ] 关联了相关 Issue（如有）

#### 分支管理层面
- [ ] 分支命名符合规范
- [ ] 已从主分支同步最新代码
- [ ] 提交历史已整理（如需要）

---

## 五、团队协作工作流

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

---

## 六、高级实践与工具

### 6.1 Git Hooks 使用

#### Pre-commit Hook

在提交前自动检查代码质量。

**示例：`.git/hooks/pre-commit`**
```bash
#!/bin/sh
# 运行代码检查
npm run lint
if [ $? -ne 0 ]; then
    echo "Lint 检查失败，请修复后再提交"
    exit 1
fi

# 运行测试
npm test
if [ $? -ne 0 ]; then
    echo "测试失败，请修复后再提交"
    exit 1
fi
```

#### Commit-msg Hook

检查 Commit Message 格式。

**使用工具**：
- [commitlint](https://commitlint.js.org/)：检查提交信息格式
- [gitlint](https://jorisroovers.com/gitlint/)：Git 提交信息检查工具

### 6.2 标签管理

#### 标签类型

1. **轻量标签**：指向特定提交的引用
   ```bash
   git tag v1.0.0
   ```

2. **附注标签**：包含标签信息的完整对象
   ```bash
   git tag -a v1.0.0 -m "版本 1.0.0 发布"
   ```

#### 标签命名规范

- **语义化版本**：遵循 [SemVer](https://semver.org/lang/zh-CN/) 规范
  - 格式：`v主版本号.次版本号.修订号`
  - 示例：`v1.0.0`、`v1.2.3`、`v2.0.0-beta.1`

- **标签前缀**：使用 `v` 前缀（如 `v1.0.0`）

#### 标签操作

```bash
# 创建标签
git tag -a v1.0.0 -m "版本 1.0.0"

# 推送标签
git push origin v1.0.0

# 推送所有标签
git push origin --tags

# 删除标签
git tag -d v1.0.0
git push origin :refs/tags/v1.0.0
```

### 6.3 自动化检查

#### CI/CD 集成

在 CI/CD 流程中集成以下检查：

1. **代码质量检查**：
   - Linter 检查
   - 代码覆盖率检查
   - 安全检查

2. **Commit Message 检查**：
   - 格式验证
   - 类型验证

3. **自动化测试**：
   - 单元测试
   - 集成测试
   - E2E 测试

#### 推荐工具

- **commitlint**：Commit Message 格式检查
- **husky**：Git Hooks 管理
- **prettier**：代码格式化
- **eslint**：代码质量检查

---

## 七、常见问题与解决方案

### Q1: 提交后发现 Commit Message 写错了怎么办？

**解决方案**：

```bash
# 修改最近一次提交的信息
git commit --amend -m "正确的提交信息"

# 如果已推送，需要强制推送
git push --force-with-lease origin <branch-name>
```

⚠️ **注意**：只修改尚未合并到主分支的提交。

### Q2: 如何撤销已提交的更改？

**场景 1：撤销最近的提交，保留更改**
```bash
git reset --soft HEAD~1
```

**场景 2：撤销最近的提交，丢弃更改**
```bash
git reset --hard HEAD~1
```

**场景 3：创建一个新的提交来撤销更改**
```bash
git revert <commit-id>
```

### Q3: 如何将多个提交合并为一个？

**解决方案**：使用交互式 Rebase

```bash
git rebase -i HEAD~3  # 合并最近 3 个提交
# 将需要合并的提交改为 squash 或 fixup
```

### Q4: 如何在 Rebase 过程中处理冲突？

**解决步骤**：

1. 查看冲突文件：`git status`
2. 手动解决冲突
3. 标记已解决：`git add <files>`
4. 继续 Rebase：`git rebase --continue`

如果无法解决：
```bash
git rebase --abort  # 中止 rebase，回到之前的状态
```

### Q5: 误将代码提交到错误的分支怎么办？

**解决方案**：

```bash
# 1. 在当前分支创建临时分支保存更改
git branch temp-branch

# 2. 切换到错误的分支，重置到提交前
git checkout wrong-branch
git reset --hard HEAD~1

# 3. 切换到正确的分支
git checkout correct-branch

# 4. 应用更改
git cherry-pick <commit-id>
# 或
git merge temp-branch

# 5. 删除临时分支
git branch -d temp-branch
```

### Q6: 如何查看某个文件的提交历史？

```bash
git log <file-path>
git log --oneline <file-path>  # 简洁模式
git log -p <file-path>  # 显示详细变更
```

### Q7: 如何查找引入 Bug 的提交？

**使用 git bisect**：

```bash
# 开始二分查找
git bisect start

# 标记为坏的提交（当前）
git bisect bad

# 标记为好的提交（已知正常的提交）
git bisect good <commit-id>

# Git 会自动切换到中间提交，测试后标记 good 或 bad
git bisect good  # 或 bad

# 重复直到找到问题提交
# 完成后重置
git bisect reset
```

---

## 附录

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

### C. 参考资源

- [Conventional Commits](https://www.conventionalcommits.org/)
- [Semantic Versioning](https://semver.org/)
- [Google Git Style Guide](https://google.github.io/eng-practices/review/)
- [Git Flow](https://nvie.com/posts/a-successful-git-branching-model/)
- [GitHub Flow](https://guides.github.com/introduction/flow/)

---

**文档版本**：v1.0.0  
**最后更新**：2024年  
**维护者**：开发团队

---

> 💡 **提示**：本文档是团队协作的指导原则，具体项目可根据实际情况调整。如有疑问或建议，请及时反馈。
