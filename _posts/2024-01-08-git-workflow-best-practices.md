---
title: "Git 工作流最佳实践：从提交规范到分支策略"
date: 2024-01-08
categories: [工程效率]
tags: [Git, 团队协作, CI/CD, 代码规范]
excerpt: "规范的 Git 工作流是高效团队协作的基础。本文介绍 Conventional Commits、分支策略、以及如何通过自动化工具强制执行规范。"
---

## 提交信息规范（Conventional Commits）

一个好的提交信息应该清晰表达**做了什么**以及**为什么做**。推荐使用 [Conventional Commits](https://www.conventionalcommits.org/) 规范：

```
<type>(<scope>): <subject>

[optional body]

[optional footer]
```

### 常用类型

| Type | 含义 |
|------|------|
| `feat` | 新功能 |
| `fix` | Bug 修复 |
| `docs` | 文档变更 |
| `style` | 代码格式（不影响逻辑） |
| `refactor` | 重构（非新功能/非 Bug 修复） |
| `perf` | 性能优化 |
| `test` | 增加/修改测试 |
| `chore` | 构建/工具链变更 |
| `ci` | CI 配置变更 |

### 示例

```bash
# ✅ 好的提交信息
feat(auth): add JWT refresh token support
fix(api): handle null user_id in /users/:id endpoint
docs(readme): update docker setup instructions
perf(db): add index on users.email column

# ❌ 差的提交信息
fix bug
update
WIP
12345
```

## 分支策略

### GitHub Flow（推荐中小团队）

简洁高效，适合持续部署：

```
main (受保护，始终可部署)
  ↑
feature/xxx → PR → code review → merge → delete branch
```

**规则**：
- `main` 永远是可部署的稳定状态
- 所有开发在功能分支进行
- 通过 PR/MR 进行代码审查
- 合并后立即部署

### Git Flow（适合版本化发布）

```
main ─────────────────────────────────── v1.0 ─── v1.1
                                          ↑         ↑  
develop ──────────────────────────────────┤─────────┤
         ↑         ↑          ↑           │         │
      feature/a  feature/b  release/1.0 ──┘  hotfix/1.0.1
```

## 常用 Git 操作速查

### 分支管理

```bash
# 创建并切换到新分支
git switch -c feature/user-auth

# 同步远程最新变更（推荐使用 rebase 保持线性历史）
git fetch origin
git rebase origin/main

# 删除已合并的本地分支
git branch --merged main | grep -v main | xargs git branch -d

# 查看所有远程分支
git branch -r
```

### 修改提交历史

```bash
# 修改最近一次提交信息
git commit --amend

# 交互式 rebase：整理最近 3 个提交
git rebase -i HEAD~3
# pick → 保留
# reword → 修改提交信息
# squash → 合并到上一个提交
# drop → 删除提交

# 撤销最近一次提交（保留文件变更）
git reset --soft HEAD~1

# 撤销并丢弃变更（危险！）
git reset --hard HEAD~1
```

### Stash 的正确用法

```bash
# 暂存当前未提交的变更
git stash push -m "WIP: 用户认证模块"

# 查看 stash 列表
git stash list

# 恢复最近的 stash（并从列表删除）
git stash pop

# 恢复指定 stash（保留在列表中）
git stash apply stash@{2}
```

## 自动化规范检查

### commitlint + husky

```bash
# 安装
npm install --save-dev @commitlint/cli @commitlint/config-conventional husky

# 配置 commitlint
echo '{"extends": ["@commitlint/config-conventional"]}' > .commitlintrc.json

# 启用 husky
npx husky install
npx husky add .husky/commit-msg 'npx --no -- commitlint --edit ${1}'
```

### pre-commit hooks

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-merge-conflict
  
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.3.0
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format
```

```bash
# 安装 pre-commit
pip install pre-commit
pre-commit install
```

## PR/MR 最佳实践

### PR 描述模板

```markdown
## 变更说明
简要描述本次变更的目的和内容。

## 变更类型
- [ ] Bug 修复
- [ ] 新功能
- [ ] 重构
- [ ] 文档

## 测试方式
说明如何验证本次变更。

## 截图（如有 UI 变更）

## 相关 Issue
Closes #123
```

### Review 清单

- [ ] 代码逻辑是否正确？
- [ ] 是否有足够的测试覆盖？
- [ ] 是否有潜在的性能问题？
- [ ] 错误处理是否完善？
- [ ] 是否有安全风险？
- [ ] 文档是否需要更新？

## 总结

良好的 Git 工作流的核心是**可追溯性**和**可回滚性**：
- 规范的提交信息让历史可读、自动生成 CHANGELOG
- 分支保护规则确保主干稳定
- 自动化工具降低人工遵守规范的成本

工具再好，也需要团队达成共识并坚持执行。
