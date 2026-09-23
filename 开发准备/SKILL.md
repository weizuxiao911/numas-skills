---
name: 开发准备
description: 检查开源贡献所需开发环境 (GitHub CLI 安装与授权), 确保 gh 可用并完成授权绑定, 环境就绪后才能进入后续步骤
---

# 开发准备

确保用户本机具备开源贡献所需的基础工具链, 核心是 **GitHub CLI (`gh`)** 可用:
1. `gh` 已安装
2. `gh` 已登录 GitHub 账号, 且 token 有效 (具备 repo/fork/PR 权限)

本步骤是后续全部步骤的前置: **fork/clone、排查定位、提交 PR 都依赖 gh 可用**.

## 执行原则

- **只读检查自动执行** (不询问用户, 直接跑命令)
- **写操作 (安装 / 登录) 必须先征求用户同意** 或由用户自行执行
- 所有命令通过 shell 工具在**用户本机**执行
- 每步检查后向用户**简报结果** (成功 / 缺失 / 待处理)

## 检查流程

### 第 1 步: gh 是否安装

执行 `gh --version`

- **成功** (输出 `gh version x.y.z ...`) → 进入第 2 步
- **失败** (`command not found` / `not found`) → 引导安装:
  - macOS: `brew install gh` (若 brew 可用; 先征求用户同意再执行)
  - 无 brew / 其它平台: 引导用户访问 https://cli.github.com/ 下载安装
  - 安装完成后重新执行 `gh --version` 确认

### 第 2 步: gh 是否已授权

执行 `gh auth status`

- **已登录且 token 有效** (输出 `✓ Logged in to github.com account <user>`) → 进入第 3 步
- **未登录 / token 失效** (输出 `Failed to log in` 或 `The token in keyring is invalid`) → 引导授权:
  1. 告知用户需要执行交互式命令 `gh auth login` (需用户参与)
  2. 引导选择: **GitHub.com** → **HTTPS** → **Login with a web browser**
  3. 提示用户: 复制终端显示的 one-time code → 浏览器打开 https://github.com/login/device → 粘贴并授权
  4. 授权完成后重新执行 `gh auth status` 确认

### 第 3 步: 汇总报告

向用户简报:
- `gh` 版本
- 登录账号 (如 `weizuxiao911`)
- token 权限 scope (是否含 `repo`)
- 结论: **环境就绪** / 待处理项列表

全部通过后, 告知用户可以进行下一步 (fork 并 clone).

## 常见问题

- **brew 未安装**: 引导访问 https://brew.sh/ 安装, 或改用 https://cli.github.com/ 直接下载 gh
- **GitHub 网络访问慢/失败**: 可引导用户配置代理后重试
- **token 失效** (长期未用): 重新执行 `gh auth login` 即可
- **权限不足** (scope 缺 `repo`): 重新 `gh auth login` 并确保勾选所需 scope, 或 `gh auth refresh -s repo`
