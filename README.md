# Paper Weekly

个人论文阅读博客：每周 1–2 篇，记录 Idea / 挑战 / 方法 / 实验，随时更新、随时查阅。

页面结构仿 [audio-paper-digest](https://nanless.github.io/audio-paper-digest-blog/posts/2026-06-02-anymo-scaling-any-modality-conditional-motion/)：目录、作者、点评、摘要、开源、方法、创新、实验、细节、局限、收获。

站点（开启 GitHub Pages 后）：<https://joyokk.github.io/Paper-Weekly/>

## 本地预览

需要 [Hugo Extended](https://gohugo.io/installation/)（本机可用 `winget install Hugo.Hugo.Extended`）。`series` 填 ISO 周，例如 `2026-W34`。

```bash
git clone --recurse-submodules https://github.com/JoyOKK/Paper-Weekly.git
cd Paper-Weekly
hugo server
```

浏览器打开 <http://localhost:1313/Paper-Weekly/>。

首次克隆若主题目录是空的：`git submodule update --init --recursive`。

## 新增一篇

1. 复制 `archetypes/posts.md` 的章节骨架，写成：

   `content/posts/YYYY-MM-DD-<slug>.md`

2. Front matter 里填 `summary`、`tags`、`series`（当周区间，如 `2026-08-17 ~ 08-23`）、`venue`。

3. 本地 `hugo server` 预览，或直接 push，`main` 上的 GitHub Actions 会发布。

手机或网页改 GitHub 上的 markdown 再 commit，同样会自动部署。

## 仓库结构

```
content/posts/          # 阅读笔记（Markdown）
archetypes/posts.md     # 新文章模板
static/slides/          # 可选汇报 HTML
themes/PaperMod/        # 主题（git submodule）
hugo.toml
.github/workflows/pages.yml
```

## 发布到 GitHub Pages

仓库 Settings → Pages → Source 选 **GitHub Actions**。之后每次 push `main` 即更新站点。
