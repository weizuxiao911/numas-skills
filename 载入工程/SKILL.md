---
name: 载入工程
description: 把任务仓库 fork 到用户 GitHub 账号并 clone 到指定目录, 配置双远程 (origin=fork, upstream=原仓库)
---

# 载入工程

把开源贡献任务的目标仓库 **fork 到用户自己的 GitHub 账号**, clone 到用户指定目录,
并配置**双远程**(origin=fork, upstream=原仓库) 便于后续提交 PR 与同步上游.

## 前置检查

1. 执行 `gh auth status` 确认已登录; 未登录 → 提示用户先完成「开发环境检查」技能
2. 从任务消息里读取 `clone 目标目录: <dir>` (用户在欢迎页已选择); 缺失 → 询问用户

## 执行流程

### 1. fork + clone

在目标目录下执行 (先征求用户同意, 走 permission 审批):

```bash
cd <clone 目标目录>
gh repo fork <原仓库 URL> --clone
```

- `gh repo fork` 默认 fork 到当前登录账号; `--clone` 同时 clone 到当前目录
- clone 出的目录名 = 仓库名 (如 `vsag`), 完整路径 = `<目标目录>/<仓库名>`

### 2. 配置双远程

进入 clone 出的项目目录, 确认/配置:

```bash
git remote -v                       # 确认 origin 指向用户自己的 fork
git remote add upstream <原仓库 URL> # 若 upstream 不存在则添加
```

- `origin` = 用户自己的 fork (提交/推送目标)
- `upstream` = 原仓库 (PR 目标 + 后续同步上游)

### 3. 汇报

向用户简报:
- fork 地址: `https://github.com/<user>/<repo>`
- 本地项目路径: `<目标目录>/<仓库名>`
- 双远程配置结果
- 提示可以进行下一步「修复问题」

## 注意事项

- 所有写操作 (fork / clone / remote) 必须先经用户确认 (permission 审批)
- **clone 目标目录必须是用户明确选择的**, 不要自行决定位置
- 网络失败可重试; 如需镜像等替代方案, 先与用户确认
