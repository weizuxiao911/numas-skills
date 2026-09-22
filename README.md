# numas-skills

Numas 远程 skill 分发仓库。

[numas](https://github.com/weizuxiao911/numas)(opencode fork 桌面应用) 首次启动时会自动创建
`~/.config/opencode/numas.json`, 其中 `skills.urls` 指向本仓库的 raw 地址;
numas 启动时按 URL 拉取 skill 到本地缓存并加载, 供 AI 引导流程使用。

## 仓库结构

```
index.json          # skill 索引 (name / files / version)
<skill-name>/
  SKILL.md          # skill 正文 (frontmatter: name + description)
```

- `index.json` 的 `version` 用于缓存比对: 改动 skill 内容后**必须递增 version**, 客户端才会重新拉取
- skill 目录名必须与 `index.json` 中的 `name` 一致 (拉取路径 = `{url}/{name}/{file}`)

## 镜像

- GitHub (主): `https://raw.githubusercontent.com/weizuxiao911/numas-skills/main`
- Gitee (镜像): `https://gitee.com/weizuxiao911/numas-skills/raw/main`

## 维护

```bash
# 修改 skill 后
# 1. 递增 index.json 里对应 skill 的 version
# 2. 提交推送
git add -A && git commit -m "update: <skill> v<n>" && git push
```
