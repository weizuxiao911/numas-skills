---
name: 提交 PR
description: 把本地修复推送到用户 fork 并向上游原仓库发起 Pull Request (含 PR 描述), 由用户确认后执行
---

# 提交 PR

把已完成的修复推送到用户自己的 fork, 并向上游原仓库发起 Pull Request.

## 前置检查

1. 确认当前项目 = 任务项目 (workdir)
2. `git remote -v` 确认: `origin` = 用户 fork, `upstream` = 原仓库
3. `git status` 确认改动已提交 (未提交 → 先与用户确认提交)
4. 确认目标分支 (issue 里通常写明, 如 `oss-camp2026`)

## 执行流程

### 1. 推送分支 (用户确认后)

```bash
git push origin <当前分支>
```

- 只推送到用户自己的 fork (origin), **绝不直接推 upstream**

### 2. 起草 PR 内容 (先给用户确认)

准备 PR 标题与描述:
- 标题: 一句话说明解决的问题
- 描述: 关联 issue (`Closes #<n>`) / 改动摘要 / 测试方式

**把草稿给用户确认后**再创建.

### 3. 创建 PR

```bash
gh pr create --repo <原仓库> --base <目标分支> --head <user>:<分支> \
  --title "<标题>" --body "<描述>"
```

### 4. 汇报

- 给出 PR 链接
- 提醒用户: 等待维护者 review; 后续按 review 意见迭代 (在 fork 上继续提交, PR 自动更新)

## 注意事项

- **绝不直接向原仓库推送** (无权限且不合规)
- PR 描述里关联 issue (Closes/Fixes #n), 便于维护者追踪
- 创建 PR 前必须让用户确认标题与描述
