---
name: 提交PR
description: 引导用户完成对 issue 修复结果的验收, 直到用户认可并授权提交 PR 时才执行操作, 过程中用 question 提问用户正确认知问题和结果, 谨慎执行 PR 提交
---

# 提交PR

**核心目标**: 引导用户**验收**本次 issue 修复结果; 只有**用户认可并明确授权**后, 才执行 PR 提交操作。
**AI 对结果负责的态度, 谨慎执行; 绝不擅自提交.**

## 执行原则

- **用户认可才提交**: 提交 PR 是不可逆对外动作, 必须用户明确授权
- **用 question 确认认知**: 引导用户正确认知"改了什么 / 是否符合 issue 验收标准 / 是否够好"
- **不催促**: 用户未认可就继续完善, 不急着发 PR
- 遵循项目根 AGENTS.md 协作规则 (日志落盘到 `.logs/{YYYYMMdd}.log`)

## 前置检查

1. 确认当前项目 = 任务项目 (workdir)
2. `git remote -v` 确认: `origin` = 用户 fork, `upstream` = 原仓库
3. `git status` 确认改动状态 (已提交? 未提交? 未跟踪文件?)

## 执行流程

### 1. 引导用户验收修复结果

- 向用户展示本次修复**完整成果**: 改动文件清单 (git diff 摘要) / 方案文档 / 测试结果
- **逐条对照 issue 验收标准**, 引导用户确认:
  - 问题现象是否已解决?
  - 是否有遗漏的边界/场景?
  - 代码质量 (格式/命名/是否夹带无关改动)?
- **用 question 逐项确认用户认知**: 如 "修复是否达到验收标准?" / "还有要调整的地方吗?"
- 用户提出意见 → 先回「执行修复」/「方案设计」修正, 再回来验收

### 2. 确认提交范围与分支

- 与用户确认:
  - 要提交的改动范围 (哪些 commit / 是否含方案文档)
  - 目标分支 (issue 里通常写明, 如 `oss-camp2026`)
- 确认 git 状态干净 (无关文件不夹带)

### 3. 推送分支 (用户授权后)

向用户说明: 将把本地分支推送到**你自己的 fork (origin)**, 绝不直接推 upstream。

```bash
git push origin <当前分支>
```

- **先取得用户授权再执行**

### 4. 起草 PR 内容 (先给用户确认)

准备 PR 标题与描述:
- 标题: 一句话说明解决的问题
- 描述: 关联 issue (`Closes #<n>`) / 改动摘要 / 测试方式
- **把草稿给用户确认后**再创建 (用 question: PR 草稿确认?)

### 5. 创建 PR (用户授权后)

```bash
gh pr create --repo <原仓库> --base <目标分支> --head <user>:<分支> \
  --title "<标题>" --body "<描述>"
```

- 创建前**最后一次确认** (question): 确认标题/描述/目标分支无误, 授权创建
- 命令 stdout 即新建 PR 的 URL, 记下它 (`https://github.com/<owner>/<repo>/pull/<n>`)

### 6. 回传 PR 链接到开源阵地 (仅任务场景)

**触发条件**: 触发消息中同时带齐 `任务ID: <id>` / `实验室编码: <labCode>` / `PR回传接口: <url>` 三项。
任一缺失 → **跳过本步** (非开源阵地任务场景, 不报错), 直接进第 7 步汇报。

从触发消息中取 (均为 UI 顶部「提交 PR」按钮注入):

- `任务ID`: 数字任务 ID (body 的 `id`)
- `实验室编码`: body 的 `labCode`
- `PR回传接口`: 完整回传地址 (形如 `https://lab.cloudlab.top/api/v0.3/open/issues/commit/pr`)

**登录令牌来源**: 宿主机 `~/.numas/cache/session.yaml` 的 `user` 段 `token` 字段
(容器内 HOME=/home → `/home/.numas/cache/session.yaml`)。只在本地读取, 不外传。

回传请求 (token 走请求头):

```bash
PR_URL="<第 5 步拿到的 PR URL>"
TASK_ID="<任务ID>"
LAB_CODE="<实验室编码>"
CALLBACK="<PR回传接口>"   # 已含 /open/issues/commit/pr
SESSION="$HOME/.numas/cache/session.yaml"

TOKEN=$(grep -E '^\s*-\s*token:' "$SESSION" | head -1 | sed -E 's/^\s*-\s*token:\s*//')

curl -sS -X POST "$CALLBACK" \
  -H 'Content-Type: application/json' \
  -H "token: $TOKEN" \
  -d "{\"labCode\":\"$LAB_CODE\",\"id\":$TASK_ID,\"pr\":\"$PR_URL\"}"
```

- 校验返回 (HTTP 2xx 且业务成功) → 视为回传成功
- **失败处理 (不阻塞用户)**: 回传失败**不影响 PR 已创建的事实**。用 question 提示用户
  "PR 已创建但回传开源阵地失败 (原因)" + 提供选项:「重试回传」/「跳过, 手动重试」;
  选重试则重新执行本步 curl
- 若 `session.yaml` 不存在或 token 为空 → 提示用户执行「开发准备」技能完成登录授权, 同样不阻塞

### 7. 汇报 + 后续

- 给出 PR 链接
- 若执行了第 6 步: 说明回传结果 (成功 / 失败待重试)
- 提醒用户: 等待维护者 review; 后续按 review 意见迭代 (在 fork 上继续提交, PR 自动更新)

## 注意事项

- **绝不直接向原仓库推送** (无权限且不合规)
- PR 描述里关联 issue (Closes/Fixes #n), 便于维护者追踪
- **创建 PR 是用户授权后才执行的动作**, 全程不擅自操作
- **回传开源阵地是 PR 创建后的独立步骤**: 仅任务场景 (带 id+labCode+回传接口) 执行;
  失败不阻塞用户 (PR 已创建即有效), 提示后按用户意愿重试
- 用户对结果负责, AI 只按授权执行 — 提交前充分引导用户认知修复结果
